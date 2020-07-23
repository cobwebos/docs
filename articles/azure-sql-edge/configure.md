---
title: 配置 Azure SQL Edge（预览版）
description: 了解如何配置 Azure SQL Edge （预览版）。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: c38bb6100665cc9456b66608660bdca520b934c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636234"
---
# <a name="configure-azure-sql-edge-preview"></a>配置 Azure SQL Edge（预览版）

Azure SQL Edge 通过以下两个选项之一进行配置：

- 环境变量
- 放置在/var/opt/mssql 文件夹中的 mssql. 文件

> [!NOTE]
> 设置环境变量会替代 mssql. 文件中指定的设置。

## <a name="configure-by-using-environment-variables"></a>使用环境变量进行配置

Azure SQL Edge 公开了几个不同的环境变量，她们可用于配置 SQL Edge 容器。 这些环境变量是可用于 Linux 上的 SQL Server 的一个子集。 有关 Linux 上的 SQL Server 环境变量的详细信息，请参阅[环境变量](/sql/linux/sql-server-linux-configure-environment-variables/)。

Azure SQL Edge 不支持以下 Linux 上的 SQL Server 环境变量。 如果已定义，则在容器初始化过程中将忽略此环境变量。

| 环境变量 | 说明 |
|-----|-----|
| **MSSQL_ENABLE_HADR** | 启用可用性组。 例如，启用**1**并禁用**0** 。 |

> [!IMPORTANT]
> SQL Edge 的 MSSQL_PID 环境变量仅接受 Premium 和 Developer 作为有效值。 Azure SQL Edge 不支持使用产品密钥进行初始化。

> [!NOTE]
> 下载适用于 Azure SQL Edge 的[Microsoft 软件许可条款](https://go.microsoft.com/fwlink/?linkid=2128283)。

### <a name="specify-the-environment-variables"></a>指定环境变量

通过[Azure 门户](deploy-portal.md)部署服务时，为 SQL Edge 指定环境变量。 您可以在模块部署的 "**环境变量**" 部分或**容器 "创建选项**" 中添加它们。

在**环境变量**中添加值。

![使用环境变量列表设置](media/configure/set-environment-variables.png)

在**容器创建选项**中添加值。

![使用容器创建选项设置](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>使用 mssql. 会议文件进行配置

Azure SQL Edge 不包含[mssql 会议配置实用工具](/sql/linux/sql-server-linux-configure-mssql-conf/)，如 Linux 上的 SQL Server。 你需要手动配置 mssql.，并将其放在映射到 SQL Edge 模块中的/var/opt/mssql/文件夹的持久存储驱动器中。 当你从 Azure Marketplace 部署 SQL Edge 时，此映射将指定为**容器创建选项**中的 "**装载**" 选项。

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

以下 mssql 方案选项不适用于 SQL Edge：

|选项|描述|
|:---|:---|
|**客户反馈** | 选择是否 SQL Server 向 Microsoft 发送反馈。 |
|**数据库邮件配置文件** | 为 Linux 上的 SQL Server 设置默认数据库邮件配置文件。 |
|**高可用性** | 启用可用性组。 |
|**Microsoft 分布式事务处理协调器** | 在 Linux 上配置 MSDTC 并对其进行故障排除。 SQL Edge 不支持其他分布式事务相关配置选项。 有关这些其他配置选项的详细信息，请参阅[CONFIGURE MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc)。 |
|**MLServices EULA** | 接受 Azure 机器学习包的 R 和 Python Eula。 仅适用于 SQL Server 2019。|
|**outboundnetworkaccess** |为[机器学习服务](/sql/linux/sql-server-linux-setup-machine-learning/) R、Python 和 Java 扩展启用出站网络访问。|

以下示例 mssql. 会议文件适用于 SQL 边缘。 有关 mssql. 会议文件的格式的详细信息，请参阅[mssql. 工作格式](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)。

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

## <a name="next-steps"></a>后续步骤

- [连接到 Azure SQL Edge](connect.md)
- [使用 SQL Edge 构建端到端 IoT 解决方案](tutorial-deploy-azure-resources.md)
