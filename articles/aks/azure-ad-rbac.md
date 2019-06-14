---
title: 在 Azure Kubernetes 服务中使用 RBAC 和 Azure AD 控制群集资源
description: 了解如何使用 Azure Active Directory 组成员身份在 Azure Kubernetes 服务 (AKS) 中通过基于角色的访问控制 (RBAC) 来限制对群集资源的访问
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: e974c47d1dfb04f66b622c64a7143d00de87c4cb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60467538"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>在 Azure Kubernetes 服务中使用基于角色的访问控制和 Azure Active Directory 标识来控制对群集资源的访问

可将 Azure Kubernetes Service (AKS) 配置为使用 Azure Active Directory (AD) 进行用户身份验证。 在此配置中，可以使用 Azure AD 身份验证令牌登录到 AKS 群集。 还可以根据用户标识或目录组成员身份配置 Kubernetes 基于角色的访问控制 (RBAC)，以限制对群集资源的访问。

本文介绍如何使用 Azure AD 组成员身份在 AKS 群集中通过 Kubernetes RBAC 来控制对命名空间和群集资源的访问。 将在 Azure AD 中创建示例组和用户，然后在 AKS 群集中创建角色和角色绑定，以授予创建和查看资源的相应权限。

## <a name="before-you-begin"></a>开始之前

本文假设已有一个启用了 Azure AD 集成的现有 AKS 群集。 如需 AKS 群集，请参阅[将 Azure Active Directory 与 AKS 集成][azure-ad-aks-cli]。

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="create-demo-groups-in-azure-ad"></a>在 Azure AD 中创建演示组

本文将会创建两个用户角色用于演示 Kubernetes RBAC 和 Azure AD 如何控制对群集资源的访问。 将使用以下两个示例角色：

* **应用程序开发人员**
    * 属于 *appdev* 组的名为 *aksdev* 的用户。
* **站点可靠性工程师**
    * 属于 *opssre* 组的名为 *akssre* 的用户。

在生产环境中，可以使用 Azure AD 租户中的现有用户和组。

首先，使用 [az aks show][az-aks-show] 命令获取 AKS 群集的资源 ID。 将该资源 ID 分配到名为 *AKS_ID* 的变量，以便可以在其他命令中引用它。

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

使用 [az ad group create][az-ad-group-create] 命令在 Azure AD 中为应用程序开发人员创建第一个示例组。 以下示例创建名为 *appdev* 的组：

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

现在，使用 [az role assignment create][az-role-assignment-create] 命令为 *appdev* 组创建 Azure 角色分配。 此分配可为该组的任何成员授予 Azure Kubernetes 服务群集用户角色，因此可让他们使用 `kubectl` 来与 AKS 群集交互。 

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> 如果出现类似于 `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` 的错误，请等待几秒，让 Azure AD 组对象 ID 传播到整个目录，然后重试 `az role assignment create` 命令。

为名为 *opssre* 的 SRE 创建第二个示例组。

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

同样，请创建 Azure 角色分配，以便为该组的成员授予 Azure Kubernetes 服务群集用户角色： 

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>在 Azure AD 中创建演示用户

在 Azure AD 中为应用程序开发人员和 SRE 创建两个示例组后，接下来让我们创建两个示例用户。 在本文结束时若要测试 RBAC 集成，需要使用这些帐户登录到 AKS 群集。

使用 [az ad user create][az-ad-user-create] 命令在 Azure AD 中创建第一个用户帐户。

以下示例使用显示名称 *AKS Dev* 和用户主体名称 (UPN) `aksdev@contoso.com` 创建一个用户。 请更新该 UPN 以包含 Azure AD 租户的验证域（请将 *contoso.com* 替换为你自己的域），并提供自己的安全 `--password` 凭据：

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

现在，使用 [az ad group member add][az-ad-group-member-add] 命令将该用户添加到在上一部分创建的 *appdev* 组：

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

创建第二个用户帐户。 以下示例使用显示名称 *AKS SRE* 和用户主体名称 (UPN) `akssre@contoso.com` 创建一个用户。 同样，请更新该 UPN 以包含 Azure AD 租户的验证域（请将 *contoso.com* 替换为你自己的域），并提供自己的安全 `--password` 凭据：

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>为应用开发人员创建 AKS 群集资源

现在创建 Azure AD 组和用户。 之前已经为组成员创建了 Azure 角色分配，使他们能够以普通用户的身份连接到 AKS 群集。 现在，让我们配置 AKS 群集，以允许这些不同的组访问特定的资源。

首先使用 [az aks get-credentials][az-aks-get-credentials] 命令获取群集管理员凭据。 在以下部分之一，你将获取普通用户群集凭据，以查看 Azure AD 身份验证流的运作方式。 

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

使用 [kubectl create namespace][kubectl-create] 命令在 AKS 群集中创建一个命名空间。 以下示例创建名为 *dev* 的命名空间：

```console
kubectl create namespace dev
```

在 Kubernetes 中，角色定义要授予的权限，角色绑定将这些权限应用到所需的用户或组。   这些分配可应用于特定命名空间或整个群集。 有关详细信息，请参阅[使用 RBAC 授权][rbac-authorization]。

首先，为 *dev* 命名空间创建一个角色。 此角色授予对命名空间的完全权限。 在生产环境中，可为不同的用户或组指定更精细的权限。

创建名为 `role-dev-namespace.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

使用 [kubectl apply][kubectl-apply] 命令创建角色，并指定 YAML 清单的文件名：

```console
kubectl apply -f role-dev-namespace.yaml
```

接下来，使用 [az ad group show][az-ad-group-show] 命令获取 *appdev* 组的资源 ID。 此组将设置为在下一步骤中创建的角色绑定的使用者。

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

现在，为 *appdev* 组创建角色绑定，以使用前面创建的角色来访问命名空间。 创建名为 `rolebinding-dev-namespace.yaml` 的文件并粘贴以下 YAML 清单。 在最后一行中，请将 *groupObjectId* 替换为前一命令的组对象 ID 输出：

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

使用 [kubectl apply][kubectl-apply] 命令创建角色绑定，并指定 YAML 清单的文件名：

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>为 SRE 创建 AKS 群集资源

现在请重复上述步骤，为 SRE 创建命名空间、角色和角色绑定。

首先，使用 [kubectl create namespace][kubectl-create] 命令为 *sre* 创建一个命名空间：

```console
kubectl create namespace sre
```

创建名为 `role-sre-namespace.yaml` 的文件并粘贴以下 YAML 清单：

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

使用 [kubectl apply][kubectl-apply] 命令创建角色，并指定 YAML 清单的文件名：

```console
kubectl apply -f role-sre-namespace.yaml
```

使用 [az ad group show][az-ad-group-show] 命令获取 *opssre* 组的资源 ID：

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

为 *opssre* 组创建角色绑定，以使用前面创建的角色来访问命名空间。 创建名为 `rolebinding-sre-namespace.yaml` 的文件并粘贴以下 YAML 清单。 在最后一行中，请将 *groupObjectId* 替换为前一命令的组对象 ID 输出：

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

使用 [kubectl apply][kubectl-apply] 命令创建角色绑定，并指定 YAML 清单的文件名：

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>使用 Azure AD 标识来与群集资源交互

现在，让我们通过在 AKS 群集中创建和管理资源，来测试权限是否按预期方式工作。 在这些示例中，你将在用户的分配命名空间中计划和查看 pod。 然后，尝试在分配的命名空间外部计划和查看 pod。

首先，使用 [az aks get-credentials][az-aks-get-credentials] 命令重置 *kubeconfig* 上下文。 在上一部分，你已使用群集管理员凭据设置了上下文。 管理员用户将绕过 Azure AD 登录提示。 如果未指定 `--admin` 参数，应用的用户上下文将要求使用 Azure AD 对所有请求进行身份验证。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

在 *dev* 命名空间中使用 [kubectl run][kubectl-run] 命令计划一个基本的 NGINX pod：

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

根据登录提示，输入在本文开头部分创建的 `appdev@contoso.com` 帐户的凭据。 成功登录后，帐户令牌将会缓存，供将来的 `kubectl` 命令使用。 如以下示例输出中所示，现已成功计划 NGINX：

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

现在，请在 *dev* 命名空间中使用 [kubectl get pods][kubectl-get] 命令查看 pod。

```console
kubectl get pods --namespace dev
```

如以下示例输出中所示，NGINX pod 已成功运行： 

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>在分配的命名空间外部创建和查看群集资源

现在请尝试在 *dev* 命名空间外部查看 pod。 再次使用 [kubectl get pod][kubectl-get] 命令，但这次会看到如下所示的 `--all-namespaces`：

```console
kubectl get pods --all-namespaces
```

该用户的组成员身份不具备允许此操作的 Kubernetes 角色，如以下示例输出中所示：

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

同样，尝试在不同的命名空间（例如 *sre* 命名空间）中计划 pod。 该用户的组成员身份与 Kubernetes 角色和角色绑定不相符，无法授予这些权限，如以下示例输出中所示：

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>测试 SRE 对 AKS 群集资源的访问

若要确认 Azure AD 组成员身份和 Kubernetes RBAC 是否可在不同的用户和组之间正常运行，请在以 *opssre* 用户身份登录后尝试运行前面的命令。

使用 [az aks get-credentials][az-aks-get-credentials] 命令重置 *kubeconfig* 上下文，以清除前面为 *aksdev* 用户缓存的身份验证令牌：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

尝试分配的 *sre* 命名空间中计划和查看 pod。 出现提示时，请使用在本文开头部分创建的 `opssre@contoso.com` 凭据登录：

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

如以下示例输出中所示，可以成功创建和查看 pod:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

现在，请尝试在分配的 SRE 命名空间外部查看或计划 pod：

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

如以下示例输出中所示，这些 `kubectl` 命令失败： 用户的组成员身份和 Kubernetes 角色与角色绑定无法授予在其他命名空间中创建或管理资源的权限：

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>清理资源

在本文中，你已在 AKS 群集中创建了资源，并在 Azure AD 中创建了用户和组。 若要清理所有这些资源，请运行以下命令：

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>后续步骤

有关如何保护 Kubernetes 群集的详细信息，请参阅 [AKS 的访问和标识选项][rbac-authorization]。

有关标识和资源控制的最佳做法，请参阅[有关 AKS 中的身份验证和授权的最佳做法][operator-best-practices-identity]。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
