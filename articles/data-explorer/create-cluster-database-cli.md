---
title: 使用 CLI 创建 Azure 数据资源管理器群集和数据库
description: 本文介绍如何使用 Azure CLI 创建 Azure 数据资源管理器群集和数据库
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: howto
ms.date: 1/31/2019
ms.openlocfilehash: a4c9156ef80f05e247b1cfef0acd56b601a2db65
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812678"
---
# <a name="create-an-azure-data-explorer-cluster-and-a-database-using-cli"></a>使用 CLI 创建 Azure 数据资源管理器群集和数据库

本文介绍如何使用 Azure CLI 创建 Azure 数据资源管理器群集和数据库。

## <a name="whats-the-difference-between-the-management-plane-and-data-plane-apis"></a>管理平面和数据平面 API 之间的差异

有两种不同的 API 库：管理和数据平面 API。
管理 API 用于管理资源，例如，创建群集、创建数据库、删除数据连接、更改实例计数等。数据平面 API 用于与数据交互、运行查询、引入数据等。

## <a name="configure-the-cli-parameters"></a>配置 CLI 参数

登录到帐户

```Bash
az login
```

设置将在其中创建群集的订阅。

```Bash
az account set --subscription "your_subscription"
```

## <a name="create-the-azure-data-explorer-cluster"></a>创建 Azure 数据资源管理器群集

请使用以下命令创建群集。

```Bash
az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
```

提供以下值

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | 名称 | *azureclitest* | 所需的群集名称。|
   | sku | *D13_v2* | 适用于群集的 SKU。 |
   | resource-group | *testrg* | 将在其中创建群集的资源组名称。 |

如果需要，可以使用更多可选参数，例如群集的容量等。

要检查群集是否已成功创建，可以运行

```Bash
az kusto cluster show --name azureclitest --resource-group testrg
```

如果结果包含“provisioningState”与“Succeeded”值，则表示已成功创建群集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 数据资源管理器群集中创建数据库

请使用以下命令创建数据库。

```Bash
az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
```

提供以下值

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | cluster-name | *azureclitest* | 应创建的群集的名称。|
   | 名称 | *clidatabase* | 所需的数据库名称。|
   | resource-group | *testrg* | 将在其中创建群集的资源组名称。 |
   | soft-delete-period | *3650:00:00:00* | 数据应保存的时间，以便可以查询。 |
   | hot-cache-period | *3650:00:00:00* | 数据应保存在缓存中的时间。 |

可以看到所创建的数据库，方法是通过运行

```Bash
az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
```

这样，你就有了一个群集和数据库。

## <a name="clean-up-resources"></a>清理资源

如果计划学习其他快速入门和教程，请保留创建的资源。

删除群集时，也会删除其中的所有数据库。 使用以下命令可删除群集。

```Bash
az kusto cluster delete --name azureclitest --resource-group testrg
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：将数据从事件中心引入到 Azure 数据资源管理器](ingest-data-event-hub.md)
