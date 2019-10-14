---
title: 如何在 Azure Spring Cloud 中使用持久性存储 | Microsoft Docs
description: 如何在 Azure Spring Cloud 中使用持久性存储
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038446"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>如何在 Azure Spring Cloud 中使用持久性存储

Azure Spring Cloud 为应用程序提供两种类型的存储：持久性和临时性。  Azure Spring Cloud 默认为每个应用程序实例启用临时存储。 临时存储有 5GB 的容量限制，其默认装载路径为 `/tmp`。

> [!WARNING]
> 重启应用程序实例会永久删除其关联的临时存储。

持久存储是由 Azure 托管的文件共享容器，其分配范围是按应用程序的。 存储在持久存储中的数据在应用程序的所有实例中共享。 一个 Azure Spring Cloud 服务实例最多有 10 个启用了持久磁盘的应用程序，每个应用程序获得 50GB 的持久存储。 持久存储的默认装载路径为 `/persistent`。

## <a name="enable-persistent-storage-using-the-azure-portal"></a>使用 Azure 门户启用持久存储

1. 在 Azure Spring Cloud 服务页中选择“应用程序仪表板”，然后选择需要持久存储的应用程序。 
1. 在“概览”选项卡上找到“持久存储”属性，然后选择“禁用”。   
1. 单击“启用”以启用持久存储，然后选择“确定”按钮，应用所做的更改。  

当持久存储启用以后，其大小和路径会显示在“概览”页的“持久存储”属性中。  

> [!WARNING]
> 禁用持久存储会解除分配该应用程序的存储。   该存储帐户中的所有数据会丢失。 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>使用 Azure CLI 启用持久存储

创建启用了持久磁盘的应用：
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

在现有应用中启用持久存储：

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

在现有应用中禁用持久存储：

> [!WARNING]
> 禁用持久存储会解除分配该应用程序的存储，永久丢失存储在其中的任何数据。 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>后续步骤

了解[应用程序和服务配额](spring-cloud-quotas.md)，或了解如何[手动缩放应用程序](spring-cloud-tutorial-scale-manual.md)。