---
title: 数据管理网关的发行说明 | Microsoft Docs
description: 数据管理网关发行说明
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: ac0e1945e75ee7aea346c103a671b4a47b9e5994
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048608"
---
# <a name="release-notes-for-data-management-gateway"></a>数据管理网关的发行说明
> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用当前版本数据工厂服务，请参阅 [V2 中的自承载集成运行时](../create-self-hosted-integration-runtime.md)。

现代数据集成的一大挑战是将数据在本地和云之间进行移动。 数据工厂借助数据管理网关实现此集成，后者是一个可安装在本地以实现混合数据移动的代理。

有关数据管理网关及其用法的详细信息，请参阅以下文章：

*  [数据管理网关](data-factory-data-management-gateway.md)
*  [使用 Azure 数据工厂在本地和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>当前版本 
我们不再维护此处的发行说明。 请在[此处](https://go.microsoft.com/fwlink/?linkid=853077)获取最新的发行说明




## <a name="earlier-versions"></a>早期版本
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>增强功能
- 可将 DNS 条目添加到允许列表服务总线，而不是将防火墙中的所有 Azure IP 地址列入允许列表（如果需要）。 可以在 Azure 门户中找到相应的 DNS 条目（“数据工厂”->“创作和部署”->“网关”->“serviceUrls”（采用 JSON））
- HDFS 连接器现在允许跳过 SSL 验证，支持自签名的公用证书。
- 已修复：更新期间发生的网关脱机问题（由于时钟偏差）


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>增强功能
-   可将 DNS 条目添加到白名单服务总线，而不是将防火墙中的所有 Azure IP 地址列入允许列表（如果需要）。 在此处了解更多详情。
-   现在可以向/从最大 4.75 TB（这是块 Blob 支持的最大大小）的单块 Blob 复制数据。 （早期的限制为 195 GB）。
-   已修复：在复制活动期间解压缩多个小文件时出现的内存不足问题。
-   已修复：使用幂等性功能从 Document DB 复制到本地 SQL Server 时发生的索引超出范围问题。
-   已修复：SQL 清理脚本对“复制向导”中的本地 SQL Server 不起作用。
-   已修复：末尾有空格的列名称在复制活动中不起作用。

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>增强功能
- 已修复：网关计算机重新启动时缺少凭据的问题。
- 已修复：在使用备份文件进行网关还原期间出现的注册问题。


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>增强功能
- 已修复：从 Oracle 作为源读取十进制 null 值不正确。

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>新增功能
- 客户可以提供有关网关注册体验的反馈。
- 支持新的压缩格式：ZIP (Deflate)

### <a name="enhancements-"></a>增强功能
- 改进了 Oracle 接收器和 HDFS 源的性能。
- 修复了网关自动更新、网关并行处理能力方面的 Bug。


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>增强功能
- 进行了改进且更稳健的网关注册体验 - 现在可以在网关注册过程中跟踪进度状态，这使注册体验的响应速度更快。
- 网关还原过程中的改进 - 即使你没有具有此更新的网关备份文件，也仍可以恢复网关。 这需要你在门户中重置“链接的服务”凭据。
- Bug 修复。

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>新增功能

- 现可在本地存储数据源凭据。 这些凭据已加密。 使用可从现有网关导出的备份文件可在本地恢复和还原数据源凭据。

### <a name="enhancements-"></a>增强功能

- 网关注册体验已提升且更加可靠。
- 支持 QuoteChar 配置在复制向导中自动检测文本格式，同时提高格式检测的整体准确度。

## <a name="2361002"></a>2.3.6100.2

- 支持 firstRowAsHeader 和 SkipLineCount 在复制向导中自动检测本地文件系统和 HDFS 中的文本文件。
- 增强了网关和服务总线之间的网络连接稳定性
- 修复了几项 bug


## <a name="2260721"></a>2.2.6072.1

*  支持使用网关配置管理器设置网管的 HTTP 代理。 配置后，可通过 HTTP 代理访问 Azure Blob、Azure 表、Azure Data Lake 和 Document DB。
*  在从/向 Azure Blob、Azure Data Lake Store、本地文件系统和本地 HDFS 复制数据时，支持对 TextFormat 进行标头处理。
*  支持从追加 Blob、页 Blob 以及现已支持的块 Blob 复制数据。
*  引入了一种新的网关状态“在线(受限)”，其指示网关主要功能正常工作，但对复制向导的交互操作支持例外。
*  增强了使用注册密钥注册网关的可靠性。

## <a name="216040"></a>2.1.6040.

*  DB2 驱动程序现包含在网关安装包内。 无需单独安装。
*  DB2 现支持 z/OS、DB2 for i (AS/400) 以及已支持的平台（Linux、Unix 和 Windows）。
*  支持使用 Azure Cosmos DB 作为本地数据存储的源或目标
*  支持从/向冷/热 Blob 存储以及已支持的通用存储帐户复制数据。
*  允许通过具有远程登录权限的网关连接到本地 SQL Server。  

## <a name="2060131"></a>2.0.6013.1

*  手动安装期间可选择用于网关的语言/区域性。

*  网关未按预期工作时，可选择向 Microsoft 发送最近 7 天的网关日志，帮助解决此问题。 如果网关未连接到云服务，可选择保存和存档网关日志。  

*  网关配置管理器的用户界面改进：

    *  提高网关状态在“主页”选项卡上的可见性。

    *  已重新组织和简化控件。

    *  可使用[无代码复制工具](data-factory-copy-data-wizard-tutorial.md)从存储中复制数据。 有关此功能的大体详细信息，请参阅[暂存复制](data-factory-copy-activity-performance.md#staged-copy)。
*  可使用数据管理网关将数据从本地 SQL Server 数据库直接传入 Azure 机器学习。

*  性能提升

    * 针对无代码复制工具中的 SQL Server 提升查看架构/预览的性能。

## <a name="11259531"></a>1.12.5953.1

*  Bug 修复

## <a name="11159181"></a>1.11.5918.1

*  网关事件日志的最大大小已从 1 MB 增加到 40 MB。

*  网关自动更新期间如需重启，会显示警告对话框。 可选择立即重启或稍后重启。

*  如果自动更新失败，网关安装程序最多重试三次自动更新。

*  性能提升

    * 在无代码复制情况下，提升了从本地服务器加载大型表的性能。

*  Bug 修复

## <a name="11058921"></a>1.10.5892.1

*  性能提升

*  Bug 修复

## <a name="1958652"></a>1.9.5865.2

*  零触摸自动更新功能
*  具有网关状态指示器的新托盘图标
*  从客户端“立即更新”的功能
*  设置更新计划时间的功能
*  用于切换开/关自动更新的 PowerShell 脚本
*  支持 JSON 格式  
*  性能提升
*  Bug 修复

## <a name="1858221"></a>1.8.5822.1

*  提升故障排除体验
*  性能提升
*  Bug 修复

### <a name="1757951"></a>1.7.5795.1

*  性能提升
*  Bug 修复

### <a name="1757641"></a>1.7.5764.1

*  性能提升
*  Bug 修复

### <a name="1657351"></a>1.6.5735.1

*  支持本地 HDFS 源/接收器
*  性能提升
*  Bug 修复

### <a name="1656961"></a>1.6.5696.1

*  性能提升
*  Bug 修复

### <a name="1656761"></a>1.6.5676.1

*  支持 Configuration Manager 的诊断工具
*  支持用于 Azure 数据工厂的表格数据源的表列
*  支持用于 Azure 数据工厂的 SQL DW
*  支持用于 Azure 数据工厂的 BlobSource 和 FileSource 中的隐藏
*  支持 CopyBehavior - Azure 数据工厂中包含二进制副本的 BlobSink 和 FileSink 的 MergeFiles、PreserveHierarchy 和 FlattenHierarchy
*  支持用于 Azure 数据工厂的复制活动报告进程
*  支持用于 Azure 数据工厂的数据源连接验证
*  Bug 修复

### <a name="1656721"></a>1.6.5672.1

*  支持用于 Azure 数据工厂的 ODBC 数据源的表名称
*  性能提升
*  Bug 修复

### <a name="1656581"></a>1.6.5658.1

*  支持用于 Azure 数据工厂的文件接收器
*  支持为 Azure 数据工厂在二进制副本中保留层次结构
*  支持用于 Azure 数据工厂的复制活动幂等性
*  Bug 修复

### <a name="1656401"></a>1.6.5640.1

*  支持用于 Azure 数据工厂的额外 3 个数据源（ODBC、OData、HDFS）
*  支持用于 Azure 数据工厂的 csv 分析程序中的引号字符
*  压缩支持 (BZip2)
*  Bug 修复

### <a name="1556121"></a>1.5.5612.1

*  支持用于 Azure 数据工厂的 5 个关系数据库（MySQL、PostgreSQL、DB2、Teradata 和 Sybase）
*  压缩支持（Gzip 和 Deflate）
*  性能提升
*  Bug 修复

### <a name="1455491"></a>1.4.5549.1

*  增加了对 Azure 数据工厂的 Oracle 数据源支持
*  性能提升
*  Bug 修复

### <a name="1454921"></a>1.4.5492.1

*  支持 Microsoft Azure 数据工厂和 Office 365 Power BI 服务的统一二进制文件
*  优化配置 UI 和注册过程
*  Azure 数据工厂 - 对 SQL Server 数据源的 Azure 入口和出口支持

### <a name="1253031"></a>1.2.5303.1

*  解决了超时问题，可支持更多的耗时数据源连接。

### <a name="1155268"></a>1.1.5526.8

*  安装期间需要必备组件 .NET Framework 4.5.1。

### <a name="1051442"></a>1.0.5144.2

*  未进行影响 Azure 数据工厂方案的更改。
