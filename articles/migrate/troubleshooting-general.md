---
title: 排查 Azure Migrate 问题 | Microsoft 文档
description: 概述 Azure Migrate 服务中的已知问题，并针对常见错误提供故障排除技巧。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 03/19/2018
ms.author: raynew
ms.openlocfilehash: b2c89a980411cac02f46bc91d53620bc94fa845b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="troubleshoot-azure-migrate"></a>排查 Azure Migrate 问题

## <a name="troubleshoot-common-errors"></a>排查常见错误

[Azure Migrate](migrate-overview.md) 会评估要迁移到 Azure 的本地工作负载。 使用本文内容来排查在部署和使用 Azure Migrate 期间出现的问题。


收集器不能连接到 Internet

若所用的计算机使用代理，则可能发生这种情况。 请确保提供代理所需的授权凭据。
如果使用任何基于 URL 的防火墙代理控制出站连接，请确保将下列必需的 URL 列入白名单：

**URL** | **用途**  
--- | ---
*.portal.azure.com | 需检查与 Azure 服务的连接并验证时间同步问题。
*.oneget.org | 需下载基于 PowerShell 的 vCenter PowerCLI 模块。

收集器不能使用从门户复制的项目 ID 和密钥连接到项目。

请确保你已复制和粘贴正确信息。 若要进行故障排除，请安装 Microsoft Monitoring Agent (MMA)，并验证 MMA 是否可以连接到该项目，如下所述：

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

我的项目密钥在末端有“==”符号。这些符号通过收集器编码为其他字母数字字符。**这是正常情况吗？

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
引导类型不受支持 | Azure 不支持引导类型为 EFI 的 VM。 建议在运行迁移之前将引导类型转换为 BIOS。 <br/><br/>可以使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) 执行此类 VM 的迁移，因为它在迁移期间会将 VM 的引导类型转换为 BIOS。
有条件支持的 Windows OS | OS 已过了其支持日期结束时间，并且需要一个自定义支持协议 (CSA) 才能[在 Azure 中受支持](https://aka.ms/WSosstatement)，请考虑在迁移到 Azure 之前升级 OS。
不受支持的 Windows OS | Azure 仅支持[所选的 Windows OS 版本](https://aka.ms/WSosstatement)，请考虑在迁移到 Azure 之前升级计算机的 OS。
有条件认可的 Linux OS | Azure 仅认可[所选的 Linux OS 版本](../virtual-machines/linux/endorsed-distros.md)，请考虑在迁移到 Azure 之前升级计算机的 OS。
未经认可的 Linux OS | 计算机可以在 Azure 中引导，但是 Azure 未提供 OS 支持，请考虑在迁移到 Azure 之前将 OS 升级到[经认可的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)
未知操作系统 | VM 的操作系统在 vCenter Server 中指定为“其他”，因此，Azure Migrate 无法识别 VM 的 Azure 迁移就绪性。 在迁移计算机之前，请确保 Azure [支持](https://aka.ms/azureoslist)计算机中运行的 OS。
不受支持的 OS 位数 | 采用 32 位 OS 的 VM 可以在 Azure 中引导，但建议在迁移到 Azure 之前将 VM 的 OS 从 32 位升级到 64 位。
需要 Visual Studio 订阅。 | 计算机中运行了一个仅在 Visual Studio 订阅中受支持的 Windows 客户端 OS。
未找到所需存储性能的 VM。 | 计算机所需的存储性能（IOPS/吞吐量）超出了 Azure VM 支持范围。 在迁移之前，减少计算机的存储需求。
未找到具有所需网络性能的 VM。 | 计算机所需的网络性能（输入/输出）超出了 Azure VM 支持。 减少计算机的网络要求。
在指定的定价层未找到 VM。 | 如果定价层设置为“标准”，请考虑在迁移到 Azure 之前缩小 VM。 如果大小调整为“基本”，请考虑将评估的定价层更改为“标准”。
未在指定位置找到 VM。 | 在迁移之前使用不同目标位置。
存在一个或多个不合适磁盘。 | 附加到 VM 的一个或多个磁盘不符合 Azure 要求。 对于附加到 VM 的每个磁盘，请确保磁盘的大小 < 4 TB，如果不是，请在迁移到 Azure 之前缩小磁盘大小。 请确保 Azure [托管虚拟机磁盘](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)支持每个磁盘所需的性能（IOPS/吞吐量）。   
存在一个或多个不合适网络适配器。 | 在迁移之前从计算机中删除未使用的网络适配器。
磁盘计数超过限制 | 在迁移之前从计算机中删除未使用的磁盘。
磁盘大小超过限制 | Azure 支持大小最高为 4 TB 的磁盘。 在迁移之前将磁盘压缩至小于 4 TB。
指定位置中磁盘不可用 | 在迁移之前请确保磁盘已在目标位置。
不可用于指定冗余的磁盘 | 磁盘应使用在评估设置中定义的冗余存储类型（默认为 LRS）。
由于内部错误无法确定磁盘适用性 | 请尝试为组创建一个新评估。
未找到具有所需内核和内存的 VM | Azure 无法使用合适的 VM 类型。 在迁移之前请减少本地计算机的内存和内核数。
由于内部错误，无法确定 VM 适用性。 | 请尝试为组创建一个新评估。
由于内部错误，无法确定一个或多个磁盘的适用性。 | 请尝试为组创建一个新评估。
由于内部错误，无法确定一个或多个网络适配器的适用性。 | 请尝试为组创建一个新评估。


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


## <a name="vcenter-errors"></a>vCenter 错误

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>UnhandledException 错误 发生内部错误: System.IO.FileNotFoundException

这是在收集器版本低于 1.0.9.5 时出现的问题。 如果使用的收集器是 1.0.9.2 版或预发行版本（例如 1.0.8.59），则会遇到此问题。 请访问[此处提供的论坛链接，获取详细的解答](https://social.msdn.microsoft.com/Forums/azure/en-US/c1f59456-7ba1-45e7-9d96-bae18112fb52/azure-migrate-connect-to-vcenter-server-error?forum=AzureMigrate)。

[通过升级收集器来解决问题](https://aka.ms/migrate/col/checkforupdates)。

### <a name="error-unabletoconnecttoserver"></a>UnableToConnectToServer 错误

无法连接到 vCenter Server“Servername.com:9443”，因为存在以下错误：在可以接受消息的 https://Servername.com:9443/sdk 上没有终结点在侦听。

当收集器计算机无法解析指定的 vCenter Server 名称或者指定的端口错误时，会发生这种情况。 默认情况下，如果端口未指定，收集器会尝试连接到端口号 443。

1. 尝试从收集器计算机 ping Servername.com。
2. 如果步骤 1 失败，请尝试通过 IP 地址连接到 vCenter Server。
3. 确定可连接到 vCenter 的正确端口号。
4. 最后检查 vCenter Server 是否已启动并运行。

## <a name="collector-error-codes-and-recommended-actions"></a>收集器错误代码和建议的操作

|           |                                |                                                                               |                                                                                                       |                                                                                                                                            | 
|-----------|--------------------------------|-------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------| 
| 错误代码 | 错误名称                      | 消息                                                                       | 可能的原因                                                                                        | 建议的操作                                                                                                                          | 
| 601       | CollectorExpired               | 收集器已过期。                                                        | 收集器已过期。                                                                                    | 请下载新版本的收集器，然后重试。                                                                                      | 
| 751       | UnableToConnectToServer        | 由于出现错误，无法连接到 vCenter Server“%Name;”: %ErrorMessage;     | 检查错误消息以了解更多详细信息。                                                             | 解决问题，然后重试。                                                                                                           | 
| 752       | InvalidvCenterEndpoint         | 服务器“%Name;”不是 vCenter Server。                                  | 请提供 vCenter Server 详细信息。                                                                       | 利用正确的 vCenter Server 详细信息，重试操作。                                                                                   | 
| 753       | InvalidLoginCredentials        | 由于出现错误，无法连接到 vCenter Server“%Name;”: %ErrorMessage; | 由于登录凭据无效，连接到 vCenter Server 失败。                             | 请确保所提供的登录凭据正确无误。                                                                                    | 
| 754       | NoPerfDataAvaialable           | 性能数据不可用。                                               | 在 vCenter Server 中检查统计信息级别。 它应设置为 3，性能数据才可用。 | 将统计信息级别更改为 3（适用于 5 分钟、30 分钟和 2 小时持续时间），并在至少等待一天后重试。                   | 
| 756       | NullInstanceUUID               | 遇到 InstanceUUID 为 null 的计算机                                  | vCenter Server 可能具有不合适的对象。                                                      | 解决问题，然后重试。                                                                                                           | 
| 757       | VMNotFound                     | 找不到虚拟机                                                  | 虚拟机可能被删除: %VMID;                                                                | 请确保在发现过程中限定 vCenter 清单存在时，选择虚拟机                                      | 
| 758       | GetPerfDataTimeout             | VCenter 请求超时。消息: %Message;                                  | vCenter Server 凭据不正确                                                              | 检查 vCenter Server 凭据，并确保 vCenter Server 可访问。 请重试操作即可。 如果该问题仍然存在，请联系支持部门。 | 
| 759       | VmwareDllNotFound              | 找不到 VMWare.Vim DLL。                                                     | PowerCLI 未正确安装。                                                                   | 请检查 PowerCLI 是否正确安装。 请重试操作即可。 如果该问题仍然存在，请联系支持部门。                               | 
| 800       | ServiceError                   | Azure Migrate 收集器服务未运行。                               | Azure Migrate 收集器服务未运行。                                                       | 使用 services.msc 启动该服务并重试此操作。                                                                             | 
| 801       | PowerCLIError                  | VMware PowerCLI 安装失败。                                          | VMware PowerCLI 安装失败。                                                                  | 请重试操作即可。 如果该问题仍然存在，请手动安装它，然后重试此操作。                                                   | 
| 802       | TimeSyncError                  | 时间未与 Internet 时间服务器同步。                            | 时间未与 Internet 时间服务器同步。                                                    | 请确保计算机上的时间已根据计算机的时区准确设置并重试此操作。                                 | 
| 702       | OMSInvalidProjectKey           | 指定的项目键无效。                                                | 指定的项目键无效。                                                                        | 请用正确的项目键重试此操作。                                                                                              | 
| 703       | OMSHttpRequestException        | 发送请求时出错。 消息: %Message;                                | 请检查项目 ID 和键，并确保终结点可访问。                                       | 请重试操作即可。 如果问题持续出现，请联系 Microsoft 支持。                                                                     | 
| 704       | OMSHttpRequestTimeoutException | HTTP 请求超时。消息: %Message;                                     | 请检查项目 ID 和键，并确保终结点可访问。                                       | 请重试操作即可。 如果问题持续出现，请联系 Microsoft 支持。                                                                     | 
