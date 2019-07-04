---
title: 使用 Azure Kubernetes 服务 (AKS) 中的 pod 安全策略
description: 了解如何使用 Azure Kubernetes 服务 (AKS) 中的 PodSecurityPolicy 控制 pod 病人入院
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: iainfou
ms.openlocfilehash: 9da722006651cfc9e9f2a175d5c330ba5df08123
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447065"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>预览-保护群集使用 Azure Kubernetes 服务 (AKS) 中的 pod 安全策略

若要提高在 AKS 群集的安全性，您可以限制 pod 可以是计划。 请求不允许的资源的 pod 无法在 AKS 群集中运行。 可以定义使用 pod 安全策略的此访问权限。 本文介绍如何使用 pod 安全策略以限制在 AKS 中的 pod 部署。

> [!IMPORTANT]
> AKS 预览版功能是自助服务的选择加入。 提供这些项目是为了从我们的社区收集反馈和 bug。 在预览版中，这些功能不是用于生产环境中使用。 公共预览版中的功能属于最大努力支持。 AKS 技术支持团队的协助营业时间太平洋时区 （太平洋标准时间） 仅将提供。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal]。

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要使用 pod 安全策略，需要*aks 预览版*CLI 扩展版本 0.4.1 或更高版本。 安装*aks 预览版*Azure CLI 扩展使用[az 扩展添加][az-extension-add]command, then check for any available updates using the [az extension update][az-extension-update]命令::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>注册 pod 安全策略功能提供程序

若要创建或更新使用 pod 安全策略的 AKS 群集，请先启用你的订阅上的一个功能标志。 若要注册*PodSecurityPolicyPreview*功能标志，请使用[az 功能注册][az-feature-register]命令，在下面的示例所示：

> [!CAUTION]
> 注册时对某一订阅功能，目前你无法取消注册该功能。 启用某些预览功能后，可能会对所有 AKS 群集，然后在订阅中创建使用默认值。 不要启用预览功能在生产订阅。 使用单独的订阅来测试预览功能和收集反馈。

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间  。 您可以检查注册状态使用[az 功能列表][az-feature-list]命令：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

准备就绪后，刷新的注册*Microsoft.ContainerService*使用的资源提供程序[az provider register][az-provider-register]命令：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod 安全策略概述

在 Kubernetes 群集中，许可控制器用于截获对 API 服务器的请求，以创建资源时。 然后可以通过许可控制器*验证*针对一组规则，对资源请求或*改变*要更改部署参数的资源。

*PodSecurityPolicy*是验证 pod 规范的许可控制器满足你定义的要求。 这些要求可能会限制特权容器使用、 对某些类型的存储，或用户或组可以作为运行容器的访问。 当你尝试部署的资源的 pod 规范不符的 pod 安全策略中所述的要求时，请求被拒绝。 此功能来控制 pod 可以是计划在 AKS 群集会阻止某些可能的安全漏洞或特权提升。

启用 AKS 群集中的 pod 安全策略，将应用的某些默认策略。 这些默认策略提供的可以计划的开箱体验来定义什么 pod 数。 但是，群集用户可能会遇到问题之前定义你自己的策略部署的 pod。 建议方法是：

* 创建 AKS 群集
* 定义 pod 的安全策略
* 启用 pod 安全策略功能

若要显示方式的默认策略限制 pod 部署，请在本文中我们首先启用 pod 安全策略功能，然后创建自定义策略。

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>启用 AKS 群集上的 pod 安全策略

可以启用或禁用 pod 安全策略使用[az aks 更新][az-aks-update]命令。 群集名称上的以下示例启用 pod 安全策略*myAKSCluster*资源组中名为*myResourceGroup*。

> [!NOTE]
> 供实际使用，不要启用 pod 安全策略，直到您定义您自己的自定义策略。 若要查看的默认策略如何限制 pod 的第一步在本文中，启用 pod 安全策略部署。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>AKS 的默认策略

当启用 pod 安全策略时，AKS 创建两个名为的默认策略*特权*并*受限*。 不会编辑或删除这些默认策略。 相反，创建你自己定义所需的设置以控制的策略。 让我们来看这些默认策略是它们如何影响 pod 部署。

若要查看可用的策略，请使用[kubectl 获取 psp][kubectl-get]命令，如下面的示例中所示。 默认值的一部分*受限*策略，用户被拒绝*PRIV*用于特权的 pod 升级和用户*MustRunAsNonRoot*。

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*受限*pod 安全策略应用于 AKS 群集中的任何经过身份验证用户。 此分配由 ClusterRoles 和 ClusterRoleBindings 控制。 使用[kubectl 获取 clusterrolebindings][kubectl-get]命令，并搜索*默认值： 受限：* 绑定：

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

以下浓缩版输出中, 所示*psp： 受限*ClusterRole 分配给任何*system： 经过身份验证*用户。 此功能而无需自己正在定义的策略提供基本级别的限制。

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

请务必了解这些默认策略如何与用户请求之前，您可以创建安全策略的你自己 pod 安排 pod 进行交互。 在下一步的几个部分，让我们计划某些 pod，查看这些操作中的默认策略。

## <a name="create-a-test-user-in-an-aks-cluster"></a>在 AKS 群集中创建的测试用户

默认情况下，当您使用[az aks get-credentials 来获取凭据][az-aks-get-credentials]命令，*管理员*AKS 群集的凭据，然后添加到你`kubectl`配置。管理员用户绕过 pod 安全策略的实施。 如果你的 AKS 群集使用 Azure Active Directory 的集成，可以非管理员用户的凭据进行登录，若要查看操作中的策略的实施。 在本文中，让我们创建的 AKS 群集，可以使用中的测试用户帐户。

创建名为示例命名空间*psp aks*测试使用的资源[kubectl 创建命名空间][kubectl-create]命令。 然后，创建一个名为服务帐户*nonadmin 用户*使用[kubectl 创建 serviceaccount][kubectl-create]命令：

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

接下来，创建为 RoleBinding *nonadmin 用户*若要使用在命名空间中执行基本操作[kubectl 创建 rolebinding][kubectl-create]命令：

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>创建管理员和非管理员用户的别名命令

若要突出显示时使用的正则管理用户之间的差异`kubectl`并在上一步骤中，创建的非管理员用户创建两个命令行别名：

* **Kubectl 管理员**别名是常规的管理员用户，且其范围为*psp aks*命名空间。
* **Kubectl nonadminuser**别名是有关*nonadmin 用户*创建在上一步骤中，且其范围为*psp aks*命名空间。

创建这些两个别名，如以下命令中所示：

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>测试特权 pod 的创建

让我们首先测试计划了具有的安全上下文的 pod 时，会发生什么情况`privileged: true`。 此安全上下文呈报 pod 的权限。 在上一节显示安全策略的默认 AKS pod*受限*策略应拒绝此请求。

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

Pod 使用，创建[kubectl 应用][kubectl-apply]命令并指定 YAML 清单的名称：

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Pod 无法安排，如以下示例输出中所示：

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Pod 不会达到计划阶段，因此没有要删除移动之前的资源。

## <a name="test-creation-of-an-unprivileged-pod"></a>测试创建的无特权 pod

在上一示例中，pod 规范请求权限的提升。 默认情况下拒绝此请求*受限*pod 的安全策略，因此在 pod 安排将失败。 让我们尝试现在运行而无需特权提升请求该相同 NGINX pod。

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

Pod 使用，创建[kubectl 应用][kubectl-apply]命令并指定 YAML 清单的名称：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Kubernetes 计划程序接受 pod 请求。 但是，如果您查看的 pod 使用状态`kubectl get pods`，出现错误：

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

使用[kubectl 描述 pod][kubectl-describe]命令以查看 pod 的事件。 以下浓缩版的示例显示了容器和映像需要根权限，即使我们没有请求它们：

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

尽管我们没有请求任何特权的访问，需要为端口创建绑定 NGINX 容器映像*80*。 若要将端口绑定*1024年*和更低，*根*是必需的用户。 当尝试启动，pod*受限*pod 安全策略拒绝此请求。

此示例演示通过 AKS 创建的默认 pod 安全策略在起作用并限制用户可以执行的操作。 正如您可能不期望基本的 NGINX pod，要对其拒绝，它是必须了解这些默认策略的行为。

移到下一步之前，请删除此测试 pod 使用[kubectl 删除 pod][kubectl-delete]命令：

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>测试创建了具有特定的用户上下文的 pod

在上一示例中，容器映像会自动尝试使用根将 NGINX 绑定到端口 80。 默认情况下已拒绝此请求*受限*pod 的安全策略，因此无法启动 pod。 让我们尝试现在运行特定的用户上下文中，使用该相同的 NGINX pod，如`runAsUser: 2000`。

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

Pod 使用，创建[kubectl 应用][kubectl-apply]命令并指定 YAML 清单的名称：

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Kubernetes 计划程序接受 pod 请求。 但是，如果您查看的 pod 使用状态`kubectl get pods`，没有比上一示例不同的错误：

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

使用[kubectl 描述 pod][kubectl-describe]命令以查看 pod 的事件。 以下浓缩版的示例显示了 pod 事件：

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

事件指示已创建容器，并已将其启动。 没有任何明显有关 pod 处于失败状态。 让我们看一下使用 pod 日志[kubectl 日志][kubectl-logs]命令：

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

以下示例日志输出将提供一个指示，指明的 NGINX 配置本身中，没有权限错误时，服务尝试启动。 无需将绑定到端口 80 再次导致此错误。 虽然 pod 规范定义的常规用户帐户，此用户帐户并不足够在 OS 级别对 NGINX 服务启动并绑定到受限制的端口。

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

同样，务必了解默认 pod 安全策略的行为。 此错误是有点难以跟踪，并再次，您不可能认为基本的 NGINX pod，要对其拒绝。

移到下一步之前，请删除此测试 pod 使用[kubectl 删除 pod][kubectl-delete]命令：

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>创建自定义 pod 安全策略

现在，已了解默认 pod 安全策略的行为，让我们来提供某种方式让*nonadmin 用户*成功安排 pod。

让我们创建一个策略来拒绝请求特权的访问的 pod。 其他选项，如*runAsUser*或允许*卷*，不显式受限制。 此类型的策略拒绝的请求特许访问权限，但否则可让群集运行请求的 pod。

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

创建策略使用[kubectl 应用][kubectl-apply]命令并指定 YAML 清单的名称：

```console
kubectl apply -f psp-deny-privileged.yaml
```

若要查看可用的策略，请使用[kubectl 获取 psp][kubectl-get]命令，如下面的示例中所示。 比较*psp 拒绝特权*策略使用默认*受限*已在前面的示例创建一个 pod 中强制实施的策略。 仅使用*PRIV*被策略拒绝升级。 上的用户或组没有限制*psp 拒绝特权*策略。

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>允许用户帐户使用自定义 pod 安全策略

在上一步骤中，您创建 pod 安全策略来拒绝 pod 该请求特权访问权限。 若要允许使用此策略，您创建*角色*或*ClusterRole*。 然后，将使用这些角色之一*RoleBinding*或*ClusterRoleBinding*。

对于此示例中，创建，您可以 ClusterRole*使用* *psp 拒绝特权*在上一步中创建策略。 创建名为 `psp-deny-privileged-clusterrole.yaml` 的文件并粘贴以下 YAML 清单：

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

创建使用 ClusterRole [kubectl 应用][kubectl-apply]命令并指定 YAML 清单的名称：

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

现在，创建要使用在上一步中创建 ClusterRole ClusterRoleBinding。 创建名为 `psp-deny-privileged-clusterrolebinding.yaml` 的文件并粘贴以下 YAML 清单：

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

创建使用 ClusterRoleBinding [kubectl 应用][kubectl-apply]命令并指定 YAML 清单的名称：

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 在这篇文章的第一个步骤中，AKS 群集上启用了 pod 安全策略功能。 建议的做法是仅定义了自己的策略后启用 pod 安全策略功能。 这是您将在其中启用 pod 安全策略功能的阶段。 已定义一个或多个自定义策略，并且用户帐户已与这些策略相关联。 现在，你可以安全地 pod 安全策略功能和最大程度减少由默认策略导致的问题。

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>测试再次的无特权的 pod 创建

使用自定义 pod 安全策略应用和用户帐户要使用策略的绑定，让我们尝试再次创建无特权的 pod。 使用相同`nginx-privileged.yaml`清单创建 pod 使用[kubectl 应用][kubectl-apply]命令：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

已成功计划 pod。 当检查的 pod 使用状态[kubectl get pod][kubectl-get]命令中，为 pod*运行*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

此示例演示如何创建自定义 pod 安全策略，以定义的访问权限为不同的用户或组在 AKS 群集。 默认值 AKS 策略提供受到严格控制哪些 pod 可以运行，因此创建您自己自定义策略，然后正确地定义所需的限制。

删除无特权的 NGINX pod 使用[kubectl 删除][kubectl-delete]命令并指定 YAML 清单的名称：

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>清理资源

若要禁用 pod 安全策略，请使用[az aks 更新][az-aks-update]试命令。 群集名称上的以下示例禁用 pod 安全策略*myAKSCluster*资源组中名为*myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

接下来，删除 ClusterRole 和 ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

删除使用网络策略[kubectl 删除][kubectl-delete]命令并指定 YAML 清单的名称：

```console
kubectl delete -f psp-deny-privileged.yaml
```

最后，删除*psp aks*命名空间：

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建 pod 安全策略，以防止特权访问的使用。 有许多策略可以强制执行，例如卷或运行方式用户的类型的功能。 有关可用选项的详细信息，请参阅[Kubernetes pod 安全策略参考文档][kubernetes-policy-reference]。

有关限制 pod 的网络流量的详细信息，请参阅[保护在 AKS 中使用网络策略的 pod 之间的流量][network-policies]。

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
