---
title: 适用于 Azure Data Share 的 Azure CLI 参考
description: 适用于 Azure Data Share 的 Azure CLI 参考登陆页面
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298455"
---
# <a name="azure-cli-for-azure-data-share"></a>适用于 Azure Data Share 的 Azure CLI

Azure 命令行接口 ([Azure CLI](/cli/azure/what-is-azure-cli)) 是一组用来创建和管理 Azure 资源的命令。  它适用于很多 Azure 服务，包括 Azure Data Share。  数据共享有 65 个以上的不同命令！  使用这些命令可以从命令行高效使用服务。

## <a name="references-for-data-share"></a>Data Share 参考

适用于 Azure Data Share 的所有 Azure CLI 命令当前都是对 Azure CLI 的扩展。  使用扩展可以访问实验性的命令和预发布命令。  在[使用 Azure CLI 的扩展](/cli/azure/azure-cli-extensions-overview)中详细了解扩展参考。

|Azure CLI 参考 |说明
|-|-|-|
| [az datashare](/cli/azure/ext/datashare/datashare) | 所有用于管理 Azure Data Share 的命令。
| [az datashare account](/cli/azure/ext/datashare/datashare/account) | 用于管理 Azure Data Share 帐户的命令。
| [az datashare consumer](/cli/azure/ext/datashare/datashare/consumer) | 供使用者管理 Azure Data Share 的命令。
| [az datashare dataset](/cli/azure/ext/datashare/datashare/dataset) | 供提供者管理 Azure Data Share 数据集的命令。
| [az datashare invitation](/cli/azure/ext/datashare/datashare/invitation) | 供使用者管理 Azure Data Share 邀请的命令。
| [az datashare provider-share-subscription](/cli/azure/ext/datashare/datashare/provider-share-subscription) | 供提供者管理 Azure Data Share 订阅的命令。
| [az datashare synchronization](/cli/azure/ext/datashare/datashare/synchronization)  | 用于管理 Azure Data Share 同步的命令。
| [az datashare synchronization-setting](/cli/azure/ext/datashare/datashare/synchronization-setting)  | 供提供者管理 Azure Data Share 同步设置的命令。

## <a name="reference-examples"></a>参考示例

每个 Azure CLI 参考都提供了示例。 虽然还可通过 Azure 门户完成这些任务，但如果使用 Azure CLI，则只需一个命令行就能搞定。  下面是几个代码块，你可从中了解使用 Azure CLI 是多么的简单。

若要使用 Azure Data Share，首先需要有一个资源组。  使用 Azure CLI 可轻松创建和管理 Azure 资源组。  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

这与创建数据共享帐户一样简单。

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>另请参阅

* [Azure CLI 入门](/cli/azure/get-started-with-azure-cli)，了解如何进行安装和登录。

* 在 Azure CLI 文档中了解其他[核心](/cli/azure/reference-index)和[扩展](/cli/azure/azure-cli-extensions-list)参考。
