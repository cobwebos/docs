---
title: 快速入门 - 使用 CLI 2.0 创建 Azure 媒体服务帐户 | Microsoft Docs
description: 按照本快速入门的步骤，创建 Azure 媒体服务帐户。
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: f7e5cb28f90466e9366c0a32e2a333e6823b9396
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779712"
---
# <a name="quickstart-create-an-azure-media-services-account"></a>快速入门：创建 Azure 媒体服务帐户

> [!NOTE]
> Azure 媒体服务 (2018-03-30) 的最新版本为预览版。 此版本也称为 v3。 

无论是开发人员还是媒体内容创建者，若要在 Azure 中存储、加密、编码、管理和流式传输媒体内容，都需要创建媒体服务帐户。 创建媒体服务帐户时，需要提供 Azure 存储帐户资源的 ID。 指定存储帐户会附加到媒体服务帐户。 此存储帐户资源必须位于媒体服务帐户所在的地理区域内。  

本快速入门介绍使用 CLI 2.0 创建新 Azure 媒体服务帐户的步骤。  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>登录 Azure

登录 [Azure 门户](http://portal.azure.com)并启动 CloudShell 以执行 CLI 命令，如后续步骤中所示。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 CLI，本主题要求使用 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="set-the-azure-subscription"></a>设置 Azure 订阅

在以下命令中，为媒体服务帐户提供想要使用的 Azure 订阅 ID。 导航到[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)即可查看有权访问的订阅列表。

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>创建 Azure 资源组

以下命令创建你希望其中包含存储和媒体服务帐户的资源组。 将 myresourcegroup 占位符替换为希望用于资源组的名称。

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

创建媒体服务帐户时，需要提供 Azure 存储帐户资源的 ID。 指定存储帐户会附加到媒体服务帐户。 

必须具有一个主存储帐户，并且可以拥有任意数量的与媒体服务帐户关联的辅助存储帐户。 媒体服务支持常规用途 v2 (GPv2) 或常规用途 v1 (GPv1) 帐户。 不允许将仅限 Blob **的帐户作为主帐户**。 若要了解存储帐户的详细信息，请参阅 [Azure 存储帐户选项](../../storage/common/storage-account-options.md)。 

以下命令创建将与媒体服务帐户（主要）相关联的存储帐户。 在以下脚本中，替换“storageaccountforams”占位符。 “account_name”的长度必须小于 24。

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>创建 Azure 媒体服务帐户

可以在下面找到创建新媒体服务帐户的 Azure CLI 命令。 只需替换以下突出显示的值：

* myamsaccountname
* myresourcegroup
* storageaccountforams

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组中的任何一个资源（包括在本快速入门中创建的媒体服务帐户），可删除该资源组。

在 CloudShell 中，执行以下命令：

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [流式传输文件](stream-files-dotnet-quickstart.md)
