# CKA Training Notes Scope

These notes where created by David Sargent when I went through the Udemy course [Certified Kubernetes Administrator (CKA) with Practice Tests](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests) by Mumshad Mannambeth.

These notes are not a substitute for taking the above course, although it may look like I took fairly detailed notes there are several sections, espeically early on, that are sparse or non-existent as I knew that materail really well already.

If you see mistakes or would like to clean things up, feel free to do so and create a PR.

# Cliff Notes

The [Cliff Notes.md](./Cliff%20Notes.md) file located at the root of this repo contains my condensed notes of items I needed to memorize for the test so it probably does not contain everything you need to learn.

# Table of Contents

## Core References
- [Cliff Notes](./Cliff%20Notes.md)
- [Command Memorization](./Command%20Memorization.md)

## 01. Introduction
- [Introduction](./01%20Intro/Intro.md)

## 02. Core Concepts
- [Certification Tips - Imperative Commands](./02%20Core%20Concepts/certification-tips-imperative-commands.md)
- [etcd Commands](./02%20Core%20Concepts/etcd-commands.md)

## 03. Scheduling
- [Labels and Selectors](./03%20Scheduling/Labels%20and%20Selectors.md)
- [Requests and Limits](./03%20Scheduling/Requests%20and%20Limits.md)
- [Editing Pods and Deployments](./03%20Scheduling/Editing%20Pods%20and%20Deployments.md)
- [Static Pods](./03%20Scheduling/Static%20Pods.md)
- [References](./03%20Scheduling/References.md)

## 05. Application Lifecycle Management
- [Configuration](./05%20Application%20Lifecycle%20Management/Configuration.md)
- [Configmaps](./05%20Application%20Lifecycle%20Management/Configmaps.md)
- [Secrets](./05%20Application%20Lifecycle%20Management/Secrets.md)
- [Init Containers](./05%20Application%20Lifecycle%20Management/Init%20Containers.md)
- [Rollout](./05%20Application%20Lifecycle%20Management/Rollout.md)

## 06. Cluster Maintenance
- [Cluster Maintenance](./06%20Cluster%20Maintenance/Cluster%20Maintenance.md)
- [Releases](./06%20Cluster%20Maintenance/Releases.md)
- [Backup and Restore](./06%20Cluster%20Maintenance/Backup%20and%20Restore.md)
- [Upgrading Kubernetes](./06%20Cluster%20Maintenance/Upgrading%20Kubernetes.md)
- [Working with etcdctl](./06%20Cluster%20Maintenance/Working%20with%20etcdctl.md)

## 07. Security
- [Security Primitives](./07%20Security/01%20Security%20Primitives.md)
- [Authentication](./07%20Security/02%20Authentication.md)
- [TLS Certificates for Cluster Components](./07%20Security/03%20TLS%20Certificates%20for%20Cluster%20Components.md)
- [API Groups](./07%20Security/04%20API%20Groups.md)
- [Authorization](./07%20Security/05%20Authorization.md)
- [Image Security](./07%20Security/06%20Image%20Security.md)
- [Security Contexts](./07%20Security/07%20Security%20Contexts.md)
- [Network Policies](./07%20Security/08%20Network%20Policies.md)

## 08. Storage
- [Docker Storage](./08%20Storage/01%20Docker%20Storage.md)
- [Container Storage Interface](./08%20Storage/02%20Container%20Storage%20Interface%20%20.md)
- [Persistent Volumes](./08%20Storage/03%20Persistent%20Volumes.md)
- [Persistent Volume Claims](./08%20Storage/04%20Persistent%20Volume%20Claims.md)
- [Storage Classes](./08%20Storage/05%20Storage%20Classes.md)

## 09. Networking
- [Prerequisite - Networking](./09%20Networking/01%20Prerequisite%20-%20Networking.md)
- [Prerequisite - Core DNS](./09%20Networking/02%20Prerequisite%20-%20Core%20DNS.md)
- [Prerequisite - Network Namespaces](./09%20Networking/03%20Prerequisite%20-%20Network%20Namespaces.md)
- [Prerequisite - Docker Networking](./09%20Networking/04%20Prerequisite%20-%20Docker%20Networking.md)
- [Prerequisite - CNI](./09%20Networking/05%20Prerequisite%20-%20CNI.md)
- [Networking Cluster Nodes](./09%20Networking/06%20Networking%20Cluster%20Nodes.md)
- [Pod Networking](./09%20Networking/07%20Pod%20Networking.md)
- [CNI in Kubernetes](./09%20Networking/08%20CNI%20in%20Kubernetes.md)
- [Weaveworks CNI Plugin](./09%20Networking/09%20Weaveworks%20CNI%20Plugin.md)
- [IP Address Management](./09%20Networking/10%20IP%20Address%20Management.md)
- [Service Networking](./09%20Networking/11%20Service%20Networking.md)
- [Cluster DNS](./09%20Networking/12%20Cluster%20DNS.md)
- [Ingress](./09%20Networking/13%20Ingress.md)

## 10. Design and Install a Kubernetes Cluster
- [HA Masters](./10%20Design%20and%20Install%20a%20Kubernetes%20Cluster/01%20HA%20Masters.md)
- [Installing Kubernetes the Hard Way](./10%20Design%20and%20Install%20a%20Kubernetes%20Cluster/02%20Installing%20Kubernetes%20the%20Hard%20Way.md)

## 11. Kubeadm
- [Kubeadm](./11%20Kubeadm/01%20Kubeadm.md)

## 12. End To End Testing
- [End to End Testing](./12%20End%20To%20End%20Testing/End%20to%20End%20Testing.md)

## 13. Troubleshooting
- [Application Failures](./13%20Troubleshooting/01%20Application%20Failures.md)
- [Control Plane Failures](./13%20Troubleshooting/02%20Control%20Place%20Failurs.md)
- [Worker Node Failures](./13%20Troubleshooting/03%20Worker%20Node%20Failures.md)
- [Network Troubleshooting](./13%20Troubleshooting/04%20Network%20Troubleshooting.md)

## 14. Other Topics
- [JSON Path](./14%20Other%20Topics/01%20JSON%20Path.md)