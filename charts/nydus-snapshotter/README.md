# Nydus-snapshotter Helm Chart

[![Artifact Hub](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/dragonfly)](https://artifacthub.io/packages/search?repo=dragonfly)

## TL;DR

```shell
helm repo add dragonfly https://dragonflyoss.github.io/helm-charts/
helm install --create-namespace --namespace nydus-snapshotter nydus-snapshotter dragonfly/nydus-snapshotter
```

## Introduction

Nydus snapshotter is an external plugin of containerd for [Nydus image service](https://nydus.dev) which implements a chunk-based content-addressable filesystem on top of a called `RAFS (Registry Acceleration File System)` format that improves the current OCI image specification, in terms of container launching speed, image space, and network bandwidth efficiency, as well as data integrity with several runtime backends: FUSE, virtiofs and in-kernel [EROFS](https://www.kernel.org/doc/html/latest/filesystems/erofs.html).

Nydus supports lazy pulling feature since pulling image is one of the time-consuming steps in the container lifecycle. Lazy pulling here means a container can run even the image is partially available and necessary chunks of the image are fetched on-demand. Apart from that, Nydus also supports [(e)Stargz](https://github.com/containerd/stargz-snapshotter) lazy pulling directly **WITHOUT** any explicit conversion.

For more details about how to build Nydus container image, please refer to [nydusify](https://github.com/dragonflyoss/image-service/blob/master/docs/nydusify.md) conversion tool and [acceld](https://github.com/goharbor/acceleration-service).

## Prerequisites

- Kubernetes cluster 1.20+
- Helm v3.8.0+

## Installation Guide

For more detail about installation is available in [nydus-snapshotter repo](https://github.com/containerd/nydus-snapshotter).

## Installation

### Install with custom configuration

Create the `values.yaml` configuration file.

```yaml
nydusSnapshotter:
  name: nydus-snapshotter
  image: ghcr.io/containerd/nydus-snapshotter
  tag: v1.2.11
```
Install nydus-snapshotter chart with release name `nydus-snapshotter`:

```shell
helm repo add dragonfly https://dragonflyoss.github.io/helm-charts/
helm install --create-namespace --namespace nydus-snapshotter nydus-snapshotter dragonfly/nydus-snapshotter -f values.yaml
```

## Uninstall

Uninstall the `nydus-snapshotter` daemonset:

```shell
helm delete nydus-snapshotter --namespace nydus-snapshotter
```

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| args | list | `[]` | Args to overwrite default nydus-snapshotter startup command |
| containerRuntime | object | `{"containerd":{"configFile":"/etc/containerd/config.toml","enable":true},"initContainerImage":"ghcr.io/liubin/toml-cli:v0.0.7"}` | [Experimental] Container runtime support Choose special container runtime in Kubernetes. Support: Containerd, Docker, CRI-O |
| containerRuntime.containerd | object | `{"configFile":"/etc/containerd/config.toml","enable":true}` | [Experimental] Containerd support |
| containerRuntime.containerd.configFile | string | `"/etc/containerd/config.toml"` | Custom config path directory, default is /etc/containerd/config.toml |
| containerRuntime.containerd.enable | bool | `true` | Enable containerd support Inject nydus-snapshotter config into ${containerRuntime.containerd.configFile}, |
| containerRuntime.initContainerImage | string | `"ghcr.io/liubin/toml-cli:v0.0.7"` | The image name of init container, just to update container runtime configuration file |
| daemonsetAnnotations | object | `{}` | Daemonset annotations |
| dragonfly.enable | bool | `true` | Enable dragonfly |
| dragonfly.mirrorConfig[0].auth_through | bool | `false` |  |
| dragonfly.mirrorConfig[0].headers.X-Dragonfly-Registry | string | `"https://index.docker.io"` |  |
| dragonfly.mirrorConfig[0].host | string | `"http://127.0.0.1:65001"` |  |
| dragonfly.mirrorConfig[0].ping_url | string | `"http://127.0.0.1:40901/server/ping"` |  |
| hostAliases | list | `[]` | Host Aliases |
| hostNetwork | bool | `true` | Let nydus-snapshotter run in host network |
| image | string | `"ghcr.io/containerd/nydus-snapshotter"` | Image repository |
| name | string | `"nydus-snapshotter"` | nydus-snapshotter name |
| nodeSelector | object | `{}` | Node labels for pod assignment |
| podAnnotations | object | `{}` | Pod annotations |
| podLabels | object | `{}` | Pod labels |
| priorityClassName | string | `""` | Pod priorityClassName |
| pullPolicy | string | `"Always"` | Image pull policy |
| resources | object | `{"limits":{"cpu":"2","memory":"2Gi"},"requests":{"cpu":"0","memory":"0"}}` | Pod resource requests and limits |
| tag | string | `"v0.9.0"` | Image tag |
| terminationGracePeriodSeconds | string | `nil` | Pod terminationGracePeriodSeconds |
| tolerations | list | `[]` | List of node taints to tolerate |

## Chart dependencies

| Repository | Name | Version |
|------------|------|---------|
