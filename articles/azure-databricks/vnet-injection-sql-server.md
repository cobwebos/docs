---
title: 使用 Azure 数据块查询 SQL Server Linux Docker 容器
description: 本文介绍如何将 Azure 数据块部署到虚拟网络（也称为 VNet 注入）。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747665"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>教程：从 Azure 数据砖块笔记本查询虚拟网络中的 SQL Server Linux Docker 容器

本教程将介绍如何在虚拟网络中将 Azure 数据块与 SQL Server Linux Docker 容器集成。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 将 Azure 数据砖块工作区部署到虚拟网络
> * 在公共网络中安装 Linux 虚拟机
> * 安装 Docker
> * 在 Linux Docker 容器上安装微软 SQL 服务器
> * 使用数据块笔记本中的 JDBC 查询 SQL 服务器

## <a name="prerequisites"></a>先决条件

* [在虚拟网络中创建数据砖块工作区](quickstart-create-databricks-workspace-vnet-injection.md)。

* 为 Windows 安装[Ubuntu](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)。

* 下载[SQL 服务器管理工作室](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)。

## <a name="create-a-linux-virtual-machine"></a>创建 Linux 虚拟机

1. 在 Azure 门户中，选择**虚拟机**的图标。 然后选择“+ 添加”****。

    ![添加新 Azure 虚拟机](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. 在 **"基本"** 选项卡上，选择 Ubuntu 服务器 18.04 LTS 并将 VM 大小更改为 B2。 选择管理员用户名和密码。

    ![新虚拟机配置的基础知识选项卡](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. 导航到 **"网络**"选项卡。选择虚拟网络和包含 Azure 数据砖块群集的公共子网。 选择 **"审阅 + 创建**"，然后**创建**以部署虚拟机。

    ![新虚拟机配置的网络选项卡](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. 部署完成后，导航到虚拟机。 请注意**概述**中的公共 IP 地址和虚拟网络/子网。 选择**公共 IP 地址**

    ![虚拟机概述](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. 将 **"分配"** 更改为 **"静态**"并输入**DNS 名称标签**。 选择 **"保存**"并重新启动虚拟机。

    ![公共 IP 地址配置](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. 选择 **"设置**"下的 **"网络**"选项卡。 请注意，在 Azure 数据砖块部署期间创建的网络安全组与虚拟机相关联。 选择 **"添加入站端口规则**"。

7. 添加规则以打开 SSH 的端口 22。 使用以下设置：
    
    |设置|建议的值|描述|
    |-------|---------------|-----------|
    |源|IP 地址|IP 地址指定此规则将允许或拒绝来自特定源 IP 地址的传入流量。|
    |源 IP 地址|<您的公共 ip\>|输入您的公共 IP 地址。 您可以通过访问[bing.com](https://www.bing.com/)并搜索 **"我的 IP"** 来查找您的公共 IP 地址。|
    |源端口范围|*|允许来自任何端口的流量。|
    |目标|IP 地址|IP 地址指定此规则将允许或拒绝特定源 IP 地址的传出流量。|
    |目标 IP 地址|<vm 公共 ip\>|输入虚拟机的公共 IP 地址。 您可以在虚拟机的 **"概述"** 页上找到此项。|
    |目标端口范围|22|为 SSH 打开端口 22。|
    |优先度|290|使规则具有优先级。|
    |“属性”|ssh-databricks-教程-vm|为规则指定名称。|


    ![为端口 22 添加入站安全规则](./media/vnet-injection-sql-server/open-port.png)

8. 添加规则以打开端口 1433 用于 SQL，具有以下设置：

    |设置|建议的值|描述|
    |-------|---------------|-----------|
    |源|Any|源指定此规则将允许或拒绝来自特定源 IP 地址的传入流量。|
    |源端口范围|*|允许来自任何端口的流量。|
    |目标|IP 地址|IP 地址指定此规则将允许或拒绝特定源 IP 地址的传出流量。|
    |目标 IP 地址|<vm 公共 ip\>|输入虚拟机的公共 IP 地址。 您可以在虚拟机的 **"概述"** 页上找到此项。|
    |目标端口范围|1433|为 SQL 服务器打开端口 22。|
    |优先度|300|使规则具有优先级。|
    |“属性”|sql-databricks-教程-vm|为规则指定名称。|

    ![为端口 1433 添加入站安全规则](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>在 Docker 容器中运行 SQL 服务器

1. [打开 Ubuntu 的 Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)， 或任何其他工具，将允许您 SSH 进入虚拟机. 在 Azure 门户中导航到虚拟机，然后选择 **"连接"** 以获取需要连接的 SSH 命令。

    ![连接到虚拟机](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. 在 Ubuntu 终端中输入该命令，并输入配置虚拟机时创建的管理员密码。

    ![Ubuntu 终端 SSH 登录](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. 使用以下命令在虚拟机上安装 Docker。

    ```bash
    sudo apt-get install docker.io
    ```

    使用以下命令验证 Docker 的安装：

    ```bash
    sudo docker --version
    ```

4. 安装映像。

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    检查图像。

    ```bash
    sudo docker images
    ```

5. 从映像运行容器。

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    验证容器是否正在运行。

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>创建 SQL 数据库

1. 打开 SQL 服务器管理工作室并使用服务器名称和 SQL 身份验证连接到服务器。 登录用户名为**SA，** 密码是 Docker 命令中设置的密码。 示例命令中的密码为`Password1234`。

    ![使用 SQL 服务器管理工作室连接到 SQL 服务器](./media/vnet-injection-sql-server/ssms-login.png)

2. 成功连接后，选择 **"新建查询"** 并输入以下代码段以创建数据库、表，并在表中插入一些记录。

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

    ![查询以创建 SQL Server 数据库](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>从 Azure 数据块查询 SQL 服务器

1. 导航到 Azure 数据砖块工作区，并验证是否创建了群集作为先决条件的一部分。 然后，选择 **"创建笔记本**"。 为笔记本命名，选择*Python*作为语言，然后选择您创建的群集。

    ![新的数据砖笔记本设置](./media/vnet-injection-sql-server/create-notebook.png)

2. 使用以下命令 ping SQL Server 虚拟机的内部 IP 地址。 此 ping 应该成功。 如果没有，请验证容器是否正在运行，并查看网络安全组 （NSG） 配置。

    ```python
    %sh
    ping 10.179.64.4
    ```

    您还可以使用 nslookup 命令来查看。

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. 成功 ping SQL 服务器后，可以查询数据库和表。 运行以下 python 代码：

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

若不再需要资源组、Azure Databricks 工作区以及所有相关资源，请将其删除。 删除作业可避免产生不必要的计费。 若计划将来使用 Azure Databricks 工作区，则可停止群集并在以后重启。 如果不打算继续使用此 Azure Databricks 工作区，请使用以下步骤删除在本教程中创建的所有资源：

1. 在 Azure 门户的左侧菜单中，单击“资源组”****，并单击所创建的资源的名称。

2. 在资源组页上选择“删除”，在文本框中键入要删除的资源的名称，然后再次选择“删除”********。

## <a name="next-steps"></a>后续步骤

进入下一篇文章，了解如何使用 Azure 数据块提取、转换和加载数据。
> [!div class="nextstepaction"]
> [教程：使用 Azure 数据块提取、转换和加载数据](databricks-extract-load-sql-data-warehouse.md)
