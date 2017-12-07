---
title: "排查 Azure Migrate 问题 | Microsoft 文档"
description: "概述 Azure Migrate 服务中的已知问题，并针对常见错误提供故障排除技巧。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 40faffa3f-1f44-4a72-94bc-457222ed7ac8
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 8c7c79a23ee09a7de35252d7819d1f0e5b1d98c5
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-azure-migrate"></a>排查 Azure Migrate 问题

## <a name="troubleshoot-common-errors"></a>排查常见错误

[Azure Migrate](migrate-overview.md) 会评估要迁移到 Azure 的本地工作负载。 使用本文内容来排查在部署和使用 Azure Migrate 期间出现的问题。


收集器不能使用从门户复制的项目 ID 和密钥连接到项目。

请确保你已复制和粘贴正确信息。 若要排查问题，请按照以下步骤安装 Microsoft Monitoring Agent (MMA)：

1. 在收集器 VM 上，下载 [MMA](https://go.microsoft.com/fwlink/?LinkId=828603)。
2. 请双击下载文件，开始安装。
3. 在设置中的“欢迎”页面上，单击“下一步”。 在“许可条款”页面上，单击“我接受”以接受许可协议。
4. 在“目标文件夹”中，保留或修改默认安装文件夹，然后选择“下一步”。
5. 在“代理安装选项”中，选择“Azure Log Analytics (OMS)” > “下一步”。
6. 单击“添加”以添加 Log Analytics 工作区。 粘贴复制的项目 ID 和密钥。 然后单击“下一步”。
7. 验证代理是否可以连接到该项目。 如果不能，则验证设置。 如果代理可以连接，但收集器无法连接，请联系支持部门。


错误 802：收到日期和时间同步错误。

服务器时钟可能与当前时间不同步，相差超过 5 分钟。 更改收集器 VM 上的时钟时间以匹配当前时间，如下所示：

1. 在 VM 上打开管理员命令提示符。
2. 若要检查时区，请运行 w32tm /tz。
3. 若要同步时间，请运行 w32tm /resync。

我的项目密钥在末端有“==”符号。这些符号通过收集器编码为其他字母数字字符。这是正常情况吗？

是的，每个项目密钥均以“==”结尾。 收集器在处理项目密钥前会对其进行加密。

磁盘和网络适配器的性能数据显示为零

如果在 vCenter 服务器上的统计信息设置级别设置为小于 3，便会出现此问题。 在级别 3 或更高级别上，vCenter 存储有关计算、存储和网络的 VM 性能历史记录。 如果小于级别 3，vCenter 将不会存储存储器和网络数据，而只存储 CPU 和内存数据。 在此方案中，性能数据在 Azure Migrate 中显示为零，而 Azure Migrate 根据从本地计算机上收集的元数据，为磁盘和网络提供大小建议。

若要启用磁盘和网络性能数据收集，请将统计信息设置级别更改为 3。 然后，至少等待一天来发现环境并进行评估。 

我已安装代理并使用依赖项可视化效果创建组。现发布故障转移，计算机会显示“安装代理”操作，而不是“查看依赖项”
* 发布计划或计划外故障转移，本地计算机将处于关闭状态，而等效计算机会在 Azure 中处于启动状态。 这些计算机将获得一个不同的 MAC 地址。 它们可能会根据用户是否选择保留本地 IP 地址而获得不同的 IP 地址。 如果 MAC 和 IP 地址不同，Azure Migrate 则不会将本地计算机与任何服务映射依赖项数据关联起来，同时会要求用户安装代理，而不是查看依赖项。
* 发布测试故障转移，本地计算机会按预期保持开启状态。 在 Azure 中启动的等效计算机将获取不同的 MAC 地址，并且可能会获取不同的 IP 地址。 除非用户阻止从这些计算机输出 OMS 流量，否则 Azure Migrate 不会将本地计算机与任何服务映射依赖项数据关联起来，同时会要求用户安装代理，而不是查看依赖项。


## <a name="troubleshoot-readiness-issues"></a>排查就绪性问题

**问题** | **修补程序**
--- | ---
不支持启动类型 | 在运行迁移之前更改为 BIOS。
磁盘计数超过限制 | 在迁移之前从计算机中删除未使用的磁盘。
磁盘大小超过限制 | 在迁移之前将磁盘压缩至小于 4 TB。 
指定位置中磁盘不可用 | 在迁移之前请确保磁盘已在目标位置。
不可用于指定冗余的磁盘 | 磁盘应使用在评估设置中定义的冗余存储类型（默认为 LRS）。
由于内部错误无法确定磁盘适用性 | 请尝试为组创建一个新评估。 
未找到具有所需内核和内存的 VM | Azure 无法使用合适的 VM 类型。 在迁移之前请减少本地计算机的内存和内核数。 
存在一个或多个不合适磁盘。 | 在运行迁移之前，请确保本地磁盘为 4 TB 或以下。
存在一个或多个不合适网络适配器。 | 在迁移之前从计算机中删除未使用的网络适配器。
由于内部错误，无法确定 VM 适用性。 | 请尝试为组创建一个新评估。 
由于内部错误，无法确定一个或多个磁盘的适用性。 | 请尝试为组创建一个新评估。
由于内部错误，无法确定一个或多个网络适配器的适用性。 | 请尝试为组创建一个新评估。
未找到所需存储性能的 VM。 | 计算机所需的存储性能（IOPS/吞吐量）超出了 Azure VM 支持范围。 在迁移之前，减少计算机的存储需求。
未找到具有所需网络性能的 VM。 | 计算机所需的网络性能（输入/输出）超出了 Azure VM 支持。 减少计算机的网络要求。 
未找到具有指定定价层的 VM。 | 检查定价层设置。 
未在指定位置找到 VM。 | 在迁移之前使用不同目标位置。
Linux OS 支持问题 | 请确保使用这些支持的[操作系统](../virtual-machines/linux/endorsed-distros.md)运行 64 位应用。
Windows OS 支持问题 | 请确保正在运行受支持的操作系统。 [了解详细信息](concepts-assessment-calculation.md#azure-suitability-analysis)
未知操作系统。 | 请检查 vCenter 中指定的操作系统是否正确，并重复发现过程。
需要 Visual Studio 订阅。 | Visual Studio (MSDN) 订阅上仅支持 Windows 客户端操作系统。


## <a name="collect-logs"></a>收集日志

如何在收集器 VM 上收集日志？

日志记录默认处于启用状态。 日志位于以下位置：

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

若要收集 Windows 事件跟踪，请执行以下操作：

1. 在收集器 VM 上打开 PowerShell 命令窗口。
2. 运行 Get-EventLog -LogName Application | export-csv eventlog.csv。

如何收集门户网络流量日志？

1. 打开浏览器，导航并登录到[门户](https://portal.azure.com)。
2. 按 F12 键启动“开发人员工具”。 如果需要，请清除设置“清除导航上的条目”。
3. 单击“网络”选项卡，然后开始捕获网络流量：
 - 在 Chrome 中，选择“保留日志”。 记录应自动启动。 红色圆圈指示正在捕获流量。 如果未显示，单击黑色圆圈启动
 - 在 Edge/IE 中，记录应自动启动。 如果未启动，请单击绿色播放按钮。
4. 尝试再现该错误。
5. 在记录过程中遇到错误后，停止记录，并保存一份已记录的活动：
 - 在 Chrome 中，右键单击并选择“将内容另存为 HAR”。 此操作会将日志压缩并导出为 .har 文件。
 - 在 Edge/IE 中，单击“导出捕获流量”图标。 此操作会压缩并导出文件。
6. 导航到“控制台”选项卡，以查看任何警告或错误。 保存控制台日志：
 - 在 Chrome 中，右键单击控制台日志中的任意位置。 选择“另存为”，以导出和压缩日志。
 - 在 Edge/IE 中，右键单击错误并选择“复制所有”。 
7. 关闭“开发人员工具”。
 



