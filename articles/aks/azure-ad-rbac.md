---
title: 使用 RBAC 和 Azure Kubernetes 服务中的 Azure AD 控制群集资源
description: 了解如何使用 Azure Active Directory 组成员身份来限制对使用基于角色的访问控制 (RBAC) 在 Azure Kubernetes 服务 (AKS) 群集资源的访问
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: e974c47d1dfb04f66b622c64a7143d00de87c4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467538"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>控制对在 Azure Kubernetes 服务中使用基于角色的访问控制和 Azure Active Directory 标识的群集资源的访问

可将 Azure Kubernetes Service (AKS) 配置为使用 Azure Active Directory (AD) 进行用户身份验证。 在此配置中，您登录到 AKS 群集使用 Azure AD 身份验证令牌。 你还可以配置 Kubernetes 基于角色的访问控制 (RBAC) 来限制对群集资源的访问基于用户的标识或组成员身份。

本文介绍如何使用 Azure AD 组成员身份来控制对命名空间的访问和群集资源在 AKS 群集中使用 Kubernetes RBAC。 在 Azure AD 中创建组的例子和用户，然后授予适当的权限来创建和查看资源在 AKS 群集中创建角色和 RoleBindings。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集与 Azure AD 的集成已启用。 如果需要 AKS 群集，请参阅[集成 Azure Active Directory 与 AKS][azure-ad-aks-cli]。

你需要 Azure CLI 版本 2.0.61 或更高版本安装和配置。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="create-demo-groups-in-azure-ad"></a>在 Azure AD 中创建演示组

在本文中，让我们创建两个可用于显示如何 Kubernetes RBAC 和 Azure AD 控制群集资源的访问权限的用户角色。 使用以下两个示例角色：

* **应用程序开发人员**
    * 名为的用户*aksdev*一部分*appdev*组。
* **网站可靠性工程师**
    * 名为的用户*akssre*一部分*opssre*组。

在生产环境中，可以使用现有用户和组对 Azure AD 租户中。

首先，获取 AKS 群集使用的资源 ID [az aks show] [ az-aks-show]命令。 将资源 ID 分配给一个名为变量*AKS_ID* ，以便它可以在其他命令中引用。

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

为使用的应用程序开发人员在 Azure AD 中创建第一个示例组[az ad 组创建][ az-ad-group-create]命令。 下面的示例创建名为的组*appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

现在，创建的 Azure 角色分配*appdev*组使用[az 角色分配创建][ az-role-assignment-create]命令。 此分配允许使用的组的任何成员`kubectl`AKS 群集与通过向它们授予交互*Azure Kubernetes 服务群集用户角色*。

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> 如果收到错误，如`Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`，等待几秒钟时间来传播到整个目录，然后重试的 Azure AD 组对象 ID`az role assignment create`试命令。

创建第二个示例组、 Sre 此一个名为*opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

同样，创建 Azure 角色分配以组的成员授予*Azure Kubernetes 服务群集用户角色*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>在 Azure AD 中创建演示用户

使用两个组的例子为我们的应用程序开发人员和 Sre 创建在 Azure AD 中，现在让我们创建两个示例用户。 若要测试在本文末尾的 RBAC 集成，应登录到这些帐户与 AKS 群集。

在 Azure ad 中创建第一个用户帐户[az ad 用户创建][ az-ad-user-create]命令。

下面的示例创建一个用户使用的显示名称*AKS Dev*和用户主体名称 (UPN) 的`aksdev@contoso.com`。 更新要包括的已验证的域的 Azure AD 租户的 UPN (替换*contoso.com*使用你自己的域)，并提供您自己安全`--password`凭据：

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

现在，添加到用户*appdev*在使用上一节中创建组[az ad 组成员添加][ az-ad-group-member-add]命令：

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

创建第二个用户帐户。 下面的示例创建一个用户使用的显示名称*AKS SRE*和用户主体名称 (UPN) 的`akssre@contoso.com`。 同样，更新要包括的已验证的域的 Azure AD 租户的 UPN (替换*contoso.com*使用你自己的域)，并提供您自己安全`--password`凭据：

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>创建应用程序开发人员的 AKS 群集资源

现在创建的 Azure AD 组和用户。 为要连接到 AKS 群集作为普通用户的组成员创建的 azure 角色分配。 现在，让我们配置 AKS 群集，以允许这些不同的组访问特定资源。

首先，获取群集使用的管理员凭据[az aks get-credentials 来获取凭据][ az-aks-get-credentials]命令。 在下列部分之一，获取常规*用户*群集以查看 Azure AD 身份验证的凭据运行中的流。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

使用在 AKS 群集中创建的命名空间[kubectl 创建命名空间][ kubectl-create]命令。 下面的示例创建一个命名空间名称*开发人员*:

```console
kubectl create namespace dev
```

在 Kubernetes 中，*角色*定义的权限授予，并*RoleBindings*将它们应用于所需的用户或组。 这些分配可应用于特定命名空间或整个群集。 有关详细信息，请参阅[使用 RBAC 授权][rbac-authorization]。

首先，创建的角色*开发人员*命名空间。 此角色授予对命名空间的完全权限。 在生产环境中，可以指定不同的用户或组的更精细权限。

创建名为`role-dev-namespace.yaml`并粘贴到以下 YAML 清单：

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

创建角色使用[kubectl 适用][ kubectl-apply]命令并指定 YAML 清单的文件名：

```console
kubectl apply -f role-dev-namespace.yaml
```

接下来，获取的资源 ID *appdev*组使用[az ad 组显示][ az-ad-group-show]命令。 此组将设置为下一步中 RoleBinding 的主题。

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

现在，创建为 RoleBinding *appdev*以前创建的角色用于命名空间访问组。 创建名为 `rolebinding-dev-namespace.yaml` 的文件并粘贴以下 YAML 清单。 在最后一行中，替换*groupObjectId*与前一命令的组对象 ID 输出：

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

创建使用 RoleBinding [kubectl 适用][ kubectl-apply]命令并指定 YAML 清单的文件名：

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>为 Sre 创建 AKS 群集资源

现在，重复前面的步骤以创建命名空间、 角色和 RoleBinding Sre。

首先，创建的命名空间*sre*使用[kubectl 创建命名空间][ kubectl-create]命令：

```console
kubectl create namespace sre
```

创建名为`role-sre-namespace.yaml`并粘贴到以下 YAML 清单：

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

创建角色使用[kubectl 适用][ kubectl-apply]命令并指定 YAML 清单的文件名：

```console
kubectl apply -f role-sre-namespace.yaml
```

获取的资源 ID *opssre*组使用[az ad 组显示][ az-ad-group-show]命令：

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

创建用于 RoleBinding *opssre*以前创建的角色用于命名空间访问组。 创建名为 `rolebinding-sre-namespace.yaml` 的文件并粘贴以下 YAML 清单。 在最后一行中，替换*groupObjectId*与前一命令的组对象 ID 输出：

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

创建使用 RoleBinding [kubectl 适用][ kubectl-apply]命令并指定 YAML 清单的文件名：

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>与使用 Azure AD 标识的群集资源进行交互

现在，让我们测试的预期的权限工作时创建和管理 AKS 群集中的资源。 在这些示例中，将计划和查看 pod 中用户的已分配命名空间。 然后，你尝试与已分配的命名空间之外的计划和查看 pod。

首先，重置*kubeconfig*上下文中使用[az aks get-credentials 来获取凭据][ az-aks-get-credentials]命令。 在上一节中，您将使用群集管理员凭据的上下文。 管理员用户绕过 Azure AD 登录提示。 无需`--admin`参数，用户上下文应用需要的所有请求进行身份验证使用 Azure AD。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

基本的 NGINX pod 使用计划[运行 kubectl] [ kubectl-run]命令，在*dev*命名空间：

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

作为登录提示符中，输入的凭据对你自己`appdev@contoso.com`本文开始时创建帐户。 已成功登录之后, 帐户令牌进行缓存以未来`kubectl`命令。 已成功计划 NGINX，如以下示例输出中所示：

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

现在，使用[kubectl get pod] [ kubectl-get]命令来查看中的 pod *dev*命名空间。

```console
kubectl get pods --namespace dev
```

下面的示例输出中所示，成功是 NGINX pod*运行*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>创建和查看已分配的命名空间之外的群集资源

现在，尝试查看外部的 pod*开发人员*命名空间。 使用[kubectl get pod] [ kubectl-get]命令，此时若要查看`--all-namespaces`，如下所示：

```console
kubectl get pods --all-namespaces
```

用户的组成员身份不具有允许此操作的 Kubernetes 角色，如以下示例输出中所示：

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

同样，尝试如计划在不同的命名空间中的 pod *sre*命名空间。 用户的组成员身份不一致使用 Kubernetes 角色和 RoleBinding 授予这些权限，如以下示例输出中所示：

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>测试 SRE 访问 AKS 群集资源

若要确认，我们的 Azure AD 组成员身份和 Kubernetes RBAC 不同的用户和组之间的正常运行，请尝试使用上一命令时以身份登录*opssre*用户。

重置*kubeconfig*上下文中使用[az aks get-credentials 来获取凭据][ az-aks-get-credentials]命令清除以前缓存的身份验证令牌*aksdev*用户：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

尝试在已分配的计划和查看 pod *sre*命名空间。 出现提示时，使用你自己登录`opssre@contoso.com`本文开始时创建凭据：

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

以下示例输出中所示，您可以成功创建和查看 pod:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

现在，尝试查看或计划分配 SRE 命名空间之外的 pod:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

这些`kubectl`命令失败，如以下示例输出中所示。 用户的组成员身份和 Kubernetes 角色和 RoleBindings 不授予创建权限或其他命名空间中的管理器资源：

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>清理资源

在本文中，已创建 AKS 群集和用户的资源和 Azure AD 中的组。 若要清理这些资源，请运行以下命令：

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

有关如何保护 Kubernetes 群集的详细信息，请参阅[适用于 AKS 的访问和标识选项)][rbac-authorization]。

标识和资源控制的最佳实践，请参阅[身份验证和授权在 AKS 中的最佳做法][operator-best-practices-identity]。

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
