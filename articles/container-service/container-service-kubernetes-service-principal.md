---
title: "Azure Kubernetes 群集的服务主体 | Microsoft Docs"
description: "通过 Kubernetes 在 Azure 容器服务群集中创建和管理 Azure Active Directory 服务主体"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 5af9b5fdaf228edd54900855d0eac5d90ea3db38
ms.openlocfilehash: 0121896aa27677080d6b240fdafff3c7e19683d9


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>关于 Azure 容器服务中 Kubernetes 群集的 Azure Active Directory 服务主体



在 Azure 容器服务中，Kubernetes 需要 [Azure Active Directory 服务主体](../active-directory/active-directory-application-objects.md)作为服务帐户才能与 Azure API 交互。 需要服务主体才能动态管理相关资源，例如用户定义路由和第 4 层 Azure Load Balancer。

本文介绍用于为 Kubernetes 群集指定服务主体的不同选项。 例如，如果已安装并设置 [Azure CLI 2.0（预览版）](https://docs.microsoft.com/cli/azure/install-az-cli2)，则可运行 [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) 命令，以便同时创建 Kubernetes 群集和服务主体。

> [!NOTE]
> Azure 容器服务中的 Kubernetes 支持当前为预览版。


## <a name="requirements-for-the-service-principal"></a>服务主体的的要求

下面是 Azure 容器服务中 Kubernetes 群集的 Azure Active Directory 服务主体的要求。 

* **范围**：在其中部署了群集的 Azure 订阅

* **角色**：**参与者**

* **客户端机密**：必须是密码。 目前，无法使用针对证书身份验证设置的服务主体。

> [!NOTE]
> 每个服务主体都与一个 Azure Active Directory 应用程序相关联。 Kubernetes 群集的服务主体可以与任何有效的 Azure Active Directory 应用程序名称相关联。
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>Kubernetes 群集的服务主体选项

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>选项 1：传递服务主体客户端 ID 和客户端机密

当用户创建 Kubernetes 群集时，提供现有服务主体的“客户端 ID”（通常称为 `appId`，即应用程序 ID）和“客户端机密”(`password`) 作为参数。 若要使用现有的服务主体，请确保其满足上一部分的要求。 如需创建服务主体，请参阅本文后面的[创建服务主体](#create-a-service-principal-in-azure-active-directory)。

可以在使用门户、Azure 命令行界面 (CLI) 2.0（预览版）、Azure PowerShell 等方法[部署 Kubernetes 群集](./container-service-deployment.md)时指定这些参数。

>[!TIP] 
>指定“客户端 ID”时，请确保使用服务主体的 `appId` 而不是 `ObjectId`。
>

以下示例说明了一种通过 Azure CLI 2.0 预览版（参见[安装和设置说明](/cli/azure/install-az-cli2)）传递参数的方法。 此示例使用 [Kubernetes 快速启动模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)。

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


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20-preview"></a>选项 2：在通过 Azure CLI 2.0（预览版）创建群集时生成服务主体

如果已安装并设置 [Azure CLI 2.0（预览版）](https://docs.microsoft.com/cli/azure/install-az-cli2)，则可运行 [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) 命令以[创建群集](./container-service-create-acs-cluster-cli.md)。

与其他 Kubernetes 群集创建选项一样，可以在运行 `az acs create` 时指定现有服务主体的参数。 不过，在省略这些参数时，Azure 容器服务会自动创建服务主体。 该操作以透明方式在部署过程中进行。 

以下命令创建 Kubernetes 群集，并生成 SSH 密钥和服务主体凭据：

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>在 Azure Active Directory 中创建服务主体

如需在 Azure Active Directory 中创建用于 Kubernetes 群集的服务主体，可以使用 Azure 提供的多种方法。 

以下示例性命令演示了如何通过 [Azure CLI 2.0（预览版）](https://docs.microsoft.com/cli/azure/install-az-cli2)执行此操作。 也可使用 [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md) 或[经典门户](../azure-resource-manager/resource-group-create-service-principal-portal.md)等方法创建服务主体。

> [!IMPORTANT]
> 请确保查看本文前面针对服务主体的要求。
>

```azurecli
az login

az account set --subscription "mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

此操作会返回类似下面的输出（此处以密文形式显示）：

![创建服务主体](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

突出显示的是“客户端 ID”(`appId`) 和“客户端机密”(`password`)，用作群集部署的服务主体参数。


确认服务主体，方法是打开新的 shell 并运行以下命令，在 `appId`、`password`、`tenant` 中进行替换：

```azurecli 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>其他注意事项


* 指定服务主体的“客户端 ID”时，可以使用 `appId` 的值（如本文所示）或相应的服务主体 `name`（例如，`https://www.contoso.org/example`）。

* 如果使用 `az acs create` 命令自动生成服务主体，则会将服务主体凭据写入用于运行命令的计算机上的 ~/.azure/acsServicePrincipal.json 文件中。

* 在 Kubernetes 群集的主 VM 和节点 VM 中，服务主体凭据存储在 /etc/kubernetes/azure.json 文件中。

## <a name="next-steps"></a>后续步骤

* [开始使用 Kubernetes](container-service-kubernetes-walkthrough.md)（位于容器服务群集中）。



<!--HONumber=Feb17_HO1-->


