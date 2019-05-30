---
title: 从 Azure 数据资源管理器使用 Sisense 可视化数据
description: 在本文中，了解如何作为数据源为 Sisense，设置 Azure 数据资源管理器和可视化数据。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358178"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>从 Azure 数据资源管理器中 Sisense 可视化数据

Sisense 是一个分析业务智能平台，使您能够构建分析应用程序提供高度交互的用户体验。 商业智能和报告软件的仪表板，可访问，并将数据合并中几次单击。 可以连接到结构化和非结构化数据源、 联接中具有最小的脚本编写和编码，多个源的表和创建交互式 web 仪表板和报表。 在本文中，将了解如何作为数据源为 Sisense，设置 Azure 数据资源管理器和可视化的示例群集数据。

## <a name="prerequisites"></a>必备组件

若要完成本文，您需要：

* [下载并安装 Sisense 应用](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* 创建群集和包含 StormEvents 示例数据的数据库。 有关详细信息，请参阅[快速入门：创建 Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)和[将示例数据引入到 Azure 数据资源管理器](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>连接到使用 Azure 数据资源管理器 JDBC 连接器 Sisense 仪表板

1. 下载并复制到以下 jar 文件的最新版本 *...\Sisense\DataConnectors\jdbcdrivers\adx* 

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
    
1. 打开**Sisense**应用。
1. 选择**数据**选项卡并选择 **+ ElastiCube**以创建新的 ElastiCube 模型。
    
    ![选择 ElastiCube](media/sisense/data-select-elasticube.png)

1. 在中**添加新 ElastiCube 模型**，ElastiCube 模型命名并**保存**。
   
    ![添加新 ElastiCube 模型](media/sisense/add-new-elasticube-model.png)

1. 选择 **+ 数据**。

    ![选择数据按钮](media/sisense/select-data.png)

1. 在中**选择连接器**选项卡上，选择**泛型 JDBC**连接器。

    ![选择 JDBC 连接器](media/sisense/select-connector.png)

1. 在中**Connect**选项卡上，完成以下字段用于**泛型 JDBC**连接器，然后选择**下一步**。

    ![JDBC 连接器设置](media/sisense/jdbc-connector.png)

    |字段 |描述 |
    |---------|---------|
    |连接字符串     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC Jar 文件夹  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |驱动程序的类名称    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |用户名   |    AAD 用户名     |
    |密码     |   AAD 用户密码      |

1. 在中**选择数据**选项卡上，搜索**选择数据库**选择对其具有权限的相关数据库。 在此示例中，选择*test1*。

    ![选择数据库](media/sisense/select-database.png)

1. 在中*测试*（数据库名称） 窗格中：
    1. 选择要预览表，请参阅表列名的表名称。 您可以删除不必要的列。
    1. 选择要选择的表的相关表的复选框。 
    1. 选择“完成”  。

    ![选择表](media/sisense/select-table-see-columns.png)    

1. 选择**生成**构建你的数据集。 

    * 在中**构建**窗口中，选择**生成**。

      ![生成窗口](media/sisense/build-window.png)

    * 等待，直到生成过程已完成，然后选择**生成成功**。

      ![已成功生成](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>创建 Sisense 仪表板

1. 在中**Analytics**选项卡上，选择 **+**  > **新仪表板**此数据集上创建仪表板。

    ![新建仪表板](media/sisense/new-dashboard.png)

1. 选择仪表板，并选择**创建**。 

    ![创建仪表板](media/sisense/create-dashboard.png)

1. 下**新的小组件**，选择 **+ 选择数据**若要创建新的小组件。 

    ![将字段添加到 StormEvents 仪表板](media/sisense/storm-dashboard-add-field.png)  

1. 选择 **+ 添加更多数据**向图形添加其他列。 

    ![向关系图添加更多的数据](media/sisense/add-more-data.png)

1. 选择 **+ 小组件**创建另一个小组件。 拖放以重新排列您的仪表板的小组件。

    ![Storm 仪表板](media/sisense/final-dashboard.png)

现在可以浏览您的数据与视觉对象分析、 生成其他仪表板和数据转换为可操作见解以对你的业务产生的影响。

## <a name="next-steps"></a>后续步骤

* [Azure 数据资源管理器的编写查询](write-queries.md)

