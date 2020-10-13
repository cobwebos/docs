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
ms.openlocfilehash: 6284e85d8c4e9ad9f9896081f04c6b7669b8e1c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91446946"
---
# <a name="configure-azure-sql-edge"></a>配置 Azure SQL Edge

Azure SQL Edge 通过以下两个选项之一进行配置：

- 环境变量
- 放置在 /var/opt/mssql 文件夹中的一个 mssql.conf 文件

> [!NOTE]
> 设置环境变量会替代 mssql.conf 文件中指定的设置。

## <a name="configure-by-using-environment-variables"></a>使用环境变量进行配置

Azure SQL Edge 公开了几个不同的环境变量，她们可用于配置 SQL Edge 容器。 这些环境变量是可用于 Linux 上的 SQL Server 的环境变量的子集。 有关 Linux 上的 SQL Server 环境变量的详细信息，请参阅[环境变量](/sql/linux/sql-server-linux-configure-environment-variables/)。

以下新的环境变量已添加到 Azure SQL Edge。 

| 环境变量 | 说明 | 值 |     
|-----|-----| ---------- | 
| **PlanId** | 指定要在初始化过程中使用的 Azure SQL Edge SKU。 仅当使用 Azure IoT Edge 部署 Azure SQL Edge 时，才需要使用此环境变量。 | **asde** -在 iot 边缘或 asde-先进**版**- | 
| **MSSQL_TELEMETRY_ENABLED** | 启用或禁用使用情况和诊断数据收集。 | TRUE 或 FALSE |  
| **MSSQL_TELEMETRY_DIR** | 设置使用情况和诊断数据收集审核文件的目标目录。 | SQL Edge 容器中的文件夹位置。 可以使用装入点或数据卷将此文件夹映射到主机卷。 | 
| **MSSQL_PACKAGE** | 指定要部署的 dacpac 或 bacpac 包的位置。 | 包含 dacpac 或 bacpac 包的文件夹、文件或 SAS URL。 有关详细信息，请参阅[在 SQL Edge 中部署 SQL 数据库 DACPAC 和 BACPAC 包](deploy-dacpac.md)。 |


Azure SQL Edge 不支持以下 Linux 上的 SQL Server 环境变量。 如果已定义，此环境变量在容器初始化期间将被忽略。

| 环境变量 | 说明 |
|-----|-----|
| **MSSQL_ENABLE_HADR** | 启用可用性组。 例如，**1** 为已启用，**0** 为已禁用。 |

> [!IMPORTANT]
> SQL Edge 的 MSSQL_PID 环境变量仅接受 Premium 和 Developer 作为有效值。 Azure SQL Edge 不支持使用产品密钥进行初始化。

### <a name="specify-the-environment-variables"></a>指定环境变量

当通过 [Azure 门户](deploy-portal.md)部署服务时，请为 SQL Edge 指定环境变量。 你可以在模块部署的“环境变量”部分中添加它们，也可以在“容器创建选项”中添加它们。 

在“环境变量”中添加值。

![使用环境变量列表进行设置](media/configure/set-environment-variables.png)

在“容器创建选项”中添加值。

![使用容器创建选项进行设置](media/configure/set-environment-variables-using-create-options.png)

> [!NOTE]
> 在离线部署模式下，可以使用 `docker run` 命令的 `-e`、`--env` 或 `--env-file` 选项来指定环境变量。

## <a name="configure-by-using-an-mssqlconf-file"></a>使用 mssql.conf 文件进行配置

与 Linux 上的 SQL Server 不同，Azure SQL Edge 未包括 [mssql-conf 配置实用工具](/sql/linux/sql-server-linux-configure-mssql-conf/)。 你需要手动配置 mssql.conf 文件，并将其放在映射到 SQL Edge 模块内的 /var/opt/mssql/ 文件夹的永久性存储驱动器中。 从 Azure 市场部署 SQL Edge 时，此映射是作为“容器创建选项”中的“装载”选项指定的。

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

为 Azure SQL Edge 添加了以下新的 mssql.conf 选项。 

|选项|说明|
|:---|:---|
|**customerfeedback** | 选择 SQL Server 是否向 Microsoft 发送反馈。 有关详细信息，请参阅[禁用使用情况和诊断数据收集](usage-and-diagnostics-data-configuration.md#disable-usage-and-diagnostic-data-collection)|      
|**userrequestedlocalauditdirectory** | 设置使用情况和诊断数据收集审核文件的目标目录。 有关详细信息，请参阅[使用情况和诊断数据收集的本地审核](usage-and-diagnostics-data-configuration.md#local-audit-of-usage-and-diagnostic-data-collection) |        

以下 mssql.conf 选项不适用于 SQL Edge：

|选项|说明|
|:---|:---|
|**客户反馈** | 选择 SQL Server 是否向 Microsoft 发送反馈。 |
|**数据库邮件配置文件** | 为 Linux 上的 SQL Server 设置默认数据库邮件配置文件。 |
|**高可用性** | 启用可用性组。 |
|**Microsoft 分布式事务处理协调器** | 在 Linux 上配置 MSDTC 并对其进行故障排除。 SQL Edge 不支持其他分布式事务相关的配置选项。 有关这些其他配置选项的详细信息，请参阅[配置 MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc)。 |
|**机器学习服务 EULA** | 为 Azure 机器学习包接受 R 和 Python EULA。 仅适用于 SQL Server 2019。|
|**outboundnetworkaccess** |为[机器学习服务](/sql/linux/sql-server-linux-setup-machine-learning/) R、Python 和 Java 扩展启用出站网络访问。|

以下示例 mssql.conf 文件适用于 SQL Edge。 有关 mssql.conf 文件格式的详细信息，请参阅 [mssql.conf 格式](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)。

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

## <a name="run-azure-sql-edge-as-non-root-user"></a>以非 root 用户身份运行 Azure SQL Edge

默认情况下，Azure SQL Edge 容器使用非 root 用户/组运行。 当通过 Azure 市场（或使用 docker 运行）部署时，除非指定了其他用户/组，否则 SQL Edge 容器将以 mssql（非 root）用户身份启动。 若要在部署过程中指定其他非 root 用户，请在容器创建选项下添加 `*"User": "<name|uid>[:<group|gid>]"*` 键值对。 在下面的示例中，SQL Edge 被配置为以 `*IoTAdmin*` 用户身份启动。

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

若要允许非 root 用户访问已装载的卷上的 DB 文件，请确保运行容器所用的用户/组在永久性文件存储上具有读取和写入权限。 在下面的示例中，我们将 user_id 为 10001 的非 root 用户设置为文件所有者。 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>从早期 CTP 版本升级

Azure SQL Edge 的早期 CTP 已配置为以 root 用户身份运行。 从早期 CTP 升级时，可以使用以下选项。

- 继续使用 root 用户 - 若要继续使用 root 用户，请在容器创建选项下添加 `*"User": "0:0"*` 键值对。
- 使用默认的 mssql 用户 - 若要使用默认的 mssql 用户，请执行以下步骤：
  - 在 docker 主机上添加名为 mssql 的用户。 在下面的示例中，我们将添加 ID 为 10001 的用户 mssql。 此用户也将添加到 root 组。
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - 更改数据库文件所在的目录/装载卷上的权限 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- 使用其他非 root 用户帐户 - 若要使用其他非 root 用户帐户，请执行以下步骤：
  - 更新容器创建选项，以指定在容器创建选项下添加 `*"User": "user_name | user_id*` 键值对。 将 user_name 或 user_id 替换为 docker 主机中的实际 user_name 或 user_id。 
  - 更改目录/装入卷上的权限。

## <a name="persist-your-data"></a> 保留数据

你的 Azure SQL Edge 配置会更改，数据库文件会保留在容器中（即使通过 `docker stop` 和 `docker start` 重启容器也是如此）。 但是，如果使用 `docker rm` 删除容器，则会删除容器中的所有内容，包括 Azure SQL Edge 和数据库。 以下部分介绍如何使用**数据卷**保留数据库文件（即使关联的容器已被删除）。

> [!IMPORTANT]
> 对于 Azure SQL Edge，了解 Docker 中的数据持久性至关重要。 除本部分讨论的内容外，请参阅有关[如何在 Docker 容器中管理数据](https://docs.docker.com/engine/tutorials/dockervolumes/)的 Docker 文档。

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
> 目前不支持 **Mac 上的 Docker** 与 Azure SQL Edge 映像之间的主机卷映射。 请改为使用数据卷容器。 此限制特定于 `/var/opt/mssql` 目录。 从已装载目录进行读取操作可正常运行。 例如，可在 Mac 上使用 –v 装载主机目录，并通过驻留在主机上的 .bak 文件还原备份。

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

如果随后创建另一个具有相同卷名的容器，则新容器会使用卷中包含的相同 Azure SQL Edge 数据。

若要删除数据卷容器，请使用 `docker volume rm` 命令。

> [!WARNING]
> 如果删除数据卷容器，则该容器中的所有 Azure SQL Edge 数据都会被永久删除。


## <a name="next-steps"></a>后续步骤

- [连接到 Azure SQL Edge](connect.md)
- [使用 SQL Edge 构建端到端 IoT 解决方案](tutorial-deploy-azure-resources.md)
