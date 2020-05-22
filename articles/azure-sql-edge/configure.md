---
title: 配置 Azure SQL Edge（预览版）
description: 了解如何配置 Azure SQL Edge（预览版）
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5dcdd1604674ca56fb7a646d4c571d63bd2c0e3e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594006"
---
# <a name="configure-azure-sql-edge-preview"></a>配置 Azure SQL Edge（预览版）

Azure SQL Edge 通过以下两个选项之一进行配置：

- 使用环境变量。
- 使用置于 /var/opt/mssql 文件夹中的 mssql.conf 文件。

> [!NOTE]
> 设置环境变量会替代 mssql.conf 文件中指定的设置。

## <a name="configure-using-environment-variables"></a>使用环境变量进行配置

Azure SQL Edge 公开了几个不同的环境变量，她们可用于配置 SQL Edge 容器。 这些环境变量是可用于 Linux 上的 SQL Server 的环境变量的子集。 有关 Linux 上的 SQL Server 环境变量的详细信息，请参阅[环境变量](/sql/linux/sql-server-linux-configure-environment-variables/)。

Azure SQL Edge 不支持以下 Linux 上的 SQL Server 环境变量。 如已定义，这些环境变量将在容器初始化期间被忽略。

| 环境变量 | 说明 |
|-----|-----|
| **MSSQL_ENABLE_HADR** | 启用可用性组。 例如，“1”为已启用，“0”为已禁用 |

> [!IMPORTANT]
> SQL Edge 的 MSSQL_PID 环境变量仅接受 Premium 和 Developer 作为有效值。 Azure SQL Edge 不支持使用产品密钥进行初始化。

> [!NOTE]
> 若要下载 Azure SQL Edge 最终用户许可协议，请参阅[最终用户许可协议](https://go.microsoft.com/fwlink/?linkid=2128283)。

### <a name="specifying-the-environment-variables"></a>指定环境变量

通过 [Azure 门户](deploy-portal.md)部署 Azure SQL Edge 时，可以为 SQL Edge 指定环境变量。 这可以添加到模块部署的“环境变量”部分中，也可以添加为容器创建选项的一部分，如下所述。

使用环境变量选项进行设置

![使用环境变量列表进行设置](media/configure/set-environment-variables.png)

使用容器创建选项进行设置

![使用容器创建选项进行设置](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>使用 mssql.conf 文件进行配置

与 Linux 上的 SQL Server 不同，Azure SQL Edge 不包含 [mssql-conf 配置实用工具](/sql/linux/sql-server-linux-configure-mssql-conf/)，因此，需要手动配置 mssql.conf 文件，并将其放置在映射到 SQL Edge 模块中的 /var/opt/mssql/ 文件夹的持久存储驱动器中。 从 Azure 市场部署 SQL Edge 时，此映射被指定为容器创建选项中的“装载”选项

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

以下 mssql.conf 选项不适用于 SQL Edge：
</br></br>
|选项|说明|
|:---|:---|
|**客户反馈** | 选择 SQL Server 是否向 Microsoft 发送反馈。 |
|**数据库邮件配置文件** | 为 Linux 上的 SQL Server 设置默认数据库邮件配置文件。 |
|**高可用性** | 启用可用性组。 |
|**Microsoft 分布式事务处理协调器** | 在 Linux 上配置 MSDTC 并对其进行故障排除。 此外，SQL Edge 不支持其他分布式事务相关的配置选项。 有关这些其他配置选项的详细信息，请参阅[配置 MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc) |
|**MLServices EULA** | 对于机器学习服务包，接受 R 和 Python EULA。 仅适用于 SQL Server 2019。|
|**outboundnetworkaccess** |为[机器学习服务](/sql/linux/sql-server-linux-setup-machine-learning/) R、Python 和 Java 扩展启用出站网络访问。|

下面提供了一个用于 SQL Edge 的 mssql.conf 文件示例。 有关 mssql.conf 文件格式的详细信息，请参阅 [mssql.conf 格式](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)。

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

## <a name="next-step"></a>后续步骤

- [连接到 Azure SQL Edge](connect.md)
- [使用 SQL Edge 构建端到端 IoT 解决方案](tutorial-deploy-azure-resources.md)
