---
title: 在 Azure Kubernetes Service (AKS) 中使用 pod 安全策略
description: 了解如何使用 Azure Kubernetes 服务 (AKS) 中的 PodSecurityPolicy 控制 pod 招生
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: mlearned
ms.openlocfilehash: df8aa51558bc3aa456758510792c198a8bd9cf78
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061843"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>预览-在 Azure Kubernetes Service (AKS) 中使用 pod 安全策略保护群集

若要提高 AKS 群集的安全性, 可以限制可计划的 pod。 请求不允许的资源的 pod 无法在 AKS 群集中运行。 使用 pod 安全策略定义此访问权限。 本文介绍如何在 AKS 中使用 pod 安全策略来限制 pod 的部署。

> [!IMPORTANT]
> AKS 预览功能是可选的自助服务。 预览按 "原样" 提供, 并从服务级别协议和有限担保中排除。 AKS 预览版是以最大努力为基础的客户支持部分覆盖的。 因此, 这些功能并不用于生产。 有关其他信息, 请参阅以下支持文章:
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要使用 pod 安全策略, 需要*aks* CLI 扩展版本0.4.1 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展, 然后使用[az extension update][az-extension-update]命令检查是否有任何可用的更新:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>注册 pod 安全策略功能提供程序

若要创建或更新 AKS 群集以使用 pod 安全策略, 请先在订阅上启用功能标志。 若要注册*PodSecurityPolicyPreview*功能标志, 请使用[az feature register][az-feature-register]命令, 如以下示例中所示:

> [!CAUTION]
> 在订阅上注册功能时, 当前无法注册该功能。 启用某些预览功能后, 默认值可用于在订阅中创建的所有 AKS 群集。 不要对生产订阅启用预览功能。 使用单独的订阅来测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间。 您可以使用[az feature list][az-feature-list]命令检查注册状态:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

准备就绪后, 请使用[az provider register][az-provider-register]命令刷新*ContainerService*资源提供程序的注册:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod 安全策略概述

在 Kubernetes 群集中, 当要创建资源时, 会使用一个许可控制器来截获对 API 服务器的请求。 然后, 该许可控制器可以根据一组规则*验证*资源请求, 或改变资源来更改部署参数。

*PodSecurityPolicy*是一种许可控制器, 用于验证 pod 规范是否满足定义的要求。 这些要求可能会限制使用特权容器、访问某些类型的存储, 或者容器可以运行的用户或组。 如果尝试部署的资源的 pod 规范不满足 pod 安全策略中所述的要求, 则会拒绝该请求。 此功能可控制可在 AKS 群集中计划的 pod, 防止出现一些可能的安全漏洞或权限升级。

在 AKS 群集中启用 pod 安全策略时, 会应用某些默认策略。 这些默认策略提供现成的体验来定义可计划的 pod。 但是, 在你定义自己的策略之前, 群集用户可能会遇到部署 pod 的问题。 推荐的方法是:

* 创建 AKS 群集
* 定义自己的 pod 安全策略
* 启用 pod 安全策略功能

若要显示默认策略限制 pod 部署的方式, 在本文中, 我们首先启用 pod 安全策略功能, 然后创建自定义策略。

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>在 AKS 群集上启用 pod 安全策略

可以使用[az aks update][az-aks-update]命令启用或禁用 pod 安全策略。 以下示例在名为*myResourceGroup*的资源组中的群集名称*myAKSCluster*上启用 pod 安全策略。

> [!NOTE]
> 对于实际使用, 在定义自己的自定义策略之前, 不要启用 pod 安全策略。 本文介绍如何将 pod 安全策略启用为第一步, 以查看默认策略限制 pod 部署的方式。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>默认 AKS 策略

启用 pod 安全策略时, AKS 将创建两个名为 "*特权*" 和 "*受限制*" 的默认策略。 请勿编辑或删除这些默认策略。 应创建自己的策略来定义要控制的设置。 首先, 让我们看一下这些默认策略是如何影响 pod 部署的。

若要查看可用的策略, 请使用[kubectl get psp][kubectl-get]命令, 如以下示例中所示。 作为默认*限制*策略的一部分, 用户被拒绝特权箱升级的特权, 用户*MustRunAsNonRoot*。

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*受限*箱安全策略将应用到 AKS 群集中任何经过身份验证的用户。 此分配由 ClusterRoles 和 ClusterRoleBindings 控制。 使用[kubectl get clusterrolebindings][kubectl-get]命令并搜索*默认值: 受限:* binding:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

如以下简洁输出所示, *psp: 受限*的 ClusterRole 分配给任何*系统: 经过身份验证*的用户。 此功能可以提供基本级别的限制, 而不会定义你自己的策略。

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

在开始创建自己的 pod 安全策略之前, 请务必了解这些默认策略如何与用户请求进行交互来计划 pod。 在接下来的几个部分中, 我们将安排一些箱来查看这些默认策略的运行情况。

## <a name="create-a-test-user-in-an-aks-cluster"></a>在 AKS 群集中创建测试用户

默认情况下, 当你使用[az aks get 凭据][az-aks-get-credentials]命令时, aks 群集的*管理员*凭据将添加到你`kubectl`的配置中。管理员用户绕过 pod 安全策略的实施。 如果为 AKS 群集使用 Azure Active Directory 集成, 则可以使用非管理员用户的凭据登录, 以查看操作中的执行策略。 在本文中, 我们将在 AKS 群集中创建一个可使用的测试用户帐户。

使用[kubectl create namespace][kubectl-create]命令为测试资源创建名为*aks*的示例命名空间。 然后, 使用[kubectl create serviceaccount][kubectl-create]命令创建名为*nonadmin 的*服务帐户:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

接下来, 创建 RoleBinding, 让*nonadmin-user*使用[kubectl create RoleBinding][kubectl-create]命令执行命名空间中的基本操作:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>为管理员和非管理员用户创建别名命令

若要突出显示使用`kubectl`和在前面步骤中创建的非管理员用户时常规管理员用户之间的差异, 请创建两个命令行别名:

* **Kubectl**别名适用于常规管理员用户, 其作用域为*psp-aks*命名空间。
* **Kubectl-nonadminuser**别名适用于在上一步中创建的*nonadmin 用户*, 其作用域为*psp-aks*命名空间。

创建这两个别名, 如以下命令所示:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>测试特权 pod 的创建

首先, 请测试在使用的安全上下文来`privileged: true`计划 pod 时所发生的情况。 此安全上下文将升级 pod 的权限。 在上一部分中显示了默认 AKS pod 安全策略的情况下,*受限制*的策略应拒绝此请求。

创建名为 `nginx-privileged.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

使用[kubectl apply][kubectl-apply]命令创建 pod, 并指定 YAML 清单的名称:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

无法计划 pod, 如以下示例输出所示:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Pod 不会到达计划阶段, 因此在继续操作之前, 没有要删除的资源。

## <a name="test-creation-of-an-unprivileged-pod"></a>测试非特权 pod 的创建

在上面的示例中, pod 规范请求了特权升级。 此请求被默认*限制*pod 安全策略拒绝, 因此无法计划 pod。 现在, 让我们尝试在没有权限提升请求的情况下运行相同的 NGINX pod。

创建名为 `nginx-unprivileged.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

使用[kubectl apply][kubectl-apply]命令创建 pod, 并指定 YAML 清单的名称:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Kubernetes 计划程序接受 pod 请求。 但是, 如果使用`kubectl get pods`查看 pod 的状态, 则会出现错误:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

使用[kubectl 说明 pod][kubectl-describe]命令查看 pod 的事件。 下面的压缩示例显示了容器和映像需要 root 权限, 即使我们未请求它们:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

尽管我们没有请求任何特权访问, 但 NGINX 的容器映像需要为端口*80*创建绑定。 若要绑定端口*1024*和更低的端口, 需要*root*用户。 当 pod 尝试启动时,*受限制*的 pod 安全策略将拒绝此请求。

此示例显示 AKS 创建的默认 pod 安全策略有效, 并限制用户可执行的操作。 了解这些默认策略的行为很重要, 因为您可能不希望拒绝基本的 NGINX pod。

在继续下一步之前, 请使用[kubectl delete pod][kubectl-delete]命令删除此测试盒:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>测试使用特定用户上下文创建 pod

在上面的示例中, 容器映像自动尝试使用根将 NGINX 绑定到端口80。 此请求已被默认*限制*pod 安全策略拒绝, 因此无法启动 pod。 现在, `runAsUser: 2000`让我们尝试使用特定的用户上下文 (如) 运行相同的 NGINX pod。

创建名为 `nginx-unprivileged-nonroot.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

使用[kubectl apply][kubectl-apply]命令创建 pod, 并指定 YAML 清单的名称:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Kubernetes 计划程序接受 pod 请求。 但是, 如果使用`kubectl get pods`查看 pod 的状态, 则会出现与前面的示例不同的错误:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

使用[kubectl 说明 pod][kubectl-describe]命令查看 pod 的事件。 下面的紧缩示例显示了 pod 事件:

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

事件指示已创建并启动了容器。 这并没有什么直接明显的原因是 pod 处于失败状态。 让我们使用[kubectl logs][kubectl-logs]命令查看 pod 日志:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

下面的示例日志输出提供了一个在 NGINX 配置本身中的指示, 当服务尝试启动时, 会出现权限错误。 如果需要绑定到端口 80, 则会再次引发此错误。 尽管 pod 规范定义了普通的用户帐户, 但此用户帐户在 OS 级别中不足以使 NGINX 服务启动并绑定到受限端口。

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

同样, 请务必了解默认 pod 安全策略的行为。 此错误很难跟踪, 同样, 你可能不会希望拒绝基本的 NGINX pod。

在继续下一步之前, 请使用[kubectl delete pod][kubectl-delete]命令删除此测试盒:

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>创建自定义 pod 安全策略

现在, 你已了解默认 pod 安全策略的行为, 接下来让我们为*nonadmin-用户*提供一种成功计划 pod 的方式。

让我们创建一个策略, 用于拒绝请求特权访问的 pod。 其他选项 (例如*runAsUser*或允许使用的*卷*) 未显式限制。 这种类型的策略拒绝请求特权访问, 但允许群集运行请求的 pod。

创建名为 `psp-deny-privileged.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

使用[kubectl apply][kubectl-apply]命令创建策略, 并指定 YAML 清单的名称:

```console
kubectl apply -f psp-deny-privileged.yaml
```

若要查看可用的策略, 请使用[kubectl get psp][kubectl-get]命令, 如以下示例中所示。 将*psp-拒绝特权*策略与在前面的示例中强制执行的默认*限制*策略进行比较, 以创建 pod。 策略仅拒绝使用*特权*升级。 对于 " *psp 拒绝特权*" 策略, 用户或组没有任何限制。

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>允许用户帐户使用自定义 pod 安全策略

在上一步中, 已创建了一个用于拒绝请求特权访问的 pod 的 pod 安全策略。 若要允许使用策略, 请创建*角色*或*ClusterRole*。 然后, 使用*RoleBinding*或*ClusterRoleBinding*关联其中一个角色。

在此示例中, 创建一个允许*使用*上一步中创建的 " *psp-拒绝-特权*" 策略的 ClusterRole。 创建名为 `psp-deny-privileged-clusterrole.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

使用[kubectl apply][kubectl-apply]命令创建 ClusterRole, 并指定 YAML 清单的名称:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

现在, 创建 ClusterRoleBinding 以使用上一步中创建的 ClusterRole。 创建名为 `psp-deny-privileged-clusterrolebinding.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

使用[kubectl apply][kubectl-apply]命令创建 ClusterRoleBinding, 并指定 YAML 清单的名称:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 在本文的第一步中, 已在 AKS 群集上启用 pod 安全策略功能。 建议的做法是, 在定义自己的策略后, 只启用 pod 安全策略功能。 这是启用 pod 安全策略功能的阶段。 已经定义了一个或多个自定义策略, 并且用户帐户已与这些策略相关联。 现在, 你可以安全地启用 pod 安全策略功能, 并最大限度地减少了默认策略导致的问题。

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>再次测试创建无特权的 pod

应用你的自定义 pod 安全策略, 并使用该用户帐户的绑定来使用该策略时, 让我们再次尝试创建无特权的 pod。 使用同一个`nginx-privileged.yaml`清单, 使用[kubectl apply][kubectl-apply]命令创建 pod:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

已成功计划 pod。 使用[kubectl get][kubectl-get] pod 命令检查 pod 的状态时, Pod 正在*运行*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

此示例演示如何创建自定义 pod 安全策略, 以定义针对不同用户或组的 AKS 群集的访问权限。 默认的 AKS 策略可对 pod 运行的内容提供严格的控制, 因此, 请创建你自己的自定义策略, 然后正确定义所需的限制。

使用[kubectl delete][kubectl-delete]命令删除 NGINX 无特权 pod, 并指定 YAML 清单的名称:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>清理资源

若要禁用 pod 安全策略, 请再次使用[az aks update][az-aks-update]命令。 以下示例在名为*myResourceGroup*的资源组中的群集名称*myAKSCluster*上禁用 pod 安全策略:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

接下来, 删除 ClusterRole 和 ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

使用[kubectl delete][kubectl-delete]命令删除网络策略, 并指定 YAML 清单的名称:

```console
kubectl delete -f psp-deny-privileged.yaml
```

最后, 删除*psp-aks*命名空间:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建 pod 安全策略来防止使用特权访问。 策略可以强制执行许多功能, 如卷或 RunAs 用户的类型。 有关可用选项的详细信息, 请参阅[Kubernetes pod 安全策略参考文档][kubernetes-policy-reference]。

有关限制 pod 网络流量的详细信息, 请参阅[使用 AKS 中的网络策略在 pod 之间进行安全通信][network-policies]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
