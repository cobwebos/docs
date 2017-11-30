---
title: "Azure 快速入门 - 使用 Azure CLI 创建存储帐户 | Microsoft Docs"
description: "快速了解如何使用 Azure CLI 创建新的存储帐户。"
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/28/2017
ms.author: marsma
ms.openlocfilehash: 7186c5e2ce94d06b21d95a557e960b82e268cdce
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>使用 Azure CLI 创建存储帐户

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 此快速入门详细介绍如何使用 Azure CLI 创建 Azure 存储帐户。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="create-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 此示例在 eastus 区域中创建一个名为 myResourceGroup 的资源组。

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

如果不确定为 `--location` 参数指定哪个区域，可使用 [az account list-locations](/cli/azure/account#list) 命令检索订阅支持的区域列表。

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-standard-storage-account"></a>创建通用的标准存储帐户

不同的使用方案对应有多种类型的存储帐户，每种类型都可支持一项或多项存储服务（Blob、文件、表或队列）。 下表详细介绍可用的存储帐户类型。

|存储帐户的类型|通用标准|通用高级|Blob 存储（热访问层和冷访问层）|
|-----|-----|-----|-----|
|支持的服务| Blob、文件、表和队列服务 | Blob 服务 | Blob 服务|
|支持的 Blob 类型|块 Blob、页 Blob 和追加 Blob | 页 Blob | 块 Blob 和追加 Blob|

使用 [az storage account create](/cli/azure/storage/account#create) 命令创建通用标准存储帐户。

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要你的资源组中的任何一个资源（包括使用本教程创建的存储帐户），可使用 [az group delete](/cli/azure/group#delete) 命令删除该资源组。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，已创建一个资源组和一个通用标准存储帐户。 要了解如何向存储帐户和从存储帐户传输数据，请继续阅读 Blob 存储快速入门。

> [!div class="nextstepaction"]
> [使用 Azure CLI 将对象传输到 Azure Blob 存储和从 Azure Blob 存储传输对象](../blobs/storage-quickstart-blobs-cli.md)