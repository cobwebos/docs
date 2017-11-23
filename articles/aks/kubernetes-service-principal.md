---
title: "Azure Kubernetes 群集的服务主体 | Microsoft Docs"
description: "在 AKS 中为 Kubernetes 群集创建和管理 Azure Active Directory 服务主体"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: af27d01108cbfb3bd71023ffbce85f348abb0cfe
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="service-principals-with-azure-container-service-aks"></a>使用 Azure 容器服务 (AKS) 的服务主体

AKS 群集需要 [Azure Active Directory 服务主体](../active-directory/develop/active-directory-application-objects.md)才能与 Azure API 交互。 需要服务主体才能动态管理相关资源，例如[用户定义路由](../virtual-network/virtual-networks-udr-overview.md)和[第 4 层 Azure 负载均衡器](../load-balancer/load-balancer-overview.md)。

本文介绍用于在 AKS 中为 Kubernetes 群集设置服务主体的不同选项。

## <a name="before-you-begin"></a>开始之前

本文档详述的步骤假设你已创建 AKS 群集并已通过该群集建立 kubectl 连接。 如果需要这些项，请参阅 [AKS 快速入门](./kubernetes-walkthrough.md)。

若要创建 Azure AD 服务主体，必须具有相应的权限，能够向 Azure AD 租户注册应用程序，并将应用程序分配到订阅中的角色。 如果没有必需的权限，可能需要请求 Azure AD 或订阅管理员来分配必需的权限，或者为 Kubernetes 群集预先创建一个服务主体。

还需安装并配置 Azure CLI 2.0.21 或更高版本。 若要查找版本，请运行 az --version。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-sp-with-aks-cluster"></a>使用 AKS 群集创建 SP

使用 `az aks create` 命令部署 AKS 群集时，可以选择自动生成服务主体。

在以下示例中，已经创建了 AKS 群集，但由于未指定现有的服务主体，因此为群集创建了一个服务主体。 若要完成此操作，帐户必须具有创建服务主体所需的相应权限。

```azurecli
az aks create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>使用现有的 SP

可以使用现有的 Azure AD 服务主体，也可以预先创建一个适用于 AKS 群集的服务主体。 从 Azure 门户部署群集时，这很有用，因为需提供服务主体信息。

使用现有的服务主体时，该主体必须符合以下要求：

- 范围：用于部署群集的订阅
- 角色：参与者
- 客户端机密：必须是密码

## <a name="pre-create-a-new-sp"></a>预先创建新的 SP

若要通过 Azure CLI 来创建服务主体，请使用 [az ad sp create-for-rbac]() 命令。

```azurecli
id=$(az account show --query id --output tsv)
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$id"
```

输出如下所示。 记下 `appId` 和 `password`。 创建 AKS 群集时，将使用这些值。

```
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>使用现有的 SP

使用预先创建的服务主体时，请提供 `appId` 和 `password` 作为 `az aks create` 命令的参数值。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8SCluster --service-principal <appId> ----client-secret <password>
```

如果是从 Azure 门户部署 AKS 群集，请在 AKS 群集配置窗体中输入这些值。

![浏览到 Azure Vote 的图像](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>其他注意事项

使用 AKS 和 Azure AD 服务主体时，请牢记以下注意事项。

* Kubernetes 的服务主体是群集配置的一部分。 但是，请勿使用标识来部署群集。
* 每个服务主体都与一个 Azure AD 应用程序相关联。 Kubernetes 群集的服务主体可以与任何有效的 Azure AD 应用程序名称（例如 `https://www.contoso.org/example`）相关联。 应用程序的 URL 不一定是实际的终结点。
* 指定服务主体的“客户端 ID”时，可以使用 `appId` 的值（如本文所示）或相应的服务主体 `name`（例如，`https://www.contoso.org/example`）。
* 在 Kubernetes 群集的主 VM 和节点 VM 中，服务主体凭据存储在 /etc/kubernetes/azure.json 文件中。
* 使用 `az aks create` 命令自动生成服务主体时，会将服务主体凭据写入用于运行命令的计算机上的 ~/.azure/acsServicePrincipal.json 文件中。
* 使用 `az aks create` 命令自动生成服务主体时，服务主体也可以使用在同一订阅中创建的 [Azure 容器注册表](../container-registry/container-registry-intro.md)进行身份验证。

## <a name="next-steps"></a>后续步骤

有关 Azure Active Directory 服务主体的详细信息，请参阅 Azure AD 应用程序文档。

> [!div class="nextstepaction"]
> [应用程序对象和服务主体对象](../active-directory/develop/active-directory-application-objects.md)
