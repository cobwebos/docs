---
title: include 文件
description: include 文件
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: feec6a695ad867d26d32904d020648b029f9da35
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173040"
---
## <a name="create-a-media-services-account"></a>创建媒体服务帐户

首先需创建媒体服务帐户。 本部分介绍使用 Azure CLI 创建帐户所需的内容。

### <a name="create-a-resource-group"></a>创建资源组

使用以下命令创建资源组。 Azure 资源组是在其中部署和管理资源（例如 Azure 媒体服务帐户和关联的存储帐户）的逻辑容器。

你可以替换`amsResourceGroup`用你的值。

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>创建存储帐户

创建媒体服务帐户时，需要提供 Azure 存储帐户资源的名称。 指定存储帐户会附加到媒体服务帐户。 若要详细了解如何在媒体服务中使用存储帐户，请参阅[存储帐户](../articles/media-services/latest/storage-account-concept.md)。

必须具有一个主存储帐户，并且可以拥有任意数量的与媒体服务帐户关联的辅助存储帐户   。 媒体服务支持常规用途 v2 (GPv2) 或常规用途 v1 (GPv1) 帐户   。 不允许将仅限 Blob **的帐户作为主帐户**。 若要了解存储帐户的详细信息，请参阅 [Azure 存储帐户选项](../articles/storage/common/storage-account-options.md)。 

在此示例中，我们创建一个常规用途 v2 标准 LRS 帐户。 若要通过存储帐户进行试验，请使用 `--sku Standard_LRS`。 但是，在选取用于生产的 SKU 时，应考虑 `--sku Standard_RAGRS`，以便通过异地复制确保业务连续性。 有关详细信息，请参阅[存储帐户](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)。
 
以下命令创建将与媒体服务帐户相关联的存储帐户。 在以下脚本中，可以将 `storageaccountforams` 替换为你的值。 `amsResourceGroup` 必须匹配你在上一步中的资源组为指定的值。 存储帐户名称必须具有长度小于 24。

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>创建媒体服务帐户

以下 Azure CLI 命令创建新的媒体服务帐户。 可以替换以下值：`amsaccount`  `storageaccountforams`（必须与提供给存储帐户的值相符）和 `amsResourceGroup`（必须与提供给资源组的值相符）。

```azurecli
az ams account create --name amsaccount \
  -l westus2 \
  -g amsResourceGroup --storage-account storageaccountforams
```
