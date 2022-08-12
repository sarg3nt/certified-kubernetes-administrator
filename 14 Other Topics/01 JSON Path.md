# JSON Path

## Training

In the upcoming lecture we will explore some advanced commands with kubectl utility. But that requires JSON PATH. If you are new to JSON PATH queries get introduced to it first by going through the lectures and practice tests available here.

https://kodekloud.com/p/json-path-quiz

I also have some JSON PATH exercises with Kubernetes Data Objects. Make sure you go through these:  
https://mmumshad.github.io/json-path-quiz/index.html#!/?questions=questionskub1  
https://mmumshad.github.io/json-path-quiz/index.html#!/?questions=questionskub2

## Examples

- The root element of any JSON is always a dictionary that is references with `$`
- dictionary selectors start with a dot `.`
- Arrays / Lists are denoted with `[]`
- To get a specific item in an array use its index, starting at zero, i.e. `[0]`
- Dictionary then array does not have a dot `.`.  Example: `.spec.containers[0]`
- jsonpath always returns data in an array, i.e. `["foo"]`
- Queries are denoted by `?(  )`, they can be thought of as "Check if"
  - Each item in a list is denoted as `@`
  - To find all the numbers from an array that are great than 40  
  `$[?(@ > 40)]`
  - Other syntax  
    - `@ == 40`
    - `@ != 40`
    - `@ in [40, 43, 45]`
    - `@ nin [30, 32] `
- Wildcard `*` is used as a wildcard operator to select all of a set of dictionary items or list

Question #2  k get node -o jsonpath='{.items[*].status.addresses[*]}'

Example JSON Data
```json
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "nginx-pod",
    "namespace": "default"
  },
  "metadata2": {
    "name": "example-for-learning",
    "namespace": "ignore"
  },
  "spec": {
    "containers": [
      {
        "image": "nginx:alpine",
        "name": "nginx"
      },
      {
        "image": "ubuntu:20.04",
        "name": "ubuntu"
      },
    ],
    "nodeName": "node01"
  }
}
```

Queries and Results
```bash
# Get the "kind"
$.kind 
# ["Pod"]

# Get the image of the first container
$.spec.containers[0].image 
# ["nginx:alpine"]

# Get the image with the name of ubuntu
$.spec.containers[?(@.name == "ubuntu")].image
# ["ubuntu:20.04"]

# Get all of the namespaces
$.*.namespace
# ["default","ignore"]

# Get all of the names of the containers
$.containers[*].name
# ["nginx","ubuntu"]
```

Other examples

```json
{
  "property1": "value1",
  "property2": "value2"
}
```

Queries and Results
```bash
# Get the values only
$.*
# ["value1","value2"]

```

## Lists / Arrays

- In lists you can select more than one item one at a time with commas `,`:  Example `[0,3]` would select the first and fourth item.
- In lists you can select a range of items with `:`  Example: `[0:3]` would select items 1,2,3 but not item 4 as it stops at the fourth item but does not select it.
- In lists to select every other item use the step operator `:` as the second parameter.  Example: `[0:8:2]` would select every other item up to but not including the 8th.
- In lists, to select the last item in the list use the reverse index `[-1]`, to select the third from last `[-3]`  Note: Not all implementations support this format and you must use `[-1:0]` or just `[-1:]`

## Using with Kubectl

### Basic Usage

1. Figure out what data you need with `k get <thing> -o json` to see the output in json and build your jsonpath
1. Add the jsonpath to the query:  
`k get <thin> -o jsongpath={'<query>'}`  
Example `k get nodes -o jsonpath={'.items[0].metadata.name'}`  
1. Query multiple items at once:  
`k get nodes -o=jsonpath='{.items[*].metadata.name}{.items[*].status.capacity.cpu}'`
2. Add a return between items with `{"\n"}`  
`k get nodes -o=jsonpath='{.items[*].metadata.name}{"\n"}{.items[*].status.capacity.cpu}'`  
Output of above might look like this  
`lpul-k8sprodmst1 lpul-k8sprodmst2 lpul-k8sprodmst3`   
`4 4 4`

### For Loops

Scenario: We want to output a custom view of node names and CPU but instead of outputting a row of node names and a row of CPU we want them in columns like this:
```bash
lpul-k8sprodmst1    4
lpul-k8sprodmst2    4
lpul-k8sprodmst3    4
```
To accomplish this we'd use loops.

We want a loop something like this

```javascript
FOR EACH NODE
   PRINT NODE NAME \t PRINT CPU COUNT \n
END FOR
```

We do this with the jsonpath `range` operator

```javascript
{range .items[*]}
   {.metadata.name} {"\t"} {.status.capacity.cpu} {"\n"}
{end}
```

Put the above into a single line in our kubectl query and we get this

```bash
k get nodes -o jsonpath='{range .items[*]}{.metadata.name} {"\t"} {.status.capacity.cpu} {"\n"}{end}'

# Output:
lpul-k8sprodmst1         4
lpul-k8sprodmst2         4
lpul-k8sprodmst3         4
lpul-k8sprodwrk-fad1     16
lpul-k8sprodwrk-fad2     16
lpul-k8sprodwrk-i1       4
lpul-k8sprodwrk-i2       4
lpul-k8sprodwrk01        16
lpul-k8sprodwrk02        16
lpul-k8sprodwrk03        16
lpul-k8sprodwrk04        16
lpul-k8sprodwrk05        16
lpul-k8sprodwrk06        16
lpul-k8sprodwrk07        16
lpul-k8sprodwrk08        16
```

Another options is to use the `kubectl` `custom-columns` feature.  
The above output can be achieved with this query:  
Note that we do not need `.items[*]` as the custom-columns assumes we want the data in the primary array.

```bash
k get nodes -o custom-columns=NOE:.metadata.name,CPU:.status.capacity.cpu

# Output
NOE                    CPU
lpul-k8sprodmst1       4
lpul-k8sprodmst2       4
lpul-k8sprodmst3       4
lpul-k8sprodwrk-fad1   16
lpul-k8sprodwrk-fad2   16
lpul-k8sprodwrk-i1     4
lpul-k8sprodwrk-i2     4
lpul-k8sprodwrk01      16
lpul-k8sprodwrk02      16
lpul-k8sprodwrk03      16
lpul-k8sprodwrk04      16
lpul-k8sprodwrk05      16
lpul-k8sprodwrk06      16
lpul-k8sprodwrk07      16
lpul-k8sprodwrk08      16
```

Json Paths can also be used to sort `kubectl` output by using the `--sort-by` option.

```bash
# Sort by node name
k get nodes --sort-by=.metadata.name

# Sort by number of CPUs
k get nodes --sort-by=.status.capacity.cpu

# Combining both custom-columns and sort-by
k get nodes -o custom-columns=NOE:.metadata.name,CPU:.status.capacity.cpu --sort-by=.status.capacity.cpu

# Output
NOE                    CPU
lpul-k8sprodwrk-i1     4
lpul-k8sprodmst2       4
lpul-k8sprodmst3       4
lpul-k8sprodmst1       4
lpul-k8sprodwrk-i2     4
lpul-k8sprodwrk01      16
lpul-k8sprodwrk-fad2   16
lpul-k8sprodwrk-fad1   16
lpul-k8sprodwrk02      16
lpul-k8sprodwrk03      16
lpul-k8sprodwrk04      16
lpul-k8sprodwrk05      16
lpul-k8sprodwrk06      16
lpul-k8sprodwrk07      16
lpul-k8sprodwrk08      16

```