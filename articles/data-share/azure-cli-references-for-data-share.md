---
title: Azure 数据共享的 Azure CLI 引用
description: Azure 数据共享 Azure CLI 引用登陆页面
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84302639"
---
# <a name="azure-cli-for-azure-data-share"></a>Azure 数据共享的 Azure CLI

Azure 命令行接口 ([Azure CLI](/cli/azure/what-is-azure-cli)) 是一组用来创建和管理 Azure 资源的命令。  它可在多个 Azure 服务（包括 Azure 数据共享）中使用。  数据共享有超过65的不同命令！  这些命令使你能够从命令行有效地使用服务。

## <a name="references-for-data-share"></a>数据共享的引用

Azure 数据共享的所有 Azure CLI 命令当前都是对 Azure CLI 的扩展。  扩展使你可以访问实验性和预发布命令。  在[使用 Azure CLI 的扩展](/cli/azure/azure-cli-extensions-overview)中详细了解扩展参考。

|Azure CLI 引用 |描述
|-|-|-|
| [az datashare](/cli/azure/ext/datashare/datashare) | 用于管理 Azure 数据共享的所有命令。
| [az datashare account](/cli/azure/ext/datashare/datashare/account) | 用于管理 Azure 数据共享帐户的命令。
| [az datashare 使用者](/cli/azure/ext/datashare/datashare/consumer) | 供使用者用于管理 Azure 数据共享的命令。
| [az datashare dataset](/cli/azure/ext/datashare/datashare/dataset) | 用于提供程序的命令，用于管理 Azure 数据共享数据集。
| [az datashare 邀请](/cli/azure/ext/datashare/datashare/invitation) | 供使用者用于管理 Azure 数据共享邀请的命令。
| [az datashare provider-share-订阅](/cli/azure/ext/datashare/datashare/provider-share-subscription) | 用于提供程序的命令，用于管理 Azure 数据共享订阅。
| [az datashare 同步](/cli/azure/ext/datashare/datashare/synchronization)  | 用于管理 Azure 数据共享同步的命令。
| [az datashare 同步-设置](/cli/azure/ext/datashare/datashare/synchronization-setting)  | 用于提供程序的命令，用于管理 Azure 数据共享同步设置。

## <a name="reference-examples"></a>参考示例

每个 Azure CLI 参考都提供了示例。 虽然还可通过 Azure 门户完成这些任务，但如果使用 Azure CLI，则只需一个命令行就能搞定。  下面是几个代码块，你可从中了解使用 Azure CLI 是多么的简单。

若要使用 Azure 数据共享，首先需要一个资源组。  使用 Azure CLI 可轻松创建和管理 Azure 资源组。  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

创建数据共享帐户相当简单。

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>另请参阅

* [Azure CLI 入门](/cli/azure/get-started-with-azure-cli)，了解如何进行安装和登录。

* 在 Azure CLI 文档中发现其他[核心](/cli/azure/reference-index)和[扩展](/cli/azure/azure-cli-extensions-list)参考。
