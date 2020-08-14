---
title: 使用 Web 仪表板管理 Azure Kubernetes 服务群集
description: 了解如何使用内置的 Kubernetes Web UI 仪表板管理 Azure Kubernetes 服务 (AKS) 群集
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: 35424c0a9e566a9dfa780c524e23945348335040
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225982"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>访问 Azure Kubernetes 服务 (AKS) 中的 Kubernetes Web 仪表板

Kubernetes 包含一个可用于基本管理操作的 Web 仪表板。 使用此仪表板，可以查看应用程序的基本运行状况状态和指标，创建并部署服务，以及编辑现有应用程序。 本文介绍如何使用 Azure CLI 访问 Kubernetes 仪表板，然后引导你完成一些基本的仪表板操作。

有关 Kubernetes 仪表板的详细信息，请参阅 [Kubernetes Web UI 仪表板][kubernetes-dashboard]。 AKS 使用版本 2.0 及更高版本的开源仪表板。

> [!WARNING]
> **AKS 仪表板外接程序设置为弃用。改 [为使用 Azure 门户中的 Kubernetes 资源视图 (预览版) ][kubernetes-portal] 。** 
> * 默认情况下，为运行的 Kubernetes 版本低于 1.18 的群集启用 Kubernetes 仪表板。
> * 默认情况下，在 Kubernetes 1.18 或更高版本上创建的所有新群集都将禁用仪表板加载项。 
 > * 从 Kubernetes 1.19 预览版开始，AKS 将不再支持安装托管的 kube-dashboard 加载项。 
 > * 启用了加载项的现有群集不会受到影响。 用户能够继续将开源仪表板作为用户安装的软件手动安装。

## <a name="before-you-begin"></a>准备阶段

本文档详述的步骤假设你已创建 AKS 群集并已通过该群集建立 `kubectl` 连接。 如果需要创建 AKS 群集，请参阅 [快速入门：使用 Azure CLI 部署 Azure Kubernetes 服务群集][aks-quickstart]。

还需要安装并配置 Azure CLI 2.6.0 或更高版本。 运行  `az --version`  即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="disable-the-kubernetes-dashboard"></a>禁用 Kubernetes 仪表板

默认情况下，在 K8s 版本低于 1.18 的群集上启用 kube-dashboard 加载项。 可以通过运行以下命令禁用加载项。

``` azure-cli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>启动 Kubernetes 仪表板

若要在群集上启动 Kubernetes 仪表板，请使用 [az aks browse][az-aks-browse] 命令。 此命令要求在群集上安装 kube-dashboard 加载项，运行任何低于 Kubernetes 1.18 的版本的群集都默认包含该加载项。

以下示例将为 *myResourceGroup* 资源组中的 *myAKSCluster* 群集打开仪表板：

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

此命令在开发系统与 Kubernetes API 之间创建一个代理，并在 Web 浏览器中打开 Kubernetes 仪表板。 如果 Web 浏览器未打开到 Kubernetes 仪表板，请复制并在 Azure CLI 中粘贴所记录的 URL 地址，通常为 `http://127.0.0.1:8001`。

> [!NOTE]
> 如果在 `http://127.0.0.1:8001` 没有看到仪表板，则可以手动路由到以下地址。 运行 1.16 或更高版本的群集使用 https 并需要单独的终结点。
> * K8s 1.16 或更高版本：`http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1.15 及更低版本：`http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.

> [!IMPORTANT]
> If your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>登录到仪表板 (kubernetes 1.16+)

> [!IMPORTANT]
> 从 [Kubernetes 仪表板 v1.10.1](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) 或 kubernetes v1.16 + 开始，由于[该版本中的安全修补程序](https://github.com/kubernetes/dashboard/pull/3400)，服务帐户“kubernetes-dashboard”不再能够用于检索资源。 因此，没有身份验证信息的请求会返回 401 未授权错误。 从服务帐户检索的持有者令牌仍可通过此 [Kubernetes 仪表板示例](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui)中的方式使用，但与早期版本相比，这会影响仪表板加载项的登录流。
>
>如果仍运行 1.16 之前的版本，则仍可向“kubernetes-dashboard”服务帐户授予权限，但不建议这样做：
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

显示的初始屏幕要求提供 kubeconfig 或令牌。 这两个选项都要求提供资源权限才会在仪表板中显示这些资源。

![登录屏幕](./media/kubernetes-dashboard/login.png)

**使用 kubeconfig**

对于启用了 Azure AD 的群集和启用了非 Azure AD 的群集，都可以传入kubeconfig。 确保访问令牌有效，如果令牌已过期，则可以通过 kubectl 刷新令牌。

1. 使用 `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>` 设置管理员 kubeconfig
1. 选择 `Kubeconfig` 并单击 `Choose kubeconfig file` 打开文件选择器
1. 选择 kubeconfig 文件（默认为 $HOME/.kube/config）
1. 单击 `Sign In`

**使用令牌**

1. 对于启用了非 Azure AD 的群集，运行 `kubectl config view` 并复制与群集的用户帐户关联的令牌。
1. 登录时，粘贴到令牌选项中。    
1. 单击 `Sign In`

对于启用了 Azure AD 的群集，使用以下命令检索 AAD 令牌。 验证是否已替换命令中的资源组和群集名称。

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

成功后，将显示类似如下的页面。

![Kubernetes Web 仪表板的概述页](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>创建应用程序

以下步骤要求用户具有对相应资源的权限。 

若要查看 Kubernetes 仪表板可以如何降低管理任务的复杂性，让我们创建一个应用程序。 可以从 Kubernetes 仪表板通过提供文本输入、YAML 文件或通过一个图形化向导来创建应用程序。

若要创建应用程序，请完成以下步骤：

1. 选择窗口右上角的“创建”按钮。
1. 若要使用图形化向导，请选择“创建应用”。
1. 为部署提供一个名称，例如 *nginx*
1. 输入要使用的容器映像的名称，例如 *nginx:1.15.5*
1. 若要为 Web 流量公开端口 80，请创建一个 Kubernetes 服务。 在“服务”下，选择“外部”，对于端口和目标端口，都输入 **80**。 
1. 准备就绪后，选择“部署”来创建应用。

![在 Kubernetes Web 仪表板中部署应用](./media/kubernetes-dashboard/create-app.png)

为 Kubernetes 服务分配公共外部 IP 地址需要一到两分钟时间。 在左侧，在“发现和负载均衡”下，选择“服务”。 此时将列出应用的服务，包括“外部终结点”，如以下示例中所示：

![查看服务和终结点的列表](./media/kubernetes-dashboard/view-services.png)

选择终结点地址以在 Web 浏览器窗口中打开默认的 NGINX 页面：

![查看部署的应用程序的默认 NGINX 页面](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>查看 Pod 信息

Kubernetes 仪表板可以提供基本的监视指标和故障排除信息，例如日志。

若要查看有关应用程序 Pod 的详细信息，请在左侧菜单中选择“Pod”。 此时会显示可用 Pod 的列表。 选择你的 *nginx* Pod 来查看信息，例如资源消耗：

![查看 Pod 信息](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>编辑应用程序

除了创建和查看应用程序之外，Kubernetes 仪表板还可以用来编辑和更新应用程序部署。 若要为应用程序提供额外的冗余，让我们来增加 NGINX 副本数。

若要编辑部署，请执行以下操作：

1. 在左侧菜单中选择“部署”，然后选择你的 *nginx* 部署。
1. 在右上角的导航栏中选择“编辑”。
1. 找到 `spec.replica` 值，大约在第 20 行。 若要增加应用程序的副本数，请将此值从 *1* 更改为 *3*。
1. 在完成后，选择“更新”。

![编辑部署以更新副本数](./media/kubernetes-dashboard/edit-deployment.png)

在副本集内创建新 Pod 需要花费一些时间。 在左侧菜单上，选择“副本集”，然后选择你的 *nginx* 副本集。 Pod 列表现在反映了已更新的副本计数，如以下示例输出中所示：

![查看副本集的信息](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>后续步骤

有关 Kubernetes 仪表板的详细信息，请参阅 [Kubernetes Web UI 仪表板][kubernetes-dashboard]。

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
