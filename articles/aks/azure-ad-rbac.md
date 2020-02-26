---
title: 在 Azure Kubernetes 服务中通过 RBAC 和 Azure AD 控制群集资源
description: 了解如何在 Azure Kubernetes Service （AKS）中使用基于角色的访问控制（RBAC）来使用 Azure Active Directory 组成员身份限制对群集资源的访问权限
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: 456b6dcdd590b48e06c830db85b726d4bebb69e3
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596515"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>使用基于角色的访问控制和 Azure Kubernetes 服务中的 Azure Active Directory 标识控制对群集资源的访问

可将 Azure Kubernetes Service (AKS) 配置为使用 Azure Active Directory (AD) 进行用户身份验证。 在此配置中，使用 Azure AD 身份验证令牌登录到 AKS 群集。 你还可以配置 Kubernetes 基于角色的访问控制（RBAC），以根据用户的标识或组成员身份限制对群集资源的访问权限。

本文介绍如何使用 Azure AD 组成员身份，通过 AKS 群集中的 Kubernetes RBAC 控制对命名空间和群集资源的访问。 示例组和用户在 Azure AD 中创建，然后在 AKS 群集中创建角色和 RoleBindings 以授予创建和查看资源的适当权限。

## <a name="before-you-begin"></a>开始之前

本文假设已使用 Azure AD 集成启用了现有的 AKS 群集。 如果需要 AKS 群集，请参阅将[Azure Active Directory 与 AKS 集成][azure-ad-aks-cli]。

需要安装并配置 Azure CLI 版本2.0.61 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="create-demo-groups-in-azure-ad"></a>在 Azure AD 中创建演示组

在本文中，我们将创建两个用户角色，这些角色可用于显示 Kubernetes RBAC 和 Azure AD 如何控制对群集资源的访问。 使用以下两个示例角色：

* **应用程序开发人员**
    * 名为*aksdev*的用户，属于*appdev*组。
* **站点可靠性工程师**
    * 名为*akssre*的用户，属于*opssre*组。

在生产环境中，可以在 Azure AD 租户中使用现有用户和组。

首先，使用[az AKS show][az-aks-show]命令获取 AKS 群集的资源 ID。 将资源 ID 分配给名为*AKS_ID*的变量，以便可以在其他命令中引用它。

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

使用[az AD group create][az-ad-group-create]命令为应用程序开发人员 Azure AD 创建第一个示例组。 以下示例创建名为*appdev*的组：

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

现在，使用[az role create create][az-role-assignment-create]命令为*Appdev*组创建 Azure 角色分配。 此分配使组中的任何成员都可以使用 `kubectl` 与 AKS 群集进行交互，方法是向其授予*Azure Kubernetes Service 群集用户角色*。

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> 如果收到 `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`错误，请等待几秒钟，让 Azure AD 组对象 ID 传播到目录中，然后重试 `az role assignment create` 命令。

创建第二个示例组，这是一个名为*opssre*的 SREs：

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

同样，创建 Azure 角色分配，以授予组成员*Azure Kubernetes Service 群集用户角色*：

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>在 Azure AD 中创建演示用户

在应用程序开发人员和 SREs 的 Azure AD 中创建了两个示例组，现在我们将创建两个示例用户。 若要测试本文末尾的 RBAC 集成，请用这些帐户登录到 AKS 群集。

使用[az AD user create][az-ad-user-create]命令在 Azure AD 中创建第一个用户帐户。

下面的示例创建一个用户，其显示名称为*AKS Dev* ，用户主体名称（UPN）为 `aksdev@contoso.com`。 更新 UPN 以包括 Azure AD 租户的已验证域（将*contoso.com*替换为你自己的域），并提供你自己的安全 `--password` 凭据：

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

现在，使用[az ad group member add][az-ad-group-member-add]命令将用户添加到在上一部分中创建的*appdev*组：

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

创建第二个用户帐户。 下面的示例创建一个用户，该用户的显示名称为*AKS SRE* ，用户主体名称（UPN）为 `akssre@contoso.com`。 同样，更新 UPN 以包括 Azure AD 租户的已验证域（将*contoso.com*替换为你自己的域），并提供你自己的安全 `--password` 凭据：

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

此时会创建 Azure AD 组和用户。 已为组成员创建 Azure 角色分配，以便作为常规用户连接到 AKS 群集。 现在，让我们配置 AKS 群集，以允许这些不同的组访问特定资源。

首先，使用[az aks][az-aks-get-credentials]命令获取群集管理凭据。 在以下其中一节中，你将获得常规*用户*群集凭据，以查看 Azure AD 身份验证流的操作。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

使用[kubectl create namespace][kubectl-create]命令在 AKS 群集中创建命名空间。 下面的示例创建了一个命名空间名称*dev*：

```console
kubectl create namespace dev
```

在 Kubernetes 中，*角色*定义要授予的权限， *RoleBindings*将其应用于所需的用户或组。 这些分配可应用于特定命名空间或整个群集。 有关详细信息，请参阅[使用 RBAC 授权][rbac-authorization]。

首先，为*dev*命名空间创建一个角色。 此角色授予对命名空间的完全权限。 在生产环境中，可以为不同的用户或组指定更精细的权限。

创建一个名为 `role-dev-namespace.yaml` 的文件并粘贴以下 YAML 清单：

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

使用[kubectl apply][kubectl-apply]命令创建角色，并指定 YAML 清单的文件名：

```console
kubectl apply -f role-dev-namespace.yaml
```

接下来，使用[az ad group show][az-ad-group-show]命令获取*appdev*组的资源 ID。 在下一步中，此组设置为 RoleBinding 的主题。

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

现在，为*appdev*组创建 RoleBinding，以使用以前创建的用于命名空间访问的角色。 创建名为 `rolebinding-dev-namespace.yaml` 的文件并粘贴以下 YAML 清单。 在最后一行中，将*g*替换为上一命令中的组对象 ID 输出：

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

使用[kubectl apply][kubectl-apply]命令创建 RoleBinding，并指定 YAML 清单的文件名：

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>为 SREs 创建 AKS 群集资源

现在，重复前面的步骤，为 SREs 创建命名空间、角色和 RoleBinding。

首先，使用[kubectl create namespace][kubectl-create]命令为*sre*创建命名空间：

```console
kubectl create namespace sre
```

创建一个名为 `role-sre-namespace.yaml` 的文件并粘贴以下 YAML 清单：

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

使用[kubectl apply][kubectl-apply]命令创建角色，并指定 YAML 清单的文件名：

```console
kubectl apply -f role-sre-namespace.yaml
```

使用[az ad group show][az-ad-group-show]命令获取*opssre*组的资源 ID：

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

为*opssre*组创建 RoleBinding，以使用以前创建的用于命名空间访问的角色。 创建名为 `rolebinding-sre-namespace.yaml` 的文件并粘贴以下 YAML 清单。 在最后一行中，将*g*替换为上一命令中的组对象 ID 输出：

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

使用[kubectl apply][kubectl-apply]命令创建 RoleBinding，并指定 YAML 清单的文件名：

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>使用 Azure AD 标识与群集资源交互

现在，让我们测试在 AKS 群集中创建和管理资源时所需的权限。 在这些示例中，你计划并查看用户分配的命名空间中的 pod。 然后，尝试在指定的命名空间之外计划和查看 pod。

首先，使用[az aks get 凭据][az-aks-get-credentials]命令重置*kubeconfig*上下文。 在上一节中，你将使用群集管理员凭据来设置上下文。 管理员用户将绕过 Azure AD 登录提示。 如果没有 `--admin` 参数，则应用需要使用 Azure AD 对所有请求进行身份验证的用户上下文。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

使用*dev*命名空间中的[kubectl run][kubectl-run]命令计划基本 NGINX pod：

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

作为 "登录" 提示时，输入你自己 `appdev@contoso.com` 在本文开头创建的帐户的凭据。 成功登录后，帐户令牌将缓存，以便以后 `kubectl` 命令。 NGINX 已成功计划，如以下示例输出所示：

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

现在，使用[kubectl get][kubectl-get] pod 命令查看*dev*命名空间中的 pod。

```console
kubectl get pods --namespace dev
```

如下面的示例输出所示，NGINX pod 已成功*运行*：

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>在分配的命名空间之外创建和查看群集资源

现在，请尝试在*dev*命名空间外部查看 pod。 再次使用[kubectl get][kubectl-get] pod 命令，此时可以看到 `--all-namespaces` 如下所示：

```console
kubectl get pods --all-namespaces
```

用户的组成员身份没有允许此操作的 Kubernetes 角色，如以下示例输出所示：

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

同样，尝试在不同的命名空间（如*sre*命名空间）中计划一个 pod。 用户的组成员身份与 Kubernetes 角色和 RoleBinding 不一致，可授予这些权限，如以下示例输出所示：

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>测试对 AKS 群集资源的 SRE 访问权限

若要确认我们的 Azure AD 组成员身份和 Kubernetes RBAC 在不同的用户和组之间正常工作，请在以*opssre*用户身份登录时尝试上述命令。

使用[az aks][az-aks-get-credentials] *kubeconfig*命令重置为*aksdev*用户清除以前缓存的身份验证令牌的上下文：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

尝试在已分配的*sre*命名空间中计划和查看 pod。 出现提示时，请用自己在本文开头创建的 `opssre@contoso.com` 凭据登录：

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

如下面的示例输出所示，你可以成功地创建和查看 pod：

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

现在，请尝试在已分配的 SRE 命名空间外查看或计划 pod：

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

这些 `kubectl` 命令将失败，如以下示例输出所示。 用户的组成员身份和 Kubernetes 角色和 RoleBindings 不授予在其他命名空间中创建或管理器资源的权限：

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>清理资源

本文介绍了在 AKS 群集中创建的资源以及 Azure AD 中的用户和组。 若要清理所有这些资源，请运行以下命令：

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

有关如何保护 Kubernetes 群集的详细信息，请参阅[AKS 的访问和标识选项）][rbac-authorization]。

有关标识和资源控制的最佳实践，请参阅[AKS 中的身份验证和授权的最佳实践][operator-best-practices-identity]。

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
