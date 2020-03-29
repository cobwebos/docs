---
title: 在 Azure 库伯奈斯服务 （AKS） 中使用窗格安全策略
description: 了解如何在 Azure 库伯奈斯服务 （AKS） 中使用 PodSecurity 策略来控制 pod 准入
services: container-service
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 74177136a7a61186ab1d273b57dbfce550a18ecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914528"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>预览 - 使用 Azure 库伯奈斯服务 （AKS） 中的窗格安全策略保护群集

为了提高 AKS 群集的安全性，可以限制可以计划哪些窗格。 请求不允许的资源的 Pod 无法在 AKS 群集中运行。 您可以使用 pod 安全策略定义此访问。 本文介绍如何使用 pod 安全策略来限制在 AKS 中部署 pod。

> [!IMPORTANT]
> AKS 预览功能是自助式选择加入功能。 预览版“按原样”提供，并且仅在“可用情况下”提供，不包含在服务级别协议和有限保障中。 AKS 预览版的内容部分包含在客户支持中，我们只能尽力提供支持。 因此，这些功能不应用于生产。 有关详细信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

要使用 pod 安全策略，您需要*aks 预览*CLI 扩展版本 0.4.1 或更高版本。 使用 [az extension add][az-extension-add] 命令安装 *aks-preview* Azure CLI 扩展，然后使用 [az extension update][az-extension-update] 命令检查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>注册窗格安全策略功能提供程序

要创建或更新 AKS 群集以使用 pod 安全策略，请先在订阅上启用功能标志。 要注册*PodSecurityPolicy 预览*功能标志，请使用[az 要素寄存器][az-feature-register]命令，如以下示例所示：

> [!CAUTION]
> 在订阅上注册功能时，当前无法取消注册该功能。 启用某些预览功能后，可能会将默认值用于订阅中创建的所有 AKS 群集。 不要在生产订阅上启用预览功能。 使用单独的订阅测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间**。 可以使用 [az feature list][az-feature-list] 命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态**：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>pod 安全策略概述

在 Kubernetes 群集中，在创建资源时，允许控制器用于拦截对 API 服务器的请求。 然后，准入控制器可以根据一组规则*验证*资源请求，或*更改*资源以更改部署参数。

*PodSecurityPolicy*是一个允许控制器，用于验证 Pod 规范是否满足您定义的要求。 这些要求可能会限制特权容器的使用、对某些类型的存储的访问，或者容器可以按该容器运行的用户或组。 当您尝试部署 pod 规范不符合 pod 安全策略中概述的要求的资源时，请求将被拒绝。 这种控制 AKS 群集中可以安排哪些 pod 的能力可防止某些可能的安全漏洞或权限升级。

在 AKS 群集中启用 pod 安全策略时，将应用一些默认策略。 这些默认策略提供了现成的体验，用于定义可以计划哪些窗格。 但是，群集用户在定义自己的策略之前，在部署 pod 时可能会遇到问题。 建议的方法是：

* 创建 AKS 群集
* 定义您自己的 pod 安全策略
* 启用吊舱安全策略功能

为了显示默认策略如何限制 pod 部署，在本文中，我们首先启用 pod 安全策略功能，然后创建自定义策略。

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>在 AKS 群集上启用 pod 安全策略

您可以使用[az aks 更新][az-aks-update]命令启用或禁用 pod 安全策略。 下面的示例在名为*myResourceGroup*的资源组中的群集名称*myAKSCluster*上启用 pod 安全策略。

> [!NOTE]
> 对于实际使用，在定义自己的自定义策略之前，不要启用 pod 安全策略。 在本文中，您将启用 pod 安全策略作为查看默认策略如何限制 pod 部署的第一步。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>默认 AKS 策略

启用 pod 安全策略时，AKS 将创建一个名为*特权 的*默认策略。 不要编辑或删除默认策略。 相反，请创建自己的策略来定义要控制的设置。 让我们先看看这些默认策略如何影响 pod 部署。

要查看可用的策略，请使用[kubectl get psp][kubectl-get]命令，如以下示例所示

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*特权*pod 安全策略应用于 AKS 群集中的任何经过身份验证的用户。 此分配由群集角色和群集角色绑定控制。 使用[kubectl 获取群集角色绑定][kubectl-get]命令并搜索*默认：特权：* 绑定：

```console
kubectl get clusterrolebindings default:privileged -o yaml
```

如下压缩输出所示 *，psp：受限*群集角色分配给任何*系统：经过身份验证*的用户。 此功能提供基本级别的限制，而无需定义您自己的策略。

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

在开始创建自己的 pod 安全策略之前，了解这些默认策略如何与用户请求交互以计划 pod 非常重要。 在接下来的几节中，让我们安排一些窗格，以查看这些默认策略的运行情况。

## <a name="create-a-test-user-in-an-aks-cluster"></a>在 AKS 群集中创建测试用户

默认情况下，当您使用 az [aks 获取凭据][az-aks-get-credentials]命令时，AKS 群集的*管理员*凭据将添加到配置`kubectl`中。管理员用户绕过了 pod 安全策略的强制。 如果对 AKS 群集使用 Azure 活动目录集成，则可以使用非管理员用户的凭据登录以查看策略的实施情况。 在本文中，让我们在 AKS 群集中创建一个可以使用的测试用户帐户。

使用[kubectl 创建命名空间][kubectl-create]命令为测试资源创建名为*psp-aks*的示例命名空间。 然后，使用[kubectl 创建服务帐户][kubectl-create]命令创建名为*非管理员用户的*服务帐户：

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

接下来，为*非管理员用户*创建角色绑定，以便使用[kubectl 创建角色绑定][kubectl-create]命令在命名空间中执行基本操作：

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>为管理员和非管理员用户创建别名命令

要突出显示使用`kubectl`时的常规管理员用户与在前面的步骤中创建的非管理员用户之间的差异，请创建两个命令行别名：

* **kubectl-admin**别名适用于常规管理员用户，并限定为*psp-aks*命名空间。
* **kubectl-非管理员用户**别名适用于在上一步骤中创建*的非管理员用户*，并限定为*psp-aks*命名空间。

创建以下两个别名，如以下命令所示：

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>测试创建特权窗格

让我们首先测试在计划具有 的安全上下文的`privileged: true`窗格时会发生什么情况。 此安全上下文会升级窗格的权限。 在上一节显示默认 AKS pod 安全策略时，*受限*策略应拒绝此请求。

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

使用[kubectl 应用][kubectl-apply]命令创建 pod 并指定 YAML 清单的名称：

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

无法计划 Pod，如以下示例输出所示：

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

pod 未达到计划阶段，因此在继续之前没有要删除的资源。

## <a name="test-creation-of-an-unprivileged-pod"></a>测试创建无特权 pod

在前面的示例中，pod 规范请求特权升级。 默认*受限*pod 安全策略拒绝此请求，因此无法计划该窗格。 现在，让我们尝试运行同一 NGINX 窗格，而无需权限升级请求。

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

使用[kubectl 应用][kubectl-apply]命令创建 pod 并指定 YAML 清单的名称：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

库伯内斯调度程序接受 pod 请求。 但是，如果您查看使用`kubectl get pods`的 Pod 的状态，则存在错误：

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

使用[kubectl 描述窗格][kubectl-describe]命令查看窗格的事件。 下面的压缩示例显示容器和映像需要根权限，即使我们没有请求它们：

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

即使我们没有请求任何特权访问，NGINX 的容器映像也需要为端口*80*创建绑定。 要绑定端口*1024*及以下，需要*根*用户。 当 pod 尝试启动时，*受限*的 pod 安全策略会拒绝此请求。

此示例显示 AKS 创建的默认 pod 安全策略有效，并限制用户可以执行的操作。 了解这些默认策略的行为非常重要，因为您可能不希望拒绝基本的 NGINX pod。

在继续执行下一步之前，请使用[kubectl 删除窗格][kubectl-delete]命令删除此测试窗格：

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>测试使用特定用户上下文的 pod 的创建

在前面的示例中，容器映像自动尝试使用 root 将 NGINX 绑定到端口 80。 默认*受限*pod 安全策略拒绝了此请求，因此该窗格无法启动。 现在，让我们尝试使用特定的用户上下文（如`runAsUser: 2000`）运行同一 NGINX 窗格。

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

使用[kubectl 应用][kubectl-apply]命令创建 pod 并指定 YAML 清单的名称：

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

库伯内斯调度程序接受 pod 请求。 但是，如果您查看使用`kubectl get pods`的 Pod 的状态，则存在与前面的示例不同的错误：

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

使用[kubectl 描述窗格][kubectl-describe]命令查看窗格的事件。 以下压缩示例显示了 pod 事件：

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

这些事件表示容器已创建并启动。 对于为什么该窗格处于失败状态，目前还没有什么明显之处。 让我们使用[kubectl 日志][kubectl-logs]命令查看 pod 日志：

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

以下示例日志输出指示在 NGINX 配置本身中，当服务尝试启动时存在权限错误。 此错误再次由需要绑定到端口 80 引起。 尽管 pod 规范定义了常规用户帐户，但在 OS 级别中，此用户帐户不足以使 NGINX 服务启动并绑定到受限端口。

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

同样，了解默认 pod 安全策略的行为也很重要。 此错误有点难以跟踪，而且您可能不希望一个基本的 NGINX pod 被拒绝。

在继续执行下一步之前，请使用[kubectl 删除窗格][kubectl-delete]命令删除此测试窗格：

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>创建自定义 pod 安全策略

现在，您已经看到默认 pod 安全策略的行为，让我们为*非管理员用户*提供一种成功计划 pod 的方法。

让我们创建一个策略来拒绝请求特权访问的窗格。 其他选项（如*runAsUser*或允许*的卷*）不会显式限制。 这种类型的策略拒绝特权访问请求，但允许群集运行请求的 pod。

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

使用[kubectl 应用][kubectl-apply]命令创建策略并指定 YAML 清单的名称：

```console
kubectl apply -f psp-deny-privileged.yaml
```

要查看可用的策略，请使用[kubectl get psp][kubectl-get]命令，如以下示例所示。 将*psp 拒绝特权*策略与前面示例中为创建 pod 而强制执行的默认*受限*策略进行比较。 只有您的策略拒绝使用*PRIV*升级。 *对于 psp 拒绝特权*策略的用户或组没有限制。

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>允许用户帐户使用自定义 pod 安全策略

在上一步中，您创建了一个 pod 安全策略来拒绝请求特权访问的 pod。 要允许使用策略，请创建*角色*或*群集角色*。 然后，使用*角色绑定*或*群集角色绑定*关联这些角色之一。

在此示例中，创建一个群集角色，允许您*使用*上一步中创建的*psp 拒绝特权*策略。 创建名为 `psp-deny-privileged-clusterrole.yaml` 的文件并粘贴以下 YAML 清单：

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

使用[kubectl 应用][kubectl-apply]命令创建群集角色并指定 YAML 清单的名称：

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

现在创建一个群集角色绑定，以使用在上一步中创建的群集角色。 创建名为 `psp-deny-privileged-clusterrolebinding.yaml` 的文件并粘贴以下 YAML 清单：

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

使用[kubectl 应用][kubectl-apply]命令创建群集角色绑定并指定 YAML 清单的名称：

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 在本文的第一步中，在 AKS 群集上启用了 pod 安全策略功能。 建议的做法是仅在定义自己的策略后启用 pod 安全策略功能。 这是启用 pod 安全策略功能的阶段。 已定义一个或多个自定义策略，并且用户帐户已与这些策略关联。 现在，您可以安全地启用 pod 安全策略功能，并将默认策略引起的问题降至最低。

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>再次测试创建无特权 pod

应用自定义 pod 安全策略并为用户帐户使用该策略绑定后，让我们尝试再次创建无特权 pod。 使用相同的`nginx-privileged.yaml`清单使用[kubectl 应用][kubectl-apply]命令创建 pod：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

已成功计划窗格。 当您使用[kubectl 获取窗格][kubectl-get]命令检查 pod 的状态时，该窗格正在*运行*：

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

此示例演示如何创建自定义 pod 安全策略，以定义对不同用户或组 AKS 群集的访问。 默认 AKS 策略对哪些 pod 可以运行提供严格控制，因此请创建自己的自定义策略，然后正确定义所需的限制。

使用[kubectl 删除][kubectl-delete]命令删除 NGINX 无特权 pod 并指定 YAML 清单的名称：

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>清理资源

要禁用 pod 安全策略，请再次使用[az aks 更新][az-aks-update]命令。 以下示例禁用名为*myResourceGroup*的资源组中的群集名称*myAKSCluster*上的 pod 安全策略：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

接下来，删除群集角色和群集角色绑定：

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

使用[kubectl 删除命令删除][kubectl-delete]安全策略并指定 YAML 清单的名称：

```console
kubectl delete -f psp-deny-privileged.yaml
```

最后，删除*psp-aks*命名空间：

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>后续步骤

本文介绍如何创建 pod 安全策略以防止使用特权访问。 策略可以强制执行许多功能，例如卷类型或 RunAs 用户。 有关可用选项的详细信息，请参阅[Kubernetes pod 安全策略参考文档][kubernetes-policy-reference]。

有关限制 pod 网络流量的详细信息，请参阅[使用 AKS 中的网络策略在 Pod 之间安全流量][network-policies]。

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
