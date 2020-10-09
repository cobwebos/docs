---
title: 使用 Striim 将数据迁移到 Azure Cosmos DB SQL API 帐户
description: 了解如何使用 Striim 将数据从 Oracle 数据库迁移到 Azure Cosmos DB SQL API 帐户。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 5ce805fb302264a0c3907c006983f9d939a2908e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85262066"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>使用 Striim 将数据迁移到 Azure Cosmos DB SQL API 帐户
 
Azure marketplace 中的 Striim 映像提供从数据仓库和数据库到 Azure 的持续实时数据移动。 在移动数据时，可以执行内联的非规范化、数据转换、启用实时分析和数据报告方案。 Striim 可以轻松地开始将企业数据移动到 Azure Cosmos DB SQL API。 Azure 提供了一种 marketplace 产品，可让你轻松地部署 Striim 并将数据迁移到 Azure Cosmos DB。 

本文介绍如何使用 Striim 将数据从 **Oracle 数据库** 迁移到 **Azure Cosmos DB SQL API 帐户**。

## <a name="prerequisites"></a>先决条件

* 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 在本地运行的 Oracle 数据库，其中包含一些数据。

## <a name="deploy-the-striim-marketplace-solution"></a>部署 Striim marketplace 解决方案

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择 " **创建资源** "，在 Azure marketplace 中搜索 **Striim** 。 选择第一个选项，然后选择 " **创建**"。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" 窗格中，输入 **vm 用户名**、 **vm 密码** (此密码用于通过 SSH 连接到 VM) 。 选择你想要在其中部署 Striim 的 **订阅**、 **资源组**和 **位置详细信息** 。 完成后，选择 **"确定"**。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" **管理员密码** "。 配置 VNET 和子网 () 选择默认值。 填写详细信息后，请选择 **"确定"** 继续。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" 来创建 Striim 实例。 

## <a name="configure-the-source-database"></a>配置源数据库 

在本部分中，将 Oracle 数据库配置为数据移动的源。  需要 [ORACLE JDBC 驱动程序](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) 连接到 oracle。 若要读取源 Oracle 数据库中的更改，可以使用 [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) 或 [XStream api](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)。 要读取、写入或保存 Oracle 数据库中的数据，Striim 的 Java 类路径中必须存在 Oracle JDBC 驱动程序。

将 [ojdbc8](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) 驱动程序下载到你的本地计算机上。 稍后会将其安装在 Striim 群集中。

## <a name="configure-the-target-database"></a>配置目标数据库

在本部分中，会将 Azure Cosmos DB SQL API 帐户配置为数据移动目标。

1. 使用 Azure 门户创建 [AZURE COSMOS DB SQL API 帐户](create-cosmosdb-resources-portal.md) 。

1. 导航到 Azure Cosmos 帐户中的 " **数据资源管理器** " 窗格。 选择 " **新建容器** " 以创建一个新容器。 假设要将 *产品* 和 *订单* 数据从 Oracle 数据库迁移到 Azure Cosmos DB。 使用名为**Orders**的容器创建名为**StriimDemo**的新数据库。 使用 **1000** ru 预配容器 (此示例使用 1000 ru，但应使用工作负荷的估计吞吐量) ，并 **/ORDER_ID** 作为分区键。 根据源数据的不同，这些值会有所不同。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>将 Oracle 配置为 Azure Cosmos DB 数据流

1. 现在，让我们返回到 Striim。 与 Striim 交互之前，请先安装先前下载的 Oracle JDBC 驱动程序。

1. 导航到 Azure 门户中部署的 Striim 实例。 选择上部菜单栏中的 " **连接** " 按钮，然后从 " **SSH** " 选项卡中，复制 " **使用 VM 本地帐户登录** " 字段中的 URL。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息":::

1. 打开新的终端窗口，并运行从 Azure 门户复制的 SSH 命令。 本文使用 MacOS 中的终端，你可以在 Windows 计算机上使用 PuTTY 或其他 SSH 客户端遵循类似的说明。 出现提示时，键入 **yes** 以继续，并输入在上一步中为虚拟机设置的 **密码** 。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" 选项卡，复制先前下载的 **ojdbc8** 文件。 使用以下 SCP 命令将 jar 文件从本地计算机复制到 Azure 中运行的 Striim 实例的 tmp 文件夹中：

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息":::

1. 接下来，向后导航到已在其中将 SSH 连接到 Striim 实例并以 sudo 身份登录的窗口。 将 **ojdbc8** 文件从 **/tmp** 目录移到 Striim 实例的 **lib** 目录中，并提供以下命令：

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息":::


1. 在同一终端窗口中，通过执行以下命令重新启动 Striim 服务器：

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim 将需要一分钟时间才能启动。 若要查看状态，请运行以下命令： 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. 现在，导航回 Azure 并复制 Striim VM 的公共 IP 地址。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息":::

1. 若要导航到 Striim 的 Web UI，请在浏览器中打开新选项卡，然后复制公共 IP 后跟：9080。 使用 **管理员** 用户名以及您在 Azure 门户中指定的管理员密码进行登录。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" 按钮。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" 以从现有模板开始。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" **Oracle CDC "以 Azure Cosmos DB**。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" 作为读取 Oracle 数据的读取器。 选择“下一步”继续。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" **下一步**"。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" **下一步**"。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" **下一步**"。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" 按钮来部署应用程序。
 
   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" 窗口中，可以指定是否要在部署拓扑的特定部分上运行应用程序的某些部分。 由于我们通过 Azure 在简单的部署拓扑中运行，因此我们将使用默认选项。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息" **启动应用**"。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-app.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息":::

1. 通过使用 **CDC (变更数据捕获) ** 读取器，Striim 只会选取数据库的新更改。 如果数据流经了源表，就会看到它。 不过，由于这是一个演示表，因此源未连接到任何应用程序。 如果使用示例数据生成器，可以将事件链插入 Oracle 数据库。

1. 你将看到数据流经 Striim 平台。 Striim 还会选取与表关联的所有元数据，这对于监视数据和确保数据落在适当的目标上非常有用。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息":::

1. 最后，让我们登录 Azure 并导航到 Azure Cosmos 帐户。 刷新数据资源管理器，可以看到数据已到达。  

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png" alt-text="查找 Striim marketplace 项&quot;:::

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 &quot; **基本** 信息":::

使用 Azure 中的 Striim 解决方案，可以将数据持续迁移到来自各种源（例如 Oracle、Cassandra、MongoDB）的 Azure Cosmos DB，并 Azure Cosmos DB。 若要了解详细信息，请访问 [Striim 网站](https://www.striim.com/)， [下载 Striim 的免费30天试用版](https://go2.striim.com/download-free-trial)，并在使用 Striim 设置迁移路径时遇到任何问题，并提供 [支持请求。](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>后续步骤

* 如果要将数据迁移到 Azure Cosmos DB SQL API，请参阅 [如何使用 Striim 将数据迁移到 Cassandra API 帐户](cosmosdb-cassandra-api-migrate-data-striim.md)

* [用 Azure Cosmos DB 度量值监视和调试数据](use-metrics.md)
