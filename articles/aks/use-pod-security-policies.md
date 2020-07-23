---
title: 在 Azure Kubernetes Service （AKS）中使用 pod 安全策略
description: 了解如何使用 Azure Kubernetes 服务（AKS）中的 PodSecurityPolicy 控制 pod 招生
services: container-service
ms.topic: article
ms.date: 06/30/2020
ms.openlocfilehash: dd526b7825279d886c60fbb1820222a75abab03e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507074"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>预览-在 Azure Kubernetes Service （AKS）中使用 pod 安全策略保护群集

> [!WARNING]
> **本文档中所述的功能 "pod 安全策略（预览版）" 设置为 "弃用"，在2020年10月15日之后将不再提供**，以支持[AKS 的 Azure 策略](use-pod-security-on-azure-policy.md)。
>
> 在弃用安全策略（预览版）后，必须在任何现有群集上禁用该功能，该功能使用不推荐使用的功能来执行将来的群集升级并保持在 Azure 支持范围内。
>
> 强烈建议使用适用于 AKS 的 Azure 策略开始测试方案，此策略提供内置策略来保护 pod 和内置计划，这些策略可映射到 pod 安全策略。 单击此处了解有关[从 pod 安全策略（预览版）迁移到 Azure 策略的](use-pod-security-on-azure-policy.md#migrate-from-kubernetes-pod-security-policy-to-azure-policy)信息。

若要提高 AKS 群集的安全性，可以限制可计划的 pod。 请求不允许的资源的 pod 无法在 AKS 群集中运行。 使用 pod 安全策略定义此访问权限。 本文介绍如何在 AKS 中使用 pod 安全策略来限制 pod 的部署。

> [!IMPORTANT]
> AKS 预览功能是自助式选择加入功能。 预览版“按原样”提供，并且仅在“可用情况下”提供，不包含在服务级别协议和有限保障中。 AKS 预览版的内容部分包含在客户支持中，我们只能尽力提供支持。 因此，这些功能不应用于生产。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要使用 pod 安全策略，需要*aks* CLI 扩展版本0.4.1 或更高版本。 使用 [az extension add][az-extension-add] 命令安装 *aks-preview* Azure CLI 扩展，然后使用 [az extension update][az-extension-update] 命令检查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>注册 pod 安全策略功能提供程序

**此文档和功能在2020年10月15日设置为弃用。**

若要创建或更新 AKS 群集以使用 pod 安全策略，请先在订阅上启用功能标志。 若要注册*PodSecurityPolicyPreview*功能标志，请使用[az feature register][az-feature-register]命令，如以下示例中所示：

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间**。 可以使用 [az feature list][az-feature-list] 命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

准备就绪后，请使用[az provider register][az-provider-register]命令刷新*ContainerService*资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod 安全策略概述

在 Kubernetes 群集中，当要创建资源时，会使用一个许可控制器来截获对 API 服务器的请求。 然后，该许可控制器可以根据一组规则*验证*资源请求 *，或改变*资源来更改部署参数。

*PodSecurityPolicy*是一种许可控制器，用于验证 pod 规范是否满足定义的要求。 这些要求可能会限制使用特权容器、访问某些类型的存储，或者容器可以运行的用户或组。 如果尝试部署的资源的 pod 规范不满足 pod 安全策略中所述的要求，则会拒绝该请求。 此功能可控制可在 AKS 群集中计划的 pod，防止出现一些可能的安全漏洞或权限升级。

在 AKS 群集中启用 pod 安全策略时，会应用某些默认策略。 这些默认策略提供现成的体验来定义可计划的 pod。 但是，在你定义自己的策略之前，群集用户可能会遇到部署 pod 的问题。 推荐的方法是：

* 创建 AKS 群集
* 定义自己的 pod 安全策略
* 启用 pod 安全策略功能

若要显示默认策略限制 pod 部署的方式，在本文中，我们首先启用 pod 安全策略功能，然后创建自定义策略。

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>在 AKS 群集上启用 pod 安全策略

可以使用[az aks update][az-aks-update]命令启用或禁用 pod 安全策略。 以下示例在名为*myResourceGroup*的资源组中的群集名称*myAKSCluster*上启用 pod 安全策略。

> [!NOTE]
> 对于实际使用，在定义自己的自定义策略之前，不要启用 pod 安全策略。 本文介绍如何将 pod 安全策略启用为第一步，以查看默认策略限制 pod 部署的方式。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>默认 AKS 策略

启用 pod 安全策略时，AKS 会创建一个名为 "*特权*" 的默认策略。 请勿编辑或删除默认策略。 应创建自己的策略来定义要控制的设置。 首先，让我们看一下这些默认策略是如何影响 pod 部署的。

若要查看可用的策略，请使用[kubectl get psp][kubectl-get]命令，如以下示例中所示

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*特权*pod 安全策略将应用到 AKS 群集中任何经过身份验证的用户。 此分配由 ClusterRoles 和 ClusterRoleBindings 控制。 使用[kubectl get rolebindings][kubectl-get]命令并搜索*kube-system*命名空间中的*默认：特权：* binding：

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

如以下简洁输出所示，可以将*psp：特权*ClusterRole 分配给任何*系统：经过身份验证*的用户。 此功能可以提供基本级别的特权，而不会定义你自己的策略。

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
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
  name: system:masters
```

在开始创建自己的 pod 安全策略之前，请务必了解这些默认策略如何与用户请求进行交互来计划 pod。 在接下来的几个部分中，我们将安排一些箱来查看这些默认策略的运行情况。

## <a name="create-a-test-user-in-an-aks-cluster"></a>在 AKS 群集中创建测试用户

默认情况下，当你使用[az aks get 凭据][az-aks-get-credentials]命令时，aks 群集的*管理员*凭据将添加到你的 `kubectl` 配置中。管理员用户绕过 pod 安全策略的实施。 如果为 AKS 群集使用 Azure Active Directory 集成，则可以使用非管理员用户的凭据登录，以查看操作中的执行策略。 在本文中，我们将在 AKS 群集中创建一个可使用的测试用户帐户。

使用[kubectl create namespace][kubectl-create]命令为测试资源创建名为*aks*的示例命名空间。 然后，使用[kubectl create serviceaccount][kubectl-create]命令创建名为*nonadmin 的*服务帐户：

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

接下来，创建 RoleBinding，让*nonadmin-user*使用[kubectl create RoleBinding][kubectl-create]命令执行命名空间中的基本操作：

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>为管理员和非管理员用户创建别名命令

若要突出显示使用 `kubectl` 和在前面步骤中创建的非管理员用户时常规管理员用户之间的差异，请创建两个命令行别名：

* **Kubectl**别名适用于常规管理员用户，其作用域为*psp-aks*命名空间。
* **Kubectl-nonadminuser**别名适用于在上一步中创建的*nonadmin 用户*，其作用域为*psp-aks*命名空间。

创建这两个别名，如以下命令所示：

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>测试特权 pod 的创建

首先，请测试在使用的安全上下文来计划 pod 时所发生的情况 `privileged: true` 。 此安全上下文将升级 pod 的权限。 在上一部分中显示了默认 AKS pod 安全策略的情况下，*特权*策略应拒绝此请求。

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

使用[kubectl apply][kubectl-apply]命令创建 pod，并指定 YAML 清单的名称：

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

无法计划 pod，如以下示例输出所示：

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Pod 不会到达计划阶段，因此在继续操作之前，没有要删除的资源。

## <a name="test-creation-of-an-unprivileged-pod"></a>测试非特权 pod 的创建

在上面的示例中，pod 规范请求了特权升级。 默认*权限*pod 安全策略拒绝了此请求，因此无法计划 pod。 现在，让我们尝试在没有权限提升请求的情况下运行相同的 NGINX pod。

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

使用[kubectl apply][kubectl-apply]命令创建 pod，并指定 YAML 清单的名称：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

无法计划 pod，如以下示例输出所示：

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Pod 不会到达计划阶段，因此在继续操作之前，没有要删除的资源。

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>测试使用特定用户上下文创建 pod

在上面的示例中，容器映像自动尝试使用根将 NGINX 绑定到端口80。 此请求已被默认*权限*pod 安全策略拒绝，因此无法启动 pod。 现在，让我们尝试使用特定的用户上下文（如）运行相同的 NGINX pod `runAsUser: 2000` 。

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

使用[kubectl apply][kubectl-apply]命令创建 pod，并指定 YAML 清单的名称：

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

无法计划 pod，如以下示例输出所示：

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Pod 不会到达计划阶段，因此在继续操作之前，没有要删除的资源。

## <a name="create-a-custom-pod-security-policy"></a>创建自定义 pod 安全策略

现在，你已了解默认 pod 安全策略的行为，接下来让我们为*nonadmin-用户*提供一种成功计划 pod 的方式。

让我们创建一个策略，用于拒绝请求特权访问的 pod。 其他选项（例如*runAsUser*或允许使用的*卷*）未显式限制。 这种类型的策略拒绝请求特权访问，但允许群集运行请求的 pod。

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

使用[kubectl apply][kubectl-apply]命令创建策略，并指定 YAML 清单的名称：

```console
kubectl apply -f psp-deny-privileged.yaml
```

若要查看可用的策略，请使用[kubectl get psp][kubectl-get]命令，如以下示例中所示。 使用在前面的示例中强制执行的默认*权限*策略来比较*psp 拒绝特权*策略，以创建 pod。 策略仅拒绝使用*特权*升级。 对于 " *psp 拒绝特权*" 策略，用户或组没有任何限制。

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>允许用户帐户使用自定义 pod 安全策略

在上一步中，已创建了一个用于拒绝请求特权访问的 pod 的 pod 安全策略。 若要允许使用策略，请创建*角色*或*ClusterRole*。 然后，使用*RoleBinding*或*ClusterRoleBinding*关联其中一个角色。

在此示例中，创建一个允许*使用*上一步中创建的 " *psp-拒绝-特权*" 策略的 ClusterRole。 创建名为 `psp-deny-privileged-clusterrole.yaml` 的文件并粘贴以下 YAML 清单：

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

使用[kubectl apply][kubectl-apply]命令创建 ClusterRole，并指定 YAML 清单的名称：

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

现在，创建 ClusterRoleBinding 以使用上一步中创建的 ClusterRole。 创建名为 `psp-deny-privileged-clusterrolebinding.yaml` 的文件并粘贴以下 YAML 清单：

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

使用[kubectl apply][kubectl-apply]命令创建 ClusterRoleBinding，并指定 YAML 清单的名称：

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 在本文的第一步中，已在 AKS 群集上启用 pod 安全策略功能。 建议的做法是，在定义自己的策略后，只启用 pod 安全策略功能。 这是启用 pod 安全策略功能的阶段。 已经定义了一个或多个自定义策略，并且用户帐户已与这些策略相关联。 现在，你可以安全地启用 pod 安全策略功能，并最大限度地减少了默认策略导致的问题。

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>再次测试创建无特权的 pod

应用你的自定义 pod 安全策略，并使用该用户帐户的绑定来使用该策略时，让我们再次尝试创建无特权的 pod。 使用同一个 `nginx-privileged.yaml` 清单，使用[kubectl apply][kubectl-apply]命令创建 pod：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

已成功计划 pod。 使用[kubectl get][kubectl-get] pod 命令检查 pod 的状态时，Pod 正在*运行*：

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

此示例演示如何创建自定义 pod 安全策略，以定义针对不同用户或组的 AKS 群集的访问权限。 默认的 AKS 策略可对 pod 运行的内容提供严格的控制，因此，请创建你自己的自定义策略，然后正确定义所需的限制。

使用[kubectl delete][kubectl-delete]命令删除 NGINX 无特权 pod，并指定 YAML 清单的名称：

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>清理资源

若要禁用 pod 安全策略，请再次使用[az aks update][az-aks-update]命令。 以下示例在名为*myResourceGroup*的资源组中的群集名称*myAKSCluster*上禁用 pod 安全策略：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

接下来，删除 ClusterRole 和 ClusterRoleBinding：

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

使用[kubectl delete][kubectl-delete]命令删除安全策略，并指定 YAML 清单的名称：

```console
kubectl delete -f psp-deny-privileged.yaml
```

最后，删除*psp-aks*命名空间：

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建 pod 安全策略来防止使用特权访问。 策略可以强制执行许多功能，如卷或 RunAs 用户的类型。 有关可用选项的详细信息，请参阅[Kubernetes pod 安全策略参考文档][kubernetes-policy-reference]。

有关限制 pod 网络流量的详细信息，请参阅[使用 AKS 中的网络策略在 pod 之间进行安全通信][network-policies]。

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
