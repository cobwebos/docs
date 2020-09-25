---
title: 通过 Docker 部署 Azure SQL Edge - Azure SQL Edge
description: 了解如何通过 Docker 部署 Azure SQL Edge
keywords: SQL Edge, 容器, docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: ff14f8a9f236701889aea95911f2a1e381eabf83
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943363"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>通过 Docker 部署 Azure SQL Edge

在本快速入门中，使用 Docker 拉取和运行 Azure SQL Edge 容器映像。 然后使用 **sqlcmd** 连接，以创建第一个数据库并运行查询。

此映像包含基于 Ubuntu 18.04 的 Azure SQL Edge。 它可与 Linux 上或用于 Mac/Windows 的 Docker 上的 Docker 引擎 1.8+ 配合使用。

## <a name="prerequisites"></a>必备条件

- 任何受支持的 Linux 分发或用于 Mac/Windows 的 Docker 上的 Docker 引擎 1.8+。 有关详细信息，请参阅 [Install Docker](https://docs.docker.com/engine/installation/)（安装 Docker）。 由于 Azure SQL Edge 映像基于 Ubuntu 18.04，建议使用 Ubuntu 18.04 Docker 主机。
- Docker **overlay2** 存储驱动程序。 这是大多数用户的默认设置。 如果发现自己未使用此存储提供程序并且需要进行更改，请参阅 [docker 文档中有关配置 overlay2](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver) 的说明和警告。
- 至少 10 GB 的磁盘空间。
- 至少 1 GB 的 RAM。
- [Azure SQL Edge 的硬件要求](https://docs.microsoft.com/azure/azure-sql-edge/features#hardware-support)。


## <a name="pull-and-run-the-container-image"></a>拉取并运行容器映像

在开始执行以下步骤之前，请确保已在本文顶部选择了首选的 shell（bash、PowerShell 或 cmd）。

1. 从 Microsoft 容器注册表中拉取 Azure SQL Edge 容器映像。

    - 拉取 Azure SQL Edge 容器映像
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> 对于本文中的 bash 命令，将使用 `sudo`。 在 macOS 和 windows 上，可能不需要 sudo。 在 Linux 上，如果不想使用 sudo 来运行 Docker，可以配置一个 docker 组，并将用户添加到该组。 有关详细信息，请参阅 [Post-installation steps for Linux](https://docs.docker.com/engine/install/linux-postinstall/)（适用于 Linux 的安装后步骤）。

前面的命令将拉取最新的 Azure SQL Edge 容器映像。 若要查看所有可用映像，请参阅 [azure-sql-egde Docker 中心页](https://hub.docker.com/_/microsoft-azure-sql-edge)。

2. 要使用 Docker 运行容器映像，可以从 Bash Shell (Linux/macOS) 或提升的 PowerShell 命令提示符使用以下命令。
    
    - 启动作为开发人员版运行的 Azure SQL Edge 实例
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - 启动作为高级版运行的 Azure SQL Edge 实例
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > 如果要在 Windows 上使用 PowerShell 运行这些命令，请使用双引号而不是单引号。


    > [!NOTE]
    > 密码应符合 Microsoft SQL 数据库引擎默认密码策略，否则容器无法设置 SQL 引擎，将停止工作。 默认情况下，密码的长度必须至少为 8 个字符，并且必须包含以下四种字符中的三种：大写字母、小写字母、十进制数字和符号。 你可以通过执行 [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) 命令检查错误日志。
    
    下表对前一个 `docker run` 示例中的参数进行了说明：

    | 参数 | 说明 |
    |-----|-----|
    | **-e "ACCEPT_EULA=Y"** |  将 **ACCEPT_EULA** 变量设置为任意值，以确认接受[最终用户许可协议](https://go.microsoft.com/fwlink/?linkid=2139274)。 Azure SQL Edge 映像所需的设置。 |
    | -e "MSSQL_SA_PASSWORD=yourStrong(!)Password" | 指定至少包含 8 个字符且符合 [Azure SQL Edge 密码要求](https://docs.microsoft.com/sql/relational-databases/security/password-policy)的强密码。 Azure SQL Edge 映像所需的设置。 |
    | **-p 1433:1433** | 将主机环境中的 TCP 端口（第一个值）映射到容器中的 TCP 端口（第二个值）。 在此示例中，Azure SQL Edge 侦听容器中的 TCP 1433，并对主机上的端口 1433 公开。 |
    | --name azuresqledge | 为容器指定一个自定义名称，而不是使用随机生成的名称。 如果运行多个容器，则无法重复使用相同的名称。 |
    | **-d** | 在后台运行容器（守护程序） |

    有关所有 Azure SQL Edge 环境变量的完整列表，请参阅[使用环境变量配置 Azure SQL Edge](configure.md#configure-by-using-environment-variables)。你还可以使用 [mssql.conf 文件](configure.md#configure-by-using-an-mssqlconf-file)配置 Azure SQL Edge 容器。

3. 要查看 Docker 容器，请使用 `docker ps` 命令。
   
   ```bash
    sudo docker ps -a
   ```

4. 如果“状态”列显示“正常运行”状态，表明 Azure SQL Edge 正在容器中运行，且正在侦听“端口”列中指定的端口  。 如果 Azure SQL Edge 容器的“状态”列显示“已退出”，请参阅 Azure SQL Edge 文档的疑难解答部分 。

    `-h`（主机名）参数也非常有用，但为了简单起见，本教程中不使用它。 这会将容器的内部名称更改为一个自定义值。 也就是以下 Transact-SQL 查询中返回的名称：

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    将 `-h` 和 `--name` 设为相同的值是一种很好的方法，可以轻松地识别目标容器。

5. 最后一步，更改 SA 密码，因为 `SA_PASSWORD` 在 `ps -eax` 输出中可见，并存储在同名的环境变量中。 请参阅以下步骤。

## <a name="change-the-sa-password"></a>更改 SA 密码

SA  帐户是安装过程中创建的 Azure SQL Edge 实例上的系统管理员。 创建 Azure SQL Edge 容器后，通过在容器中运行 `echo $SA_PASSWORD`，可发现指定的 `MSSQL_SA_PASSWORD` 环境变量。 出于安全考虑，请考虑更改 SA 密码。

1. 选择 SA 用户要使用的强密码。

2. 使用 `docker exec` 运行sqlcmd，以使用 Transact-SQL 更改密码。 在下面的示例中，将旧密码 `<YourStrong!Passw0rd>` 和新密码 `<YourNewStrong!Passw0rd>` 替换为你自己的密码值。

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>连接到 Azure SQL Edge

下列步骤在容器内部使用 Azure SQL Edge 命令行工具 sqlcmd 来连接 Azure SQL Edge。

> [!NOTE]
> sqlcmd 工具在 ARM64 版的 SQL Edge 容器中不提供。

1. 使用 `docker exec -it` 命令在运行的容器内部启动交互式 Bash Shell。 在下面的示例中，`azuresqledge` 是在创建容器时由 `--name` 参数指定的名称。

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. 在容器内部使用 sqlcmd 进行本地连接。 默认情况下，sqlcmd 不在路径之中，因此需要指定完整路径。

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > 可以省略命令行上提示要输入的密码。

3. 如果成功，应会显示 sqlcmd 命令提示符：`1>`。

## <a name="create-and-query-data"></a>创建和查询数据

以下部分将引导你使用 sqlcmd 和 Transact-SQL 完成新建数据库、添加数据并运行简单查询的整个过程。

### <a name="create-a-new-database"></a>新建数据库

以下步骤创建一个名为 `TestDB` 的新数据库。

1. 在 sqlcmd 命令提示符中，粘贴以下 Transact-SQL 命令以创建测试数据库：

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. 在下一行中，编写一个查询以返回服务器上所有数据库的名称：

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>插入数据

接下来创建一个新表 `Inventory`，然后插入两个新行。

1. 在 sqlcmd 命令提示符中，将上下文切换到新的 `TestDB` 数据库：

   ```sql
   USE TestDB
   ```

2. 创建名为 `Inventory` 的新表：

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. 将数据插入新表：

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. 要执行上述命令的类型 `GO`：

   ```sql
   GO
   ```

### <a name="select-data"></a>选择数据

现在，运行查询以从 `Inventory` 表返回数据。

1. 通过 sqlcmd 命令提示符输入查询，以返回 `Inventory` 表中数量大于 152 的行：

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. 执行此命令：

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>退出 sqlcmd 命令提示符

1. 要结束 sqlcmd 会话，请键入 `QUIT`：

   ```sql
   QUIT
   ```

2. 要在容器中退出交互式命令提示，请键入 `exit`。 退出交互式 Bash Shell 后，容器将继续运行。

## <a name="connect-from-outside-the-container"></a> 从容器外连接

还可以从支持 SQL 连接的任何 Linux、Windows 或 macOS 外部工具连接到 Docker 计算机上的 Azure SQL Edge 实例。 有关从外部连接到 SQL Edge 容器的详细信息，请参阅[连接和查询 Azure SQL Edge](connect.md)。

## <a name="remove-your-container"></a>删除容器

如果想删除本教程中使用的 Azure SQL Edge 容器，请运行以下命令：

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> 停止并删除容器会永久删除容器中的所有 Azure SQL Edge 数据。 如果你需要保留数据，请[在容器外创建并复制备份文件](backup-restore.md)或使用[容器数据暂留技术](configure.md#persist-your-data)。

## <a name="next-steps"></a>后续步骤

- [在 SQL Edge 中将机器学习和人工智能与 ONNX 结合使用](onnx-overview.md)。
- [使用 IoT Edge 通过 SQL Edge 生成端到端 IoT 解决方案](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge 中的数据流式处理](stream-data.md)
