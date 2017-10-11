---
title: "在 OMS Log Analytics 中收集 Linux 应用程序性能数据 | Microsoft Docs"
description: "本文提供了有关对用于 Linux 的 OMS 代理进行配置以收集 MySQL 和 Apache HTTP Server 的性能计数器的详细信息。"
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 04ea6f728e59ec8b47e54fe45e1adc6cbbfb85ff
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="collect-performance-counters-for-linux-applications-in-log-analytics"></a>在 Log Analytics 中收集 Linux 应用程序的性能计数器 
本文提供了有关对[用于 Linux 的 OMS 代理](https://github.com/Microsoft/OMS-Agent-for-Linux)进行配置以收集特定应用程序的性能计数器的详细信息。  本文中包括的应用程序有：  

- [MySQL](#MySQL)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
如果安装 OMS 代理时在计算机上检测到 MySQL 服务器或 MariaDB 服务器，则会自动安装 MySQL 服务器的性能监视提供程序。 此提供程序连接到本地 MySQL/MariaDB 服务器以公开性能统计信息。 必须配置 MySQL 用户凭据，以便提供程序能够访问 MySQL 服务器。

### <a name="configure-mysql-credentials"></a>配置 MySQL 凭据
MySQL OMI 提供程序需要预先配置 MySQL 用户并安装 MySQL 客户端库，才能从 MySQL 实例中查询性能和运行状况信息。  这些凭据存储在 Linux 代理上存储的一个身份验证文件中。  该身份验证文件指定了 MySQL 实例正在侦听的绑定地址和端口以及用于收集指标的凭据。  

在安装用于 Linux 的 OMS 代理期间，MySQL OMI 提供程序会在 MySQL my.cnf 配置文件（默认位置）中扫描绑定地址和端口并对 MySQL OMI 身份验证文件进行部分设置。

MySQL 身份验证文件存储在 `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth` 处。


### <a name="authentication-file-format"></a>身份验证文件格式
下面是 MySQL OMI 身份验证文件的格式

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

下表描述了身份验证文件中的条目。

| 属性 | 说明 |
|:--|:--|
| 端口 | 表示 MySQL 实例正在侦听的当前端口。 端口 0 指定后面的属性用于默认实例。 |
| Bind-Address| 当前 MySQL 绑定地址。 |
| username| 用来监视 MySQL 服务器实例的 MySQL 用户。 |
| Base64 编码的密码| MySQL 监视用户的密码（采用 Base64 编码）。 |
| AutoUpdate| 指定当升级 MySQL OMI 提供程序时，是否将重新扫描 my.cnf 文件中的更改并覆盖 MySQL OMI 身份验证文件。 |

### <a name="default-instance"></a>默认实例
MySQL OMI 身份验证文件可以定义一个默认的实例和端口号，以便更轻松地管理一台 Linux 主机上的多个 MySQL 实例。  端口为 0 的实例代表默认实例。 所有其他实例都将继承在默认实例中设置的属性，除非它们指定了不同的值。 例如，如果添加了在端口“3308”上侦听的 MySQL 实例，则将使用默认实例的绑定地址、用户名和 Base64 编码的密码来尝试和监视在端口 3308 上侦听的实例。 如果 3308 上的实例绑定到了另一个地址，且使用相同的 MySQL 用户名和密码对，则只需要指定绑定地址，并且将继承其他属性。

下表提供了示例实例设置 

| 说明 | 文件 |
|:--|:--|
| 默认实例和端口为 3308 的实例。 | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| 默认实例和端口为 3308 且采用不同用户名和密码的实例。 | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMI 身份验证文件程序
随 MySQL OMI 提供程序安装了一个 MySQL OMI 身份验证文件程序，可以使用该程序来编辑 MySQL OMI 身份验证文件。 可以在以下位置找到该身份验证文件程序。

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> 凭据文件必须可供 omsagent 帐户读取。 建议以 omsgent 身份运行 mycimprovauth 命令。

下表提供了有关 mycimprovauth 的使用语法的详细信息。

| 操作 | 示例 | 说明
|:--|:--|:--|
| autoupdate *false\|true* | mycimprovauth autoupdate false | 设置在重新启动或更新时是否会自动更新身份验证文件。 |
| default *bind-address username password* | mycimprovauth default 127.0.0.1 root pwd | 在 MySQL OMI 身份验证文件中设置默认实例。<br>应当以纯文本输入密码字段 - MySQL OMI 身份验证文件中的密码将是 Base 64 编码的。 |
| delete *default\|port_num* | mycimprovauth 3308 | 删除由默认值或端口号指定的实例。 |
| help | mycimprov help | 输出可使用的命令列表。 |
| print | mycimprov print | 输出易于阅读的 MySQL OMI 身份验证文件。 |
| update port_num *bind-address username password* | mycimprov update 3307 127.0.0.1 root pwd | 更新指定的实例，或添加该实例（如果它不存在）。 |

以下示例命令为 localhost 上的 MySQL 服务器定义了一个默认用户帐户。  应当以纯文本输入密码字段 - MySQL OMI 身份验证文件中的密码将是 Base 64 编码的

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>MySQL 性能计数器所需的数据库权限
MySQL 用户需要访问以下查询来收集 MySQL 服务器性能数据。 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


MySQL 用户还需要对以下默认表具有 SELECT 访问权限。

- information_schema
- mysql 

可以通过运行以下授予命令来授予这些特权。

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> 要向 MySQL 监视用户授予权限，执行授权的用户必须具有 'GRANT option' 特权以及要授予的特权。

### <a name="define-performance-counters"></a>定义性能计数器

将用于 Linux 的 OMS 代理配置为将数据发送到 Log Analytics 后，必须配置要收集的性能计数器。  请对下表中的计数器使用 [Log Analytics 中的 Windows 和 Linux 性能数据来源](log-analytics-data-sources-windows-events.md)中所述的过程。

| 对象名称 | 计数器名称 |
|:--|:--|
| MySQL 数据库 | 磁盘空间(字节) |
| MySQL 数据库 | 表 |
| MySQL Server | 中止的连接百分比 |
| MySQL Server | 连接使用百分比 |
| MySQL Server | 磁盘空间使用(字节) |
| MySQL Server | 全表扫描百分比 |
| MySQL Server | InnoDB 缓冲池命中百分比 |
| MySQL Server | InnoDB 缓冲池使用百分比 |
| MySQL Server | InnoDB 缓冲池使用百分比 |
| MySQL Server | 键缓存命中百分比 |
| MySQL Server | 键缓存使用百分比 |
| MySQL Server | 键缓存写入百分比 |
| MySQL Server | 查询缓存命中百分比 |
| MySQL Server | 查询缓存修剪百分比 |
| MySQL Server | 查询缓存使用百分比 |
| MySQL Server | 表缓存命中百分比 |
| MySQL Server | 表缓存使用百分比 |
| MySQL Server | 表锁争用百分比 |

## <a name="apache-http-server"></a>Apache HTTP Server 
如果安装 omsagent 捆绑包时在计算机上检测到 Apache HTTP Server，则会自动安装 Apache HTTP Server 的性能监视提供程序。 此提供程序依赖于必须加载到 Apache HTTP Server 才能访问性能数据的一个 Apache 模块。 可以使用以下命令加载该模块：
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

要卸载 Apache 监视模块，请运行以下命令︰
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>定义性能计数器

将用于 Linux 的 OMS 代理配置为将数据发送到 Log Analytics 后，必须配置要收集的性能计数器。  请对下表中的计数器使用 [Log Analytics 中的 Windows 和 Linux 性能数据来源](log-analytics-data-sources-windows-events.md)中所述的过程。

| 对象名称 | 计数器名称 |
|:--|:--|
| Apache HTTP Server | 繁忙工作线程数 |
| Apache HTTP Server | 空闲工作线程数 |
| Apache HTTP Server | 繁忙工作线程数百分比 |
| Apache HTTP Server | CPU 百分比总计 |
| Apache 虚拟主机 | 每分钟错误数 - 客户端 |
| Apache 虚拟主机 | 每分钟错误数 - 服务器 |
| Apache 虚拟主机 | 每个请求的 KB 数 |
| Apache 虚拟主机 | 每秒的请求的 KB 数 |
| Apache 虚拟主机 | 每秒的请求数 |



## <a name="next-steps"></a>后续步骤
* 从 Linux 代理[收集性能计数器](log-analytics-data-sources-performance-counters.md)。
* 了解[日志搜索](log-analytics-log-searches.md)以便分析从数据源和解决方案中收集的数据。 