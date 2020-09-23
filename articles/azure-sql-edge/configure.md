---
title: 配置 Azure SQL Edge
description: 了解如何配置 Azure SQL Edge。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: b2c52457972d94b2e999c137d19d3a434ff17a7d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888379"
---
# <a name="configure-azure-sql-edge"></a>配置 Azure SQL Edge

Azure SQL Edge 通过以下两个选项之一进行配置：

- 环境变量
- 放置在/var/opt/mssql 文件夹中的 mssql. 文件

> [!NOTE]
> 设置环境变量会替代 mssql. 文件中指定的设置。

## <a name="configure-by-using-environment-variables"></a>使用环境变量进行配置

Azure SQL Edge 公开了几个不同的环境变量，她们可用于配置 SQL Edge 容器。 这些环境变量是可用于 Linux 上的 SQL Server 的一个子集。 有关 Linux 上的 SQL Server 环境变量的详细信息，请参阅 [环境变量](/sql/linux/sql-server-linux-configure-environment-variables/)。

已将以下新环境变量添加到 Azure SQL Edge。 

| 环境变量 | 说明 | 值 |     
|-----|-----| ---------- |   
| **MSSQL_TELEMETRY_ENABLED** | 启用或禁用使用情况和诊断数据收集。 | TRUE 或 FALSE |  
| **MSSQL_TELEMETRY_DIR** | 设置使用情况和诊断数据收集审核文件的目标目录。 | SQL Edge 容器中的文件夹位置。 可以使用装入点或数据卷将此文件夹映射到主机卷。 | 
| **MSSQL_PACKAGE** | 指定要部署的 dacpac 或 bacpac 包的位置。 | 文件夹、文件或包含 dacpac 或 bacpac 包的 SAS URL。 有关详细信息，请参阅 [在 Sql Edge 中部署 Sql 数据库 DACPAC 和 BACPAC 包](deploy-dacpac.md)。 |


Azure SQL Edge 不支持以下 Linux 上的 SQL Server 环境变量。 如果已定义，则在容器初始化过程中将忽略此环境变量。

| 环境变量 | 说明 |
|-----|-----|
| **MSSQL_ENABLE_HADR** | 启用可用性组。 例如，启用 **1** 并禁用 **0** 。 |

> [!IMPORTANT]
> SQL Edge 的 MSSQL_PID 环境变量仅接受 Premium 和 Developer 作为有效值。 Azure SQL Edge 不支持使用产品密钥进行初始化。

### <a name="specify-the-environment-variables"></a>指定环境变量

通过 [Azure 门户](deploy-portal.md)部署服务时，为 SQL Edge 指定环境变量。 您可以在模块部署的 " **环境变量** " 部分或 **容器 "创建选项**" 中添加它们。

在 **环境变量**中添加值。

![使用环境变量列表设置](media/configure/set-environment-variables.png)

在 **容器创建选项**中添加值。

![使用容器创建选项设置](media/configure/set-environment-variables-using-create-options.png)

> [!NOTE]
> 在断开连接的部署模式下，可以使用 `-e` 或命令的或选项指定环境变量 `--env` `--env-file` `docker run` 。

## <a name="configure-by-using-an-mssqlconf-file"></a>使用 mssql. 会议文件进行配置

Azure SQL Edge 不包含 [mssql 会议配置实用工具](/sql/linux/sql-server-linux-configure-mssql-conf/) ，如 Linux 上的 SQL Server。 你需要手动配置 mssql.，并将其放在映射到 SQL Edge 模块中的/var/opt/mssql/文件夹的持久存储驱动器中。 当你从 Azure Marketplace 部署 SQL Edge 时，此映射将指定为**容器创建选项**中的 "**装载**" 选项。

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

为 Azure SQL Edge 添加了以下新的 mssql。 

|选项|说明|
|:---|:---|
|**customerfeedback** | 选择是否 SQL Server 向 Microsoft 发送反馈。 有关详细信息，请参阅 [禁用使用情况和诊断数据收集](usage-and-diagnostics-data-configuration.md#disable-usage-and-diagnostic-data-collection)|      
|**userrequestedlocalauditdirectory** | 设置使用情况和诊断数据收集审核文件的目标目录。 有关详细信息，请参阅 [使用情况和诊断数据收集的本地审核](usage-and-diagnostics-data-configuration.md#local-audit-of-usage-and-diagnostic-data-collection) |        

以下 mssql 方案选项不适用于 SQL Edge：

|选项|说明|
|:---|:---|
|**客户反馈** | 选择是否 SQL Server 向 Microsoft 发送反馈。 |
|**数据库邮件配置文件** | 为 Linux 上的 SQL Server 设置默认数据库邮件配置文件。 |
|**高可用性** | 启用可用性组。 |
|**Microsoft 分布式事务处理协调器** | 在 Linux 上配置 MSDTC 并对其进行故障排除。 SQL Edge 不支持其他分布式事务相关配置选项。 有关这些其他配置选项的详细信息，请参阅 [CONFIGURE MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc)。 |
|**ML 服务 Eula** | 接受 Azure 机器学习包的 R 和 Python Eula。 仅适用于 SQL Server 2019。|
|**outboundnetworkaccess** |为[机器学习服务](/sql/linux/sql-server-linux-setup-machine-learning/) R、Python 和 Java 扩展启用出站网络访问。|

以下示例 mssql. 会议文件适用于 SQL 边缘。 有关 mssql. 会议文件的格式的详细信息，请参阅 [mssql. 工作格式](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)。

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>以非根用户身份运行 Azure SQL Edge

默认情况下，Azure SQL Edge 容器使用非根用户/组运行。 当通过 Azure Marketplace 部署 (或使用 docker 运行) 时，除非指定了其他用户/组，否则 SQL 边缘容器将作为 mssql (非根) 用户启动。 若要在部署过程中指定其他非根用户，请 `*"User": "<name|uid>[:<group|gid>]"*` 在 "容器创建选项" 下添加键/值对。 在下面的示例中，SQL Edge 配置为以用户身份启动 `*IoTAdmin*` 。

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

若要允许非根用户访问已装入卷上的数据库文件，请确保在下运行容器的用户/组具有对持久文件存储的读取 & 写入权限。 在下面的示例中，我们将 user_id 10001 的非根用户设置为文件所有者。 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>从早期 CTP 版本升级

Azure SQL Edge 的早期 Ctp 配置为以根用户身份运行。 从早期 Ctp 升级时，可以使用以下选项。

- 继续使用 root 用户-若要继续使用 root 用户，请 `*"User": "0:0"*` 在容器创建选项下添加键值对。
- 使用默认 mssql 用户-若要使用默认 mssql 用户，请按照以下步骤操作
  - 在 docker 主机上添加名为 mssql 的用户。 在下面的示例中，我们将添加 ID 为10001的用户 mssql。 此用户也将添加到根组。
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - 更改数据库文件所在的目录/装载卷上的权限 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- 使用其他非根用户帐户-使用不同的非根用户帐户
  - 更新容器创建选项以指定 `*"User": "user_name | user_id*` 容器创建选项下的添加键值对。 将 user_name 或 user_id 替换为 docker 主机中的实际 user_name 或 user_id。 
  - 更改目录/装入卷的权限。

## <a name="persist-your-data"></a> 保留数据

即使你用和重新启动容器，Azure SQL Edge 配置更改和数据库文件也会保留在容器 `docker stop` 中 `docker start` 。 但是，如果删除容器 `docker rm` ，则会删除容器中的所有内容，包括 AZURE SQL Edge 和数据库。 以下部分介绍如何使用**数据卷**保留数据库文件（即使关联的容器已被删除）。

> [!IMPORTANT]
> 对于 Azure SQL Edge，了解 Docker 中的数据持久性非常重要。 除本部分讨论的内容外，请参阅有关[如何在 Docker 容器中管理数据](https://docs.docker.com/engine/tutorials/dockervolumes/)的 Docker 文档。

### <a name="mount-a-host-directory-as-data-volume"></a>将主机目录作为数据卷装载

第一种方法是在主机上将目录作为容器中的数据卷装载。 为此，请将 `docker run` 命令与 `-v <host directory>:/var/opt/mssql` 标志配合使用。 这允许在容器执行之间还原数据。

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

借助此方法，还能共享和查看 Docker 外部的主机上的文件。

> [!IMPORTANT]
> Windows 上的 Docker 的主机卷映射当前不支持映射完整的 `/var/opt/mssql` 目录  。 但是，你可以将子目录（如 `/var/opt/mssql/data`）映射到主机。

> [!IMPORTANT]
> 目前不支持 Azure SQL Edge 映像的 **Docker 上的 Docker** 的主机卷映射。 请改为使用数据卷容器。 此限制特定于 `/var/opt/mssql` 目录。 从已装载目录进行读取操作可正常运行。 例如，可在 Mac 上使用 –v 装载主机目录，并通过驻留在主机上的 .bak 文件还原备份。

### <a name="use-data-volume-containers"></a>使用数据卷容器

第二种方法是使用数据卷容器。 可通过指定卷名（而不是包含 `-v` 参数的主机目录）来创建数据卷容器。 以下示例创建名为 **sqlvolume** 的共享数据卷。

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

> [!NOTE]
> 早期版本的 Docker 不支持通过此方法在 run 命令中隐式创建数据卷。 在这种情况下，请使用 Docker 文档[创建和装载数据卷容器](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container)中列出的显式步骤。

即使停止并删除此容器，数据卷仍然存在。 可使用 `docker volume ls` 命令进行查看。

```bash
docker volume ls
```

如果随后使用相同的卷名称创建另一个容器，则新容器将使用卷中包含的相同 Azure SQL Edge 数据。

若要删除数据卷容器，请使用 `docker volume rm` 命令。

> [!WARNING]
> 如果删除数据卷容器，容器中的任何 Azure SQL Edge 数据都将被 *永久* 删除。


## <a name="next-steps"></a>后续步骤

- [连接到 Azure SQL Edge](connect.md)
- [使用 SQL Edge 构建端到端 IoT 解决方案](tutorial-deploy-azure-resources.md)
