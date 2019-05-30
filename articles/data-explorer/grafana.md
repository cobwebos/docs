---
title: 使用 Grafana 可视化 Azure 数据资源管理器中的数据
description: 在本操作指南中，你将了解如何将 Azure 数据资源管理器设置为 Grafana 的数据源，然后将来自示例群集的数据可视化。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 135f8f1c9c352f9d2307a8bf9ad1bec892aac179
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399913"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>在 Grafana 中可视化 Azure 数据资源管理器中的数据

Grafana 是一个分析平台，可用于查询和可视化数据，然后根据可视化结果创建和共享仪表板。 Grafana 提供一个 Azure 数据资源管理器插件，通过该插件可连接到 Azure 数据资源管理器并可视化数据  。 本文介绍如何将 Azure 数据资源管理器设置为 Grafana 的数据源，然后将来自示例群集的数据可视化。

## <a name="prerequisites"></a>必备组件

需要以下各项来完成这一过程：

* 适用于操作系统的 [Grafana 版本 5.3.0 或更高版本](https://docs.grafana.org/installation/)

* 适用于 Grafana 的 [Azure 数据资源管理器插件](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation)

* 包括 StormEvents 示例数据的群集。 有关详细信息，请参阅[快速入门：创建 Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)和[将示例数据引入到 Azure 数据资源管理器](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>配置数据源

执行以下步骤将 Azure 数据资源管理器配置为 Grafana 的数据源。 我们将在本节中更详细地介绍这些步骤：

1. 创建 Azure Active Directory (Azure AD) 服务主体。 Grafana 使用服务主体来访问 Azure 数据资源管理器服务。

1. 将 Azure AD 服务主体添加到 Azure 数据资源管理器数据库中的“查看者”角色  。

1. 根据 Azure AD 服务主体中的信息指定 Grafana 连接属性，然后测试连接。

### <a name="create-a-service-principal"></a>创建服务主体

可在 [Azure 门户](#azure-portal)中或使用 [Azure CLI](#azure-cli) 命令行创建服务主体。 无论使用哪种方法，创建后都会获得四个连接属性的值，将在后面的步骤中用到这些值。

#### <a name="azure-portal"></a>Azure 门户

1. 要创建服务主体，请按照 [Azure 门户文档](/azure/active-directory/develop/howto-create-service-principal-portal)中的说明进行操作。

    1. 在[将应用程序分配给角色](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)部分，将“读取者”的角色类型分配给 Azure 数据资源管理器群集  。

    1. 在[获取用于登录的值](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)部分中，复制步骤中的三个属性值：目录 ID（租户 ID）、应用程序 ID 和密码    。

1. 在 Azure 门户中，选择“订阅”，然后复制在其中创建服务主体的订阅的 ID  。

    ![订阅 ID - 门户](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. 创建服务主体。 设置适用范围和角色类型 `reader`。

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    有关详细信息，请参阅[使用 Azure CLI 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)。

1. 该命令返回如下结果集。 复制三个属性值：appID、密码和租户    。

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. 获取订阅的列表。

    ```azurecli
    az account list --output table
    ```

    复制相应的订阅 ID。

    ![订阅 ID - CLI](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>将服务主体添加到查看者角色

现在已有服务主体，可将其添加到 Azure 数据资源管理器数据库中的“查看者”角色  。 可在 Azure 门户中的“权限”下执行此任务，也可以使用管理命令在“查询”下执行此任务   。

#### <a name="azure-portal---permissions"></a>Azure 门户 - 权限

1. 在 Azure 门户中，转到 Azure 数据资源管理器群集。

1. 在“概述”部分中，选择包含 StormEvents 样本数据的数据库  。

    ![选择数据库](media/grafana/select-database.png)

1. 选择“权限”，然后选择“添加”   。

    ![数据库权限](media/grafana/database-permissions.png)

1. 在“添加数据库权限”下，选择“查看者”角色，然后选择“选择主体”    。

    ![添加数据库权限](media/grafana/add-permission.png)

1. 搜索已创建的服务主体（示例显示主体 mb-grafana）  。 选择主体，然后单击“选择”  。

    ![在 Azure 门户中管理权限](media/grafana/new-principals.png)

1. 选择“保存”。 

    ![在 Azure 门户中管理权限](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>管理命令 - 查询

1. 在 Azure 门户中，转到 Azure 数据资源管理器群集，然后选择“查询”  。

    ![Query](media/grafana/query.png)

1. 在查询窗口中运行以下命令。 使用 Azure 门户或 CLI 中的应用程序 ID 和租户 ID。

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    该命令返回如下结果集。 在此示例中，第一行用于数据库中的现有用户，第二行用于刚刚添加的服务主体。

    ![结果集](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>指定属性并测试连接

将服务主体分配给“查看者”角色后，现可在 Grafana 实例中指定属性，并测试与 Azure 数据资源管理器的连接  。

1. 在 Grafana 的左侧菜单中，选择齿轮图标，然后选择“数据源”  。

    ![数据源](media/grafana/data-sources.png)

1. 选择“添加数据源”  。

1. 在“数据源/新建”页面上，输入数据源的名称，然后选择类型“Azure 数据资源管理器数据源”   。

    ![连接名称和类型](media/grafana/connection-name-type.png)

1. 输入群集的名称，名称采用 https://{ClusterName}.{Region}.kusto.windows.net 的形式。 输入 Azure 门户或 CLI 中的其他值。 有关映射，请参阅下图中的表。

    ![连接属性](media/grafana/connection-properties.png)

    | Grafana UI | Azure 门户 | Azure CLI |
    | --- | --- | --- |
    | 订阅 ID | 订阅 ID | SubscriptionId |
    | 租户 ID | Directory ID | tenant |
    | 客户端 ID | 应用程序 ID | appId |
    | 客户端机密 | 密码 | password |
    | | | |

1. 选择“保存并测试”  。

    如果测试成功，请转到下一部分。 若遇到任何问题，请检查在 Grafana 中指定的值，并查看先前的步骤。

## <a name="visualize-data"></a>可视化数据

已将 Azure 数据资源管理器配置为 Grafana 的数据源，现在即可进行数据可视化。 这里演示一个基本例子，除例子所示，还有很多其他可执行的操作。 建议查看[为 Azure 数据资源管理器编写查询](write-queries.md)，以获取针对示例数据集运行的其他查询的示例。

1. 在 Grafana 的左侧菜单中，选择加号图标，然后选择“仪表板”  。

    ![创建仪表板](media/grafana/create-dashboard.png)

1. 在“添加”选项卡下选择“图形”   。

    ![添加图形](media/grafana/add-graph.png)

1. 在图形面板上，选择“面板标题”，然后选择“编辑”   。

    ![编辑面板](media/grafana/edit-panel.png)

1. 在面板底部，选择“数据源”，然后选择所配置的数据源  。

    ![选择数据源](media/grafana/select-data-source.png)

1. 在查询窗格中，复制以下查询，然后选择“运行”  。 查询会按天为示例数据集统计事件计数。

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![运行查询](media/grafana/run-query.png)

1. 该图表未显示任何结果，因为默认情况下其显示范围为过去六小时的数据。 在顶部菜单上，选择“过去 6 小时”  。

    ![过去 6 小时](media/grafana/last-six-hours.png)

1. 指定涵盖 2007 年的自定义范围，即 StormEvents 示例数据集中包含的年份。 选择“应用”。 

    ![自定义日期范围](media/grafana/custom-date-range.png)

    现在，图表显示了 2007 年的数据，按日统计。

    ![已完成的图表](media/grafana/finished-graph.png)

1. 在顶部菜单上，选择保存图标： ![“保存”图标](media/grafana/save-icon.png).

## <a name="next-steps"></a>后续步骤

* [Azure 数据资源管理器的编写查询](write-queries.md)

* [教程：在 Power BI 中可视化 Azure 数据资源管理器中的数据](visualize-power-bi.md)