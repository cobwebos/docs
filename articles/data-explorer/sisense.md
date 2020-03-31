---
title: 使用 Sisense 可视化 Azure 数据资源管理器中的数据
description: 本文介绍如何将 Azure 数据资源管理器设置为 Sisense 的数据源，然后将数据可视化。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "66358178"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>在 Sisense 中可视化 Azure 数据资源管理器中的数据

Sisense 是一个分析业务智能平台，用于构建可提供交互性极强的用户体验的分析应用。 有了业务智能和仪表板报告软件，只需单击几下即可访问和组合数据。 可以连接到结构化和非结构化的数据源、在几乎不编写脚本和代码的情况下联接多个源的表，以及创建交互式 Web 仪表板和报表。 本文介绍如何将 Azure 数据资源管理器设置为 Sisense 的数据源，并将来自示例群集的数据可视化。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要满足以下条件：

* [下载并安装 Sisense 应用](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* 创建包含 StormEvents 示例数据的群集和数据库。 有关详细信息，请参阅[快速入门：创建 Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)，并将[示例数据引入 Azure 数据资源管理器](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>使用 Azure 数据资源管理器 JDBC 连接器连接到 Sisense 仪表板

1. 下载以下 jar 文件的最新版并将其复制到 *..\Sisense\DataConnectors\jdbcdrivers\adx* 

    * activation-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. 打开 **Sisense** 应用。
1. 选择“数据”选项卡，然后选择“+ElastiCube”，创建新的 ElastiCube 模型。********
    
    ![选择 ElastiCube](media/sisense/data-select-elasticube.png)

1. 在“添加新的 ElastiCube 模型”中为 ElastiCube 模型命名，然后单击“保存”。********
   
    ![添加新的 ElastiCube 模型](media/sisense/add-new-elasticube-model.png)

1. 选择“+ 数据”。****

    ![“选择数据”按钮](media/sisense/select-data.png)

1. 在“选择连接器”选项卡中，选择“通用 JDBC”连接器。********

    ![选择 JDBC 连接器](media/sisense/select-connector.png)

1. 在“连接”选项卡中完成“通用 JDBC”连接器的以下字段，然后选择“下一步”。************

    ![JDBC 连接器设置](media/sisense/jdbc-connector.png)

    |字段 |描述 |
    |---------|---------|
    |连接字符串     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC JARs 文件夹  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |驱动程序的类名称    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |用户名   |    AAD 用户名     |
    |密码     |   AAD 用户密码      |

1. 在“选择数据”选项卡中搜索“选择数据库”，以便选择有权访问的相关数据库。******** 在此示例中，请选择“test1”**。

    ![选择数据库](media/sisense/select-database.png)

1. 在“test”（数据库名称）窗格中执行以下操作：**
    1. 选择表名，以便预览该表并查看表的列名。 可以删除不需要的列。
    1. 选择相关表的复选框，将该表选中。 
    1. 选择 **"完成**"。

    ![选择表](media/sisense/select-table-see-columns.png)    

1. 选择“生成”，以便生成数据集。**** 

    * 在“生成”**** 窗口中，选择“生成”****。

      ![“生成”窗口](media/sisense/build-window.png)

    * 等待生成过程完成，然后选择“生成成功”。****

      ![生成成功](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>创建 Sisense 仪表板

1. 在 **"分析"** 选项卡**+** > 中，选择 **"新建仪表板**"以在此数据集上创建仪表板。

    ![新建仪表板](media/sisense/new-dashboard.png)

1. 选取一个仪表板，然后选择“创建”。**** 

    ![创建仪表板](media/sisense/create-dashboard.png)

1. 在“新建小组件”**** 下选择“+ 选择数据”****，以便创建新的小组件。 

    ![向 StormEvents 仪表板添加字段](media/sisense/storm-dashboard-add-field.png)  

1. 选择“+ 添加更多数据”，向图形添加更多列。**** 

    ![向图添加更多数据](media/sisense/add-more-data.png)

1. 选择“+ 小组件”，创建另一个小组件。**** 通过拖放小组件重新排列仪表板中的项。

    ![Storm 仪表板](media/sisense/final-dashboard.png)

现在可以通过视觉分析来浏览数据，构建其他仪表板，以及将数据转换成能够对业务造成影响的可操作见解。

## <a name="next-steps"></a>后续步骤

* [为 Azure 数据资源管理器编写查询](write-queries.md)

