---
title: "数据管理网关的发行说明 | Microsoft Docs"
description: "数据管理网关发行说明"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: b1f5f80e5630c124609481f0ca8ec79f033c6ca0
ms.openlocfilehash: 467037521698f9d558822ee48ee3c030509184b8


---
# <a name="release-notes-for-data-management-gateway"></a>数据管理网关的发行说明
现代数据集成的一大挑战是将数据从本地和云之间进行无缝移动。 数据工厂借助数据管理网关实现此无缝集成，后者是一个可安装在本地以实现混合数据移动的代理。

有关数据管理网关及其用法的详细信息，请参阅以下文章： 

* [数据管理网关](data-factory-data-management-gateway.md)
* [使用 Azure 数据工厂在本地和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md) 

## <a name="current-version-2461511"></a>当前版本 (2.4.6151.1)

### <a name="whats-new"></a>新增功能

- 现可在本地存储数据源凭据。 这些凭据已加密。 使用可从现有网关导出的备份文件可在本地恢复和还原数据源凭据。 

### <a name="enhancements-"></a>增强功能

- 网关注册体验已提升且更加可靠。
- 支持 QuoteChar 配置在复制向导中自动检测文本格式，同时提高格式检测的整体准确度。


## <a name="earlier-versions"></a>早期版本

### <a name="2361002"></a>2.3.6100.2

- 支持 firstRowAsHeader 和 SkipLineCount 在复制向导中自动检测本地文件系统和 HDFS 中的文本文件。
- 增强了网关和服务总线之间的网络连接稳定性
- 修复了几项 bug


## <a name="2260721"></a>2.2.6072.1
* 支持使用网关配置管理器设置网管的 HTTP 代理。 配置后，可通过 HTTP 代理访问 Azure Blob、Azure 表、Azure Data Lake 和 Document DB。
* 在从/向 Azure Blob、Azure Data Lake Store、本地文件系统和本地 HDFS 复制数据时，支持对 TextFormat 进行标头处理。
* 支持从追加 Blob、页 Blob 以及现已支持的块 Blob 复制数据。
* 引入了一种新的网关状态“在线(受限)”，其指示网关主要功能正常工作，但对复制向导的交互操作支持例外。
* 增强了使用注册密钥注册网关的可靠性。

## <a name="2160401"></a>2.1.6040.1
* DB2 驱动程序现包含在网关安装包内。 无需单独安装。 
* DB2 现支持 z/OS、DB2 for i (AS/400) 以及已支持的平台（Linux、Unix 和 Windows）。 
* 支持使用 DocumentDB 作为本地数据存储的源或目标
* 支持从/向冷/热 Blob 存储以及已支持的通用存储帐户复制数据。 
* 允许通过具有远程登录权限的网关连接到本地 SQL Server。  

## <a name="2060131"></a>2.0.6013.1
* 手动安装期间可选择用于网关的语言/区域性。
* 网关未按预期工作时，可选择向 Microsoft 发送最近 7 天的网关日志，帮助解决此问题。 如果网关未连接到云服务，可选择保存和存档网关日志。  
* 网关配置管理器的用户界面改进：
  * 提高网关状态在“主页”选项卡上的可见性。
  * 已重新组织和简化控件。
* 可使用[无代码复制预览工具](data-factory-copy-data-wizard-tutorial.md)从存储中复制数据。 有关此功能的大体详细信息，请参阅[暂存复制](data-factory-copy-activity-performance.md#staged-copy)。 
* 可使用数据管理网关将数据从本地 SQL Server 数据库直接传入 Azure 机器学习。
* 性能提升
  * 针对无代码复制预览工具中的 SQL Server 提升查看架构/预览的性能。

## <a name="11259531"></a>1.12.5953.1
* Bug 修复

## <a name="11159181"></a>1.11.5918.1
* 网关事件日志的最大大小已从 1 MB 增加到 40 MB。
* 网关自动更新期间如需重启，将显示警告对话框。 可选择立即重启或稍后重启。 
* 如果自动更新失败，网关安装程序最多重试三次自动更新。
* 性能提升
  * 在无代码复制情况下，提升了从本地服务器加载大型表的性能。
* Bug 修复

## <a name="11058921"></a>1.10.5892.1
* 性能提升
* Bug 修复

## <a name="1958652"></a>1.9.5865.2
* 零触摸自动更新功能
* 具有网关状态指示器的新托盘图标
* 从客户端“立即更新”的功能
* 设置更新计划时间的功能
* 用于切换开/关自动更新的 PowerShell 脚本
* 支持 JSON 格式  
* 性能提升
* Bug 修复

## <a name="1858221"></a>1.8.5822.1
* 提升故障排除体验
* 性能提升
* Bug 修复

### <a name="1757951"></a>1.7.5795.1
* 性能提升
* Bug 修复

### <a name="1757641"></a>1.7.5764.1
* 性能提升
* Bug 修复

### <a name="1657351"></a>1.6.5735.1
* 支持本地 HDFS 源/接收器
* 性能提升
* Bug 修复

### <a name="1656961"></a>1.6.5696.1
* 性能提升
* Bug 修复

### <a name="1656761"></a>1.6.5676.1
* 支持 Configuration Manager 的诊断工具
* 支持用于 Azure 数据工厂的表格数据源的表列
* 支持用于 Azure 数据工厂的 SQL DW
* 支持用于 Azure 数据工厂的 BlobSource 和 FileSource 中的隐藏
* 支持 CopyBehavior - Azure 数据工厂中包含二进制副本的 BlobSink 和 FileSink 的 MergeFiles、PreserveHierarchy 和 FlattenHierarchy
* 支持用于 Azure 数据工厂的复制活动报告进程
* 支持用于 Azure 数据工厂的数据源连接验证
* Bug 修复

### <a name="1656721"></a>1.6.5672.1
* 支持用于 Azure 数据工厂的 ODBC 数据源的表名称
* 性能提升
* Bug 修复

### <a name="1656581"></a>1.6.5658.1
* 支持用于 Azure 数据工厂的文件接收器
* 支持为 Azure 数据工厂在二进制副本中保留层次结构
* 支持用于 Azure 数据工厂的复制活动幂等性
* Bug 修复

### <a name="1656401"></a>1.6.5640.1
* 支持用于 Azure 数据工厂的额外 3 个数据源（ODBC、OData、HDFS）
* 支持用于 Azure 数据工厂的 csv 分析程序中的引号字符
* 压缩支持 (BZip2)
* Bug 修复

### <a name="1556121"></a>1.5.5612.1
* 支持用于 Azure 数据工厂的 5 个关系数据库（MySQL、PostgreSQL、DB2、Teradata 和 Sybase）
* 压缩支持（Gzip 和 Deflate）
* 性能提升
* Bug 修复

### <a name="1455491"></a>1.4.5549.1
* 增加了对 Azure 数据工厂的 Oracle 数据源支持
* 性能提升
* Bug 修复

### <a name="1454921"></a>1.4.5492.1
* 支持 Microsoft Azure 数据工厂和 Office 365 Power BI 服务的统一二进制文件
* 优化配置 UI 和注册过程
* Azure 数据工厂 - 对 SQL Server 数据源的 Azure 入口和出口支持

### <a name="1253031"></a>1.2.5303.1
* 解决了超时问题，可支持更多的耗时数据源连接。 

### <a name="1155268"></a>1.1.5526.8
* 安装期间需要必备组件 .NET Framework 4.5.1。

### <a name="1051442"></a>1.0.5144.2
* 未进行影响 Azure 数据工厂方案的更改。 




<!--HONumber=Nov16_HO3-->


