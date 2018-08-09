---
title: Azure Kubernetes 群集的服务主体
description: 在 Azure 容器服务中为 Kubernetes 群集创建和管理 Azure Active Directory 服务主体
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: get-started-article
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: efedb7cde06ed03ec330027a18b00bcc897919cf
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576913"
---
# <a name="set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>在容器服务中为 Kubernetes 群集设置 Azure AD 服务主体

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

在 Azure 容器服务中，Kubernetes 群集需要 [Azure Active Directory 服务主体](../../active-directory/develop/app-objects-and-service-principals.md)才能与 Azure API 交互。 需要服务主体才能动态管理相关资源，例如[用户定义路由](../../virtual-network/virtual-networks-udr-overview.md)和[第 4 层 Azure 负载均衡器](../../load-balancer/load-balancer-overview.md)。


本文介绍用于为 Kubernetes 群集设置服务主体的不同选项。 例如，如果已安装并设置 [Azure CLI 2.0](/cli/azure/install-az-cli2)，则可运行 [`az acs create`](/cli/azure/acs#az-acs-create) 命令，以便同时创建 Kubernetes 群集和服务主体。


## <a name="requirements-for-the-service-principal"></a>服务主体的的要求

可以使用现有的符合以下条件的 Azure AD 服务主体，也可以创建一个新的。

* **范围**：资源组

* **角色**：参与者

* **客户端机密**：必须是密码。 目前，无法使用针对证书身份验证设置的服务主体。

> [!IMPORTANT]
> 要创建服务主体，必须具有相应的权限，能够向 Azure AD 租户注册应用程序，并将应用程序分配到订阅中的角色。 要了解你是否具有必需的权限，请[在门户中查看](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions)。
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>选项 1：在 Azure AD 中创建服务主体

如需在部署 Kubernetes 群集之前创建 Azure AD 服务主体，可以使用 Azure 提供的多种方法。

以下示例命令演示如何通过 [Azure CLI 2.0](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) 执行此操作。 也可使用 [Azure PowerShell](../../azure-resource-manager/resource-group-authenticate-service-principal.md)、[门户](../../azure-resource-manager/resource-group-create-service-principal-portal.md)等方法创建服务主体。

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>"
```

输出如下所示（此处以密文形式显示）：

![创建服务主体](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

突出显示的是“客户端 ID”(`appId`) 和“客户端机密”(`password`)，用作群集部署的服务主体参数。


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>在创建 Kubernetes 群集时指定服务主体

创建 Kubernetes 群集时，提供现有服务主体的“客户端 ID”（也称为 `appId`，即应用程序 ID）和“客户端机密”(`password`) 作为参数。 请确保服务主体满足本文开头的要求。

可以在使用 [Azure 命令行界面 (CLI) 2.0](container-service-kubernetes-walkthrough.md)、[Azure 门户](../dcos-swarm/container-service-deployment.md)等方法部署 Kubernetes 群集时指定这些参数。

>[!TIP]
>指定“客户端 ID”时，请确保使用服务主体的 `appId` 而不是 `ObjectId`。
>

以下示例说明了一种通过 Azure CLI 2.0 传递参数的方法。 此示例使用 [Kubernetes 快速启动模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)。

1. 从 GitHub [下载](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json)模板参数文件 `azuredeploy.parameters.json`。

2. 若要指定服务主体，请在文件中输入 `servicePrincipalClientId` 和 `servicePrincipalClientSecret` 的值。 （还需提供自己的适用于 `dnsNamePrefix` 和 `sshRSAPublicKey` 的值。 后者是用于访问群集的 SSH 公钥。）保存文件。

    ![传递服务主体参数](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. 运行以下命令，使用 `--parameters` 设置 azuredeploy.parameters.json 文件的路径。 此命令将群集部署在用户创建的名为 `myResourceGroup` 的资源组（位于“美国西部”区域）中。

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus"

    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>选项 2：在通过 `az acs create` 创建群集时生成服务主体

如果通过运行 [`az acs create`](/cli/azure/acs#az-acs-create) 命令来创建 Kubernetes 群集，则可选择自动生成服务主体。

与其他 Kubernetes 群集创建选项一样，可以在运行 `az acs create` 时指定现有服务主体的参数。 不过，在省略这些参数的情况下，Azure CLI 会自动创建一个适用于容器服务的服务主体。 该操作以透明方式在部署过程中进行。

以下命令创建 Kubernetes 群集，并生成 SSH 密钥和服务主体凭据：

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> 如果帐户没有创建服务主体所需的 Azure AD 和订阅权限，该命令会生成类似于`Insufficient privileges to complete the operation.`的错误
>

## <a name="additional-considerations"></a>其他注意事项

* 如果没有在订阅中创建服务主体的权限，则可能需要请求 Azure AD 或订阅的管理员分配必需的权限，或者要求他们提供适用于 Azure 容器服务的服务主体。

* Kubernetes 的服务主体是群集配置的一部分。 但是，请勿使用标识来部署群集。

* 每个服务主体都与一个 Azure AD 应用程序相关联。 Kubernetes 群集的服务主体可以与任何有效的 Azure AD 应用程序名称（例如 `https://www.contoso.org/example`）相关联。 应用程序的 URL 不一定是实际的终结点。

* 指定服务主体的“客户端 ID”时，可以使用 `appId` 的值（如本文所示）或相应的服务主体 `name`（例如，`https://www.contoso.org/example`）。

* 在 Kubernetes 群集的主 VM 和代理 VM 中，服务主体凭据存储在 `/etc/kubernetes/azure.json` 文件中。

* 使用 `az acs create` 命令自动生成服务主体时，会将服务主体凭据写入用于运行命令的计算机上的 `~/.azure/acsServicePrincipal.json` 文件中。

* 使用 `az acs create` 命令自动生成服务主体时，服务主体也可以使用在同一订阅中创建的 [Azure 容器注册表](../../container-registry/container-registry-intro.md)进行身份验证。

* 服务主体凭据可能会过期，导致群集节点进入“NotReady”状态。 请参阅[凭据过期](#credential-expiration)部分，了解缓解信息。

## <a name="credential-expiration"></a>凭据过期

除非在创建服务主体时使用 `--years` 参数指定了自定义时效期，否则凭据的有效期为自创建之时起 1 年。 凭据过期后，群集节点可能进入“NotReady”状态。

若要查看服务主体的过期日期，请使用 `--debug` 参数执行 [az ad app show](/cli/azure/ad/app#az-ad-app-show) 命令，然后在输出底部附近查找 `passwordCredentials` 的 `endDate` 值：

```azurecli
az ad app show --id <appId> --debug
```

输出（此处显示的为截断的结果）：

```json
...
"passwordCredentials":[{"customKeyIdentifier":null,"endDate":"2018-11-20T23:29:49.316176Z"
...
```

如果服务主体凭据已过期，请使用 [az ad sp reset-credentials](/cli/azure/ad/sp#az-ad-sp-reset-credentials) 命令更新凭据：

```azurecli
az ad sp reset-credentials --name <appId>
```

输出：

```json
{
  "appId": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "name": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "password": "404203c3-0000-0000-0000-d1d2956f3606",
  "tenant": "72f988bf-0000-0000-0000b-2d7cd011db47"
}
```

然后，在所有群集节点上使用新凭据对 `/etc/kubernetes/azure.json` 进行更新，然后重启节点。

## <a name="next-steps"></a>后续步骤

* [开始使用 Kubernetes](container-service-kubernetes-walkthrough.md)（位于容器服务群集中）。

* 若要排查 Kubernetes 的服务主体问题，请参阅 [ACS 引擎文档](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting)。