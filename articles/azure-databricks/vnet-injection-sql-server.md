---
title: 查询 Azure Databricks 笔记本中的虚拟网络中的 SQL Server Linux Docker 容器
description: 本文介绍如何将 Azure Databricks 部署到虚拟网络，也称为 VNet 注入。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288946"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>教程：查询 Azure Databricks 笔记本中的虚拟网络中的 SQL Server Linux Docker 容器

本教程讲解如何将 Azure Databricks 与虚拟网络中的 SQL Server Linux Docker 容器集成。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将 Azure Databricks 工作区部署到虚拟网络
> * 在公共网络中安装 Linux 虚拟机
> * 安装 Docker
> * 在 Linux docker 容器上安装 Microsoft SQL Server
> * 查询从 Databricks notebook 中使用 JDBC 的 SQL Server

## <a name="prerequisites"></a>必备组件

* 创建[虚拟网络中的 Databricks 工作区](quickstart-create-databricks-workspace-vnet-injection.md)。

* 安装[Windows 的 Ubuntu](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)。

* 下载 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)。

## <a name="create-a-linux-virtual-machine"></a>创建 Linux 虚拟机

1. 在 Azure 门户中，选择的图标**虚拟机**。 然后，选择 **+ 添加**。

    ![添加新的 Azure 虚拟机](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. 上**基础知识**选项卡上，选择 Ubuntu Server 16.04 LTS。 将 VM 大小更改为 B1ms，具有一个 VCPU 和 2 GB RAM。 Linux SQL Server Docker 容器的最低要求为 2 GB。 选择管理员用户名和密码。

    ![新的虚拟机配置的基础知识选项卡](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. 导航到**网络**选项卡。选择虚拟网络和公共子网包括在 Azure Databricks 群集。 选择**查看 + 创建**，然后**创建**部署虚拟机。

    ![新的虚拟机配置的网络选项卡](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. 部署完成后，导航到虚拟机。 请注意，公共 IP 地址和虚拟网络/子网中**概述**。 选择**公共 IP 地址**

    ![虚拟机概述](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. 更改**赋值**到**静态**，然后输入**DNS 名称标签**。 选择**保存**，并重新启动虚拟机。

    ![公共 IP 地址配置](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. 选择**联网**选项卡上的**设置**。 请注意，在 Azure Databricks 部署过程中创建的网络安全组与虚拟机相关联。 选择**添加入站的端口规则**。

7. 添加规则以打开 SSH 端口 22。 使用以下设置：
    
    |设置|建议的值|描述|
    |-------|---------------|-----------|
    |源|IP 地址|IP 地址指定的特定源 IP 地址将允许或拒绝此规则从传入的流量。|
    |源 IP 地址|< 你的公共 ip\>|输入公共 IP 地址。 可以通过访问查找公共 IP 地址[bing.com](https://www.bing.com/)并搜索 **"我的 IP"**。|
    |源端口范围|*|允许来自任何端口的流量。|
    |目标|IP 地址|IP 地址指定的特定源 IP 地址将允许或拒绝此规则的传出流量。|
    |目标 IP 地址|< 你 vm 的公共 ip\>|输入虚拟机的公共 IP 地址。 您可以在上找到此**概述**虚拟机的页面。|
    |目标端口范围|22|打开 SSH 端口 22。|
    |优先度|290|为规则指定一个优先级。|
    |名称|ssh-databricks-tutorial-vm|为规则指定一个名称。|


    ![添加端口 22 的入站的安全规则](./media/vnet-injection-sql-server/open-port.png)

8. 添加规则以打开 SQL 端口 1433年使用以下设置：

    |设置|建议的值|描述|
    |-------|---------------|-----------|
    |源|IP 地址|IP 地址指定的特定源 IP 地址将允许或拒绝此规则从传入的流量。|
    |源 IP 地址|10.179.0.0/16|输入你的虚拟网络的地址范围。|
    |源端口范围|*|允许来自任何端口的流量。|
    |目标|IP 地址|IP 地址指定的特定源 IP 地址将允许或拒绝此规则的传出流量。|
    |目标 IP 地址|< 你 vm 的公共 ip\>|输入虚拟机的公共 IP 地址。 您可以在上找到此**概述**虚拟机的页面。|
    |目标端口范围|1433|打开 SQL Server 端口 22。|
    |优先度|300|为规则指定一个优先级。|
    |名称|sql-databricks-tutorial-vm|为规则指定一个名称。|

    ![添加端口 1433年的入站的安全规则](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>在 Docker 容器中运行 SQL Server

1. 打开[Ubuntu 的 Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)，或任何其他工具，可让你以 ssh 方式连接到虚拟机。 导航到你的虚拟机在 Azure 门户并选择**Connect**以获取连接所需的 SSH 命令。

    ![连接到虚拟机](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Ubuntu 终端中输入该命令并输入时配置虚拟机创建的管理员密码。

    ![Ubuntu 终端 SSH 登录](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. 使用以下命令在虚拟机上安装 Docker。

    ```bash
    sudo apt-get install docker.io
    ```

    验证安装 Docker 使用以下命令：

    ```bash
    sudo docker --version
    ```

4. 安装映像。

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    检查映像。

    ```bash
    sudo docker images
    ```

5. 从映像运行容器。

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    验证容器正在运行。

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>创建 SQL 数据库

1. 打开 SQL Server Management Studio 并连接到服务器使用服务器名称和 SQL 身份验证。 用户名登录**SA** ，密码是在 Docker 命令中设置的密码。 示例命令中的密码是`Password1234`。

    ![连接到 SQL Server 使用 SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. 您已成功连接后，选择**新查询**并输入下面的代码段，以创建数据库，一个表，然后在表中插入一些记录。

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![若要创建 SQL Server 数据库的查询](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>查询从 Azure Databricks 的 SQL Server

1. 导航到 Azure Databricks 工作区，并验证你作为先决条件的一部分创建群集。 然后，选择**创建一个 Notebook**。 为该 notebook 提供一个名称，选择*Python*作为语言，并选择你创建的群集。

    ![新的 Databricks notebook 设置](./media/vnet-injection-sql-server/create-notebook.png)

2. 使用以下命令以 SQL Server 虚拟机的内部 IP 地址执行 ping 操作。 此 ping 应成功。 如果没有，确认容器正在运行，并查看网络安全组 (NSG) 配置。

    ```python
    %sh
    ping 10.179.64.4
    ```

    此外可以使用 nslookup 命令来查看。

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. 一旦您已成功执行 ping 命令在 SQL Server，您可以查询数据库和表。 运行以下 python 代码：

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>清理资源

不再需要时，删除资源组、 Azure Databricks 工作区中和所有相关的资源。 正在删除作业可以避免不必要的计费。 如果您计划在将来使用 Azure Databricks 工作区，可以停止群集和更高版本重新启动它。 如果您不打算继续使用此 Azure Databricks 工作区，删除通过使用以下步骤在本教程中创建的所有资源：

1. 在 Azure 门户的左侧菜单中，单击**资源组**，然后单击你创建的资源组的名称。

2. 在资源组页上，选择**删除**，键入要在文本框中，删除，然后选择的资源名称**删除**试。

## <a name="next-steps"></a>后续步骤

转到下一步的文章，了解如何提取、 转换和加载数据使用 Azure Databricks。
> [!div class="nextstepaction"]
> [教程：使用 Azure Databricks 提取、转换和加载数据](databricks-extract-load-sql-data-warehouse.md)
