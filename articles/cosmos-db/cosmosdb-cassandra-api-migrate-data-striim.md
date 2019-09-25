---
title: 使用 Striim 将数据迁移到 Azure Cosmos DB Cassandra API 帐户
description: 了解如何使用 Striim 将数据从 Oracle 数据库迁移到 Azure Cosmos DB Cassandra API 帐户。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: af99f369245d6006d0d4784e572020b820e406a3
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266043"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>使用 Striim 将数据迁移到 Azure Cosmos DB Cassandra API 帐户

Azure marketplace 中的 Striim 映像提供从数据仓库和数据库到 Azure 的持续实时数据移动。 在移动数据时，可以执行内联的非规范化、数据转换、启用实时分析和数据报告方案。 Striim 可以轻松地开始将企业数据移动到 Azure Cosmos DB Cassandra API。 Azure 提供了一种 marketplace 产品，可让你轻松地部署 Striim 并将数据迁移到 Azure Cosmos DB。 

本文介绍如何使用 Striim 将数据从**Oracle 数据库**迁移到**Azure Cosmos DB Cassandra API 帐户**。

## <a name="prerequisites"></a>先决条件

* 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 在本地运行的 Oracle 数据库，其中包含一些数据。

## <a name="deploy-the-striim-marketplace-solution"></a>部署 Striim marketplace 解决方案

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择 "**创建资源**"，在 Azure marketplace 中搜索**Striim** 。 选择第一个选项，然后选择 "**创建**"。

   ![查找 Striim marketplace 项](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. 接下来，输入 Striim 实例的配置属性。 Striim 环境部署在虚拟机中。 在 "**基本**信息" 窗格中，输入**vm 用户名**、 **vm 密码**（此密码用于通过 SSH 连接到 VM）。 选择你想要在其中部署 Striim 的**订阅**、**资源组**和**位置详细信息**。 完成后，选择 **"确定"** 。

   ![配置 Striim 的基本设置](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. 在 " **Striim 群集设置**" 窗格中，选择 Striim 部署的类型和虚拟机大小。

   |设置 | 值 | 描述 |
   | ---| ---| ---|
   |Striim 部署类型 |独立 | Striim 可以在**独立**部署类型或**群集**部署类型中运行。 独立模式将在单个虚拟机上部署 Striim 服务器，并且可以根据数据量选择 Vm 的大小。 群集模式将在具有所选大小的两个或多个 Vm 上部署 Striim 服务器。 具有2个以上节点的群集环境提供自动高可用性和故障转移。</br></br> 在本教程中，可以选择 "独立" 选项。 使用默认的 "Standard_F4s" 大小 VM。 | 
   | Striim 群集的名称|    < Striim_cluster_Name >|  Striim 群集的名称。|
   | Striim 群集密码|   < Striim_cluster_password >|  群集的密码。|

   填写表单后，请选择 **"确定"** 继续。

1. 在 " **Striim 访问设置**" 窗格中，配置要用于登录到 Striim UI 的**公共 IP 地址**（选择默认值）、**域名 Striim**、**管理员密码**。 配置 VNET 和子网（选择默认值）。 填写详细信息后，请选择 **"确定"** 继续。

   ![Striim 访问设置](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure 将验证部署并确保一切正常;验证需要几分钟才能完成。 验证完成后，选择 **"确定"** 。
  
1. 最后，查看使用条款，然后选择 "**创建**" 来创建 Striim 实例。 

## <a name="configure-the-source-database"></a>配置源数据库

在本部分中，将 Oracle 数据库配置为数据移动的源。  需要[ORACLE JDBC 驱动程序](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html)连接到 oracle。 若要读取源 Oracle 数据库中的更改，可以使用[LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html)或[XStream api](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)。 要读取、写入或保存 Oracle 数据库中的数据，Striim 的 Java 类路径中必须存在 Oracle JDBC 驱动程序。

将[ojdbc8](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html)驱动程序下载到你的本地计算机上。 稍后会将其安装在 Striim 群集中。

## <a name="configure-target-database"></a>配置目标数据库

在本部分中，你将配置 Azure Cosmos DB Cassandra API 帐户作为数据移动的目标。

1. 使用 Azure 门户创建[Azure Cosmos DB Cassandra API 帐户](create-cassandra-dotnet.md#create-a-database-account)。

1. 导航到 Azure Cosmos 帐户中的 "**数据资源管理器**" 窗格。 选择 "**新建表**" 以创建新的容器。 假设要将*产品*和*订单*数据从 Oracle 数据库迁移到 Azure Cosmos DB。 使用 Orders 容器创建名为**StriimDemo**的新密钥空间。 预配包含**1000**ru 的容器（本示例使用 1000 ru，但应使用工作负荷估计的吞吐量），并使用 **/ORDER_ID**作为主键。 根据源数据的不同，这些值会有所不同。 

   ![创建 Cassandra API 帐户](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>将 Oracle 配置为 Azure Cosmos DB 数据流

1. 现在，让我们返回到 Striim。 与 Striim 交互之前，请先安装先前下载的 Oracle JDBC 驱动程序。

1. 导航到 Azure 门户中部署的 Striim 实例。 选择上部菜单栏中的 "**连接**" 按钮，然后从 " **SSH** " 选项卡中，复制 "**使用 VM 本地帐户登录**" 字段中的 URL。

   ![获取 SSH URL](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. 打开新的终端窗口，并运行从 Azure 门户复制的 SSH 命令。 本文使用 MacOS 中的终端，你可以在 Windows 计算机上使用 PuTTY 或其他 SSH 客户端遵循类似的说明。 出现提示时，键入**yes**以继续，并输入在上一步中为虚拟机设置的**密码**。

   ![连接到 Striim VM](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. 现在，请打开一个新的 "终端" 选项卡，复制先前下载的**ojdbc8**文件。 使用以下 SCP 命令将 jar 文件从本地计算机复制到 Azure 中运行的 Striim 实例的 tmp 文件夹中：

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![将 Jar 文件从位置计算机复制到 Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. 接下来，向后导航到已在其中将 SSH 连接到 Striim 实例并以 sudo 身份登录的窗口。 将**ojdbc8**文件从 **/tmp**目录移到 Striim 实例的**lib**目录中，并提供以下命令：

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![将 Jar 文件移动到 lib 文件夹](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


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

   ![复制 Striim VM IP 地址](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. 若要导航到 Striim 的 Web UI，请在浏览器中打开新的选项卡，然后复制公共 IP，后跟：9080。 使用**管理员**用户名以及您在 Azure 门户中指定的管理员密码进行登录。

   ![登录到 Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. 现在，你将到达 Striim 的主页。 有三个不同的窗格-**仪表板**、**应用**和**SourcePreview**。 通过 "仪表板" 窗格，您可以实时移动数据并对其进行可视化处理。 "应用" 窗格包含流式处理数据管道或数据流。 页面右侧为 SourcePreview，可以在移动数据前预览数据。

1. 选择 "**应用**" 窗格，此时我们将重点介绍此窗格。 你可以使用多种示例应用来了解 Striim，但在本文中，你将创建自己的应用。 选择右上角的 "**添加应用程序**" 按钮。

   ![添加 Striim 应用](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. 有几种不同的方法可用于创建 Striim 应用程序。 对于此方案，请选择 "**从头开始**"。

   ![从头开始应用程序](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. 为应用程序提供一个友好名称，如**oraToCosmosDB** ，然后选择 "**保存**"。

   ![创建新应用程序](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. 你将进入流设计器，你可以在该设计器中拖放，以创建流式处理应用程序。 在搜索栏中键入 " **oracle** "，将**Oracle CDC**源拖放到应用画布上。  

   ![Oracle CDC 源](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. 输入 Oracle 实例的源配置属性。 源名称只是 Striim 应用程序的命名约定，可以使用名称（如**src_onPremOracle**）。 还应输入其他详细信息，如适配器类型、连接 URL、用户名、密码和表名。 选择 "**保存**" 继续。

   ![配置源参数](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. 现在，单击流的 "波形" 图标，将目标 Azure Cosmos DB 实例连接起来。 

   ![连接到目标](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. 在配置目标之前，请确保已将一个[巴尔的摩根证书添加到 Striim 的 Java 环境](/azure/java/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store)中。

1. 输入目标 Azure Cosmos DB 实例的配置属性，然后选择 "**保存**" 以继续。 下面是要注意的关键参数：

   * **适配器**-使用**DatabaseWriter**。 写入 Azure Cosmos DB Cassandra API 时，DatabaseWriter 是必需的。 Cassandra 驱动程序3.6.0 与 Striim 捆绑在一起。 如果 DatabaseWriter 超过了 Azure Cosmos 容器上预配的 ru 数，应用程序会崩溃。

   * **连接 URL** -指定 Azure Cosmos DB JDBC 连接 url。 URL 的格式为`jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * **用户名**-指定你的 Azure Cosmos 帐户名称。
   
   * **密码**-指定 Azure Cosmos 帐户的主密钥。

   * **表**-目标表必须具有主键，不能更新主键。

   ![配置目标属性](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![配置目标属性](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. 现在，我们将继续运行 Striim 应用程序。 在上部菜单栏中，选择 "已**创建**"，然后**部署应用**。 在部署窗口中，可以指定是否要在部署拓扑的特定部分上运行应用程序的某些部分。 由于我们通过 Azure 在简单的部署拓扑中运行，因此我们将使用默认选项。

   ![部署应用](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. 现在，我们将继续预览流，以查看流过 Striim 的数据。 单击波形图标，并单击其旁边的眼睛图标。 部署后，可以预览流以查看流动的数据。 选择**波形**图标，然后选择它旁边的**盯住**。 选择顶部菜单栏中的 "已**部署**" 按钮，然后选择 "**启动应用**"。

   ![启动应用](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. 通过使用**CDC （变更数据捕获）** 读取器，Striim 只会选取数据库的新更改。 如果数据流经了源表，就会看到它。 不过，由于这是一个示例表，因此未连接到任何应用程序的源。 如果使用示例数据生成器，可以将事件链插入 Oracle 数据库。

1. 你将看到数据流经 Striim 平台。 Striim 还会选取与表关联的所有元数据，这对于监视数据和确保数据落在适当的目标上非常有用。

   ![设置 CDC 管道](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. 最后，让我们登录 Azure 并导航到 Azure Cosmos 帐户。 刷新数据资源管理器，可以看到数据已到达。 

使用 Azure 中的 Striim 解决方案，可以将数据持续迁移到来自各种源（例如 Oracle、Cassandra、MongoDB）的 Azure Cosmos DB，并 Azure Cosmos DB。 若要了解详细信息，请访问[Striim 网站](https://www.striim.com/)，[下载 Striim 的免费30天试用版](https://go2.striim.com/download-free-trial)，并在使用 Striim 设置迁移路径时遇到任何问题，并提供[支持请求。](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>后续步骤

* 如果要将数据迁移到 Azure Cosmos DB SQL API，请参阅[如何使用 Striim 将数据迁移到 Cassandra API 帐户](cosmosdb-sql-api-migrate-data-striim.md)

* [用 Azure Cosmos DB 度量值监视和调试数据](use-metrics.md)
