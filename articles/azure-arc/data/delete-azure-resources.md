---
title: 从 Azure 中删除资源
description: 从 Azure 中删除资源
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 46c1df8a175657e20dce984d0c8825ae99170246
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761645"
---
# <a name="delete-resources-from-azure"></a>从 Azure 中删除资源

> [!NOTE]
>  本文中删除资源的选项不可逆！

> [!NOTE]
>  由于为启用了 Azure Arc 的数据服务提供的唯一连接模式当前是间接连接模式，因此从 Kubernetes 删除实例不会将其从 Azure 中删除，也不会从 Azure 中删除实例。  现在，删除资源的过程分为两步，以后会对此进行改进。  今后，Kubernetes 将是真实的来源，Azure 将更新以反映它。

在某些情况下，可能需要在 Azure 资源管理器 (ARM) 中手动删除启用了 Azure Arc 的数据服务资源。  可以使用以下任一选项删除这些资源。

- [从 Azure 中删除资源](#delete-resources-from-azure)
  - [删除整个资源组](#delete-an-entire-resource-group)
  - [删除资源组中的特定资源](#delete-specific-resources-in-the-resource-group)
  - [使用 Azure CLI 删除资源](#delete-resources-using-the-azure-cli)
    - [使用 Azure CLI 删除 SQL 托管实例资源](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [使用 Azure CLI 删除 PostgreSQL 超大规模服务器组资源](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [使用 Azure CLI 删除 Azure Arc 数据控制器资源](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [使用 Azure CLI 删除资源组](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>删除整个资源组
如果已为启用了 Azure Arc 的数据服务使用特定的专用资源组，并且想要删除该资源组中的 *所有内容* ，则可以删除该资源组，这将删除该资源组中的所有内容。  

可以通过执行以下操作，在 Azure 门户中删除资源组：

- 浏览到在其中创建了 Azure Arc 已启用数据服务资源的 Azure 门户中的资源组。
- 单击 " **删除资源组** " 按钮。
- 输入资源组名称并单击 " **删除**"，确认删除。

## <a name="delete-specific-resources-in-the-resource-group"></a>删除资源组中的特定资源

通过执行以下操作，可以在 Azure 门户的资源组中删除特定于 Azure Arc 的数据服务资源：

- 浏览到在其中创建了 Azure Arc 已启用数据服务资源的 Azure 门户中的资源组。
- 选择要删除的所有资源。
- 单击 "删除" 按钮。
- 键入 "是" 以确认删除，并单击 " **删除**"。

## <a name="delete-resources-using-the-azure-cli"></a>使用 Azure CLI 删除资源

可以使用 Azure CLI 删除启用了特定 Azure Arc 的数据服务资源。

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>使用 Azure CLI 删除 SQL 托管实例资源

若要使用 Azure CLI 从 Azure 中删除 SQL 托管实例资源，请在下面的命令中替换占位符值并运行。

```console
az resource delete --name <sql instance name> --resource-type Microsoft.AzureData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>使用 Azure CLI 删除 PostgreSQL 超大规模服务器组资源

若要使用 Azure CLI 从 Azure 中删除 PostgreSQL 超大规模服务器组资源，请在下面的命令中替换占位符值并运行。

```console
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>使用 Azure CLI 删除 Azure Arc 数据控制器资源

> [!NOTE]
> 删除 Azure Arc 数据控制器之前，应删除它所管理的所有数据库实例资源。

若要使用 Azure CLI 从 Azure 中删除 Azure Arc 数据控制器，请在下面的命令中替换占位符值并运行。

```console
az resource delete --name <data controller name> --resource-type Microsoft.AzureData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>使用 Azure CLI 删除资源组

你还可以使用 Azure CLI 来 [删除资源组](/azure/azure-resource-manager/management/delete-resource-group)。
