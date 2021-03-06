[![](https://images.microbadger.com/badges/image/lalyos/k8s-workshop.svg)](https://microbadger.com/images/lalyos/k8s-workshop "Get your own image badge on microbadger.com")
[![Docker Automated build](https://img.shields.io/docker/automated/lalyos/k8s-workshop.svg)](https://hub.docker.com/r/lalyos/k8s-workshop/)

Base image for k8s workshops. The main idea is to provide an environment
where participants can `kubectl` right away, "no-installation-needed"

- each user has its own namespace
- each user works inside a prepared pod
- preinstalled tools:
  - kubectl,helm
  - curl,bash,bash-completion,unzip,git,dig,net-tools
  - tmux/vim/[micro](https://github.com/zyedidia/micro)
- sample git repo pre-pulled into $HOME
- KUBECONFIG set up to own namespaces with token and server ca.pem set

For instant `kubectl` access a browser based connectivity is provided. Under the hood bash is shared via a websocket, based on: [yudai/gotty](https://github.com/yudai/gotty).

See the [last section](#tldr) for explanation about different access methods, and why the browser based is preferred. Please note, that while browser based is poreferable for first-time users, additional ssh access can be easily provided. See [sshfront](https://github.com/lalyos/k8s-sshfront)

## Roadmap

- [ ] Add browser based editor. Possible [zed](https://chrome.google.com/webstore/detail/zed-code-editor/pfmjnmeipppmcebplngmhfkleiinphhp?hl=en)
- [ ] Record sessions (presenter/participant): ttyrec/ascicast
- [ ] Persistent storage on VPC

## Usage

First source all the functions:
```
. workshop-functions.sh
```

A ClusterRole and a binding is needed initially
```
init
```

Create a devenv for a participant:
```
$ dev user5
namespace/user5 created
serviceaccount/sa-user5 created
role.rbac.authorization.k8s.io/role-user5 created
rolebinding.rbac.authorization.k8s.io/rb-user5 created
clusterrolebinding.rbac.authorization.k8s.io/crb-user5 created
secret/user5 created
deployment.apps/user5 created
service/user5 created
```

Share the url for browser connection:
```
$ get-url user5
open http://35.199.111.25:31234/1g7j7u0l/
```

## Related work

- [Namespace Validaing Admission Webhook](https://github.com/lalyos/k8s-ns-admission)
- [ssh-front](https://github.com/lalyos/k8s-sshfront)
- [self-service](https://github.com/lalyos/gitter-scripter)
- [patched gotty](https://github.com/lalyos/gotty)

## Presenter

The presenter can share its terminal session in as *read-only* via browser. Participants can easily copy-paste commands.

Share the presenter url with participats:
```
$ presenter-url
```

Start the presenter session (user0):
```
$ presenter
```

## tl;dr

There are various ways for participants to work with the k8s cluster:

### Method-1: kubectl from **laptop**

  - **pros:**
    - own/custom editor
    - own/custom key-bindings
    - no "vi freak-out"
  - **cons:**
    - win/osx/linux differences
    - potential network/proxy/firewall issues
    - time consuming for first-time users (upto 30 minutes)

### Method-2: **ssh** into k8s nodes:

  - **pros:**
    - no-install-needed
    - simple concept
  - **cons:**
    - potential "vi freak-out"
    - turnkey solutions (gke/aks/eks) can use esoteric/readonly os
    - ssh password/key setup
    - potential network/proxy/firewall issues
    - hard to scale/assign (5 nodes / 20 participants)

### Method-3: **ssh** into non-k8s VMs:
  - **pros:**
    - no-install-needed
    - easier to scale
    - can use different node-type cpu/mem
    - preemptible/spot instances to save $
  - **cons:**
    - potential "vi freak-out"
    - CluserIp unreachable
    - potential network/proxy/firewall issues
    - lots of ssh accounts to manage
    - ansible or similar tool needed

### Method-4: **browser** connection to a container:
  - **pros:**
    - absolutely no install needed
    - easy to scale
    - instant access
    - no ssh password/key sharing needed
  - **cons:**
    - key-bindings can be confusing

Please note, that while browser based is poreferable for first-time users, adiitional ssh access can be easily provided. See [sshfront](https://github.com/lalyos/k8s-sshfront)