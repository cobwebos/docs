---
title: 快速入门：创建 Azure 数据资源管理器群集和数据库
description: 在本快速入门中，了解如何创建 Azure 数据资源管理器群集和数据库，以及如何引入（加载）数据。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: 41a15a29798953cb32029b7c4d1167020074e49f
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046030"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>快速入门：创建 Azure 数据资源管理器群集和数据库

> [!div class="op_single_selector"]
> * [门户](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 若要使用 Azure 数据资源管理器，请先创建群集，再在该群集中创建一个或多个数据库。 然后将数据引入（加载）到数据库，以便对其运行查询。 在本快速入门中，你将创建群集和数据库。

如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-cluster"></a>创建群集

使用 Azure 资源组中的定义的一组计算和存储资源创建 Azure 数据资源管理器。

1. 选择门户左上角的“创建资源”按钮 (+)。

1. 搜索“Azure 数据资源管理器”。

   ![搜索资源](media/create-cluster-database-portal/search-resources.png)

1. 在屏幕底部“Azure 数据资源管理器”下，选择“创建”。

1. 使用以下信息填写基本群集详细信息。

   ![“创建群集”窗体](media/create-cluster-database-portal/create-cluster-form.png)

    **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 订阅 | 订阅 | 选择要用于群集的 Azure 订阅。|
    | 资源组 | *test-resource-group* | 使用现有资源组或创建新资源组。 |
    | 群集名称 | 唯一的群集名称 | 选择用于标识群集的唯一名称。 例如，mydataexplorercluster。 域名 *[region].kusto.windows.net* 将追加到所提供的群集名称。 名称只能包含小写字母和数字。 并且必须包含 3 到 22 个字符。
    | 位置 | *美国西部* | 为此快速入门选择美国西部。 对于生产系统，请选择最能满足你需求的区域。
    | 计算规格 | *D13_v2* | 对于本快速入门，请选择价格最低的规范。 对于生产系统，请选择最能满足你需求的规范。
    | | |

1. 选择“查看 + 创建”以查看群集的详细信息，选择“创建”以预配群集。 预配通常需要大约 10 分钟。

1. 部署完成后，选择“转到资源”。

    ![转到资源](media/create-cluster-database-portal/notification-resource.png)

## <a name="create-a-database"></a>创建数据库

现在可以执行过程中的第二步：创建数据库。

1. 在“概述”选项卡上，选择“创建数据库”。

    ![第 2 步：创建数据库](media/create-cluster-database-portal/database-creation.png)

1. 使用以下信息填写窗体。

    ![“创建数据库”窗体](media/create-cluster-database-portal/create-database.png)

    **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 数据库名称 | *TestDatabase* | 该数据库名称在群集中必须是唯一的。
    | 保留期 | *3650* | 保证数据可供查询的时间跨度（以天为单位）。 时间跨度从引入数据时算起。
    | 缓存期 | *31* | 使频繁查询的数据在 SSD 存储或 RAM（而不是更长期的存储）中保持可用的时间跨度（以天为单位）。
    | | | |

1. 选择“创建”以创建数据库。 创建过程通常需要不到一分钟的时间。 该过程完成后，将返回到群集的“概述”选项卡。

## <a name="run-basic-commands-in-the-database"></a>在数据库中运行基本命令

由于已有群集和数据库，因此可以运行查询和命令。 数据库中尚无任何数据，但仍可以查看工具的工作原理。

1. 在群集下，选择“查询”。 将命令 `.show databases` 粘贴到查询窗口中，然后选择“运行”。

    ![显示数据库命令](media/create-cluster-database-portal/show-databases.png)

    结果集显示“TestDatabase”，这是群集中的唯一数据库。

1. 将命令 `.show tables` 粘贴到查询窗口中，然后选择“运行”。

    此命令将返回空的结果集，因为你尚未拥有任何表。 将在本系列的下一篇文章中添加表。

## <a name="stop-and-restart-the-cluster"></a>停止并重新启动群集

可以停止并重新启动群集，具体取决于业务需求。

1. 若要停止群集，请在“概述”选项卡顶部选择“停止”。

    停止群集后，数据不可用于查询，并且无法引入新数据。

1. 若要重新启动群集，请在“概述”选项卡顶部选择“启动”。

    重新启动群集后，需要约 10 分钟才能使用（如同最初预配时那样）。 需要额外时间才能将数据加载到热缓存中。  

## <a name="clean-up-resources"></a>清理资源

如果计划学习其他快速入门和教程，请保留创建的资源。 否则，请清理资源组以避免产生费用。

1. 在 Azure 门户的最左侧选择“资源组”，然后选择包含 Azure 数据资源管理器群集的资源组。  

1. 选择“删除资源组”以删除整个资源组。 如果使用现有的资源组，可以选择仅删除数据资源管理器群集。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：将数据从事件中心引入到 Azure 数据资源管理器](ingest-data-event-hub.md)


