---
title: 排查 Azure Migrate 问题 | Microsoft 文档
description: 概述 Azure Migrate 服务中的已知问题，并针对常见错误提供故障排除技巧。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: raynew
ms.openlocfilehash: 0c7d0980c928ecefebeabff555378230453c742f
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827935"
---
# <a name="troubleshoot-azure-migrate"></a>排查 Azure Migrate 问题

## <a name="troubleshoot-common-errors"></a>排查常见错误

[Azure Migrate](migrate-overview.md) 会评估要迁移到 Azure 的本地工作负载。 使用本文内容来排查在部署和使用 Azure Migrate 期间出现的问题。

### <a name="i-am-using-the-ova-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>我正在使用持续发现本地环境的 OVA，但在本地环境中删除的 VM 仍在门户中显示。

持续发现设备仅持续收集性能数据，它不会检测本地环境中的任何配置更改（即 VM 添加、删除、磁盘添加等）。 如果本地环境中存在配置更改，可以执行以下操作以在门户中反映更改：

- 添加项（VM、磁盘、核心等）：若要在 Azure 门户中反映这些更改，可以从设备停止发现，然后重启发现。 这可确保在 Azure Migrate 项目中更新更改。

   ![停止发现](./media/troubleshooting-general/stop-discovery.png)

- 删除 VM：由于设备的设计方式，即使停止并启动发现，也不会反映出 VM 已删除这一更改。 这是因为后续发现的数据会追加到较旧的发现后，而不是进行覆盖。 在这种情况下，可以通过从组中删除 VM 并重新计算评估来直接忽略门户中的 VM。

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>删除 Azure Migrate 项目和关联的 Log Analytics 工作区

删除 Azure Migrate 项目时，会删除迁移项目以及所有组和评估。 但是，如果已将 Log Analytics 工作区附加到项目，则不会自动删除 Log Analytics 工作区。 这是因为相同的 Log Analytics 工作区可能用于多个用例。 如果也想要删除 Log Analytics 工作区，需要手动执行该操作。

1. 浏览到附加到该项目的 Log Analytics 工作区。
   a. 如果尚未删除迁移项目，则可以从“Essentials”部分的项目概述页中找到指向工作区的链接。

   ![LA 工作区](./media/troubleshooting-general/LA-workspace.png)

   b. 如果已删除迁移项目，请单击 Azure 门户左窗格中的“资源组”，然后转到创建工作区的资源组，然后浏览到该资源组。
2. 按照[本文](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)中的说明删除工作区。

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>无法创建迁移项目，错误消息为“请求必须包含用户标识头”

如果用户无权访问组织的 Azure Active Directory (Azure AD) 租户，可能会发生此问题。 首次添加到 Azure AD 租户中时，用户会收到一封电子邮件，邀请其加入租户。 用户需要访问电子邮件并接受邀请，才能被成功添加到租户中。 如果看不到电子邮件，请联系已有权访问租户的用户，让其按照[此处](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)介绍的步骤操作，重新向你发送邀请。

收到邀请电子邮件后，需要打开电子邮件，并单击电子邮件中的链接来接受邀请。 完成此操作后，需要注销 Azure 门户并重新登录，刷新浏览器并不起作用。 然后，可以尝试创建迁移项目。

### <a name="i-am-unable-to-export-the-assessment-report"></a>无法导出评估报告

如果无法从门户导出评估报告，请尝试使用以下 REST API 来获取评估报告的下载 URL。

1. 在计算机上安装“armclient”（如果尚未安装）：

  a. 在管理员命令提示符窗口中，运行以下命令：```@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"```

  b. 在管理员 Windows PowerShell 窗口中，运行以下命令：```choco install armclient```

2.  使用 Azure Migrate REST API 获取评估报告的下载 URL

  a.    在管理员 Windows PowerShell 窗口中，运行以下命令：```armclient login```

  此操作将打开 Azure 登录弹出窗口，你需要在此窗口中登录 Azure。

  b.    在同一 PowerShell 窗口中，请运行以下命令以获取评估报告的下载 URL（使用适当的值替换 URI 参数，下面是示例 API 请求）

       ```armclient POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Migrate/projects/{projectName}/groups/{groupName}/assessments/{assessmentName}/downloadUrl?api-version=2018-02-02```

       示例请求和输出：

       ```PS C:\WINDOWS\system32> armclient POST https://management.azure.com/subscriptions/8c3c936a-c09b-4de3-830b-3f5f244d72e9/r
esourceGroups/ContosoDemo/providers/Microsoft.Migrate/projects/Demo/groups/contosopayroll/assessments/assessment_11_16_2
018_12_16_21/downloadUrl?api-version=2018-02-02
{
  "assessmentReportUrl": "https://migsvcstoragewcus.blob.core.windows.net/4f7dddac-f33b-4368-8e6a-45afcbd9d4df/contosopayrollassessment_11_16_2018_12_16_21?sv=2016-05-31&sr=b&sig=litQmHuwi88WV%2FR%2BDZX0%2BIttlmPMzfVMS7r7dULK7Oc%3D&st=2018-11-20T16%3A09%3A30Z&se=2018-11-20T16%3A19%3A30Z&sp=r",
  "expirationTime": "2018-11-20T22:09:30.5681954+05:30"```

3. 复制响应中的 URL，并在浏览器中打开它以下载评估报告。

4. 下载报告后，使用 Excel 浏览到下载的文件夹，然后在 Excel 中打开文件进行查看。

### <a name="performance-data-for-disks-and-networks-adapters-shows-as-zeros"></a>磁盘和网络适配器的性能数据显示为零

如果在 vCenter 服务器上的统计信息设置级别设置为小于 3，便会出现此问题。 在级别 3 或更高级别上，vCenter 存储有关计算、存储和网络的 VM 性能历史记录。 如果小于级别 3，vCenter 将不会存储存储器和网络数据，而只存储 CPU 和内存数据。 在此方案中，性能数据在 Azure Migrate 中显示为零，而 Azure Migrate 根据从本地计算机上收集的元数据，为磁盘和网络提供大小建议。

若要启用磁盘和网络性能数据收集，请将统计信息设置级别更改为 3。 然后，至少等待一天来发现环境并进行评估。

### <a name="i-specified-an-azure-geography-while-creating-a-migration-project-how-do-i-find-out-the-exact-azure-region-where-the-discovered-metadata-would-be-stored"></a>创建迁移项目时，我已指定 Azure 地理位置，如何找到将存储所发现元数据的确切 Azure 区域？

你可以转到项目“概述”页中的“Essentials”部分来标识存储元数据的确切位置。 位置是由 Azure Migrate 在地理位置内随机选择的，你无法进行修改。 如果希望仅在特定区域中创建项目，则可以使用 REST API 来创建迁移项目并传递到所需的区域。

   ![项目位置](./media/troubleshooting-general/geography-location.png)

## <a name="collector-issues"></a>收集器问题

### <a name="deployment-of-azure-migrate-collector-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Azure Migrate 收集器的部署失败，并显示以下错误：提供的清单文件无效：OVF 清单条目无效。

1. 通过检查其哈希值，验证是否已正确下载 Azure Migrate 收集器 OVA 文件。 请参阅[本文](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance)来验证哈希值。 如果哈希值不匹配，请再次下载 OVA 文件并重试部署。
2. 如果仍然失败，并且使用的是 VMware vSphere 客户端来部署 OVF，请尝试通过 vSphere Web 客户端对其进行部署。 如果仍然失败，请尝试使用其他 Web 浏览器。
3. 如果使用的是 vSphere Web 客户端并尝试在 vCenter Server 6.5 或 6.7 上部署，请按照以下步骤尝试直接在 ESXi 主机上部署 OVA：
  - 使用 Web 客户端（ https://<主机 IP 地址>/ui）直接连接 ESXi 主机（而不是 vCenter Server）
  - 转到“主页”>“库存”
  - 单击“文件”>“部署 OVF 模板”>“浏览到 OVA”，并完成部署
4. 如果部署仍然失败，请联系 Azure Migrate 支持部门。


### <a name="collector-is-not-able-to-connect-to-the-internet"></a>收集器无法连接到 Internet

若所用的计算机使用代理，则可能发生这种情况。 请确保提供代理所需的授权凭据。
如果使用任何基于 URL 的防火墙代理控制出站连接，请确保将下列必需的 URL 列入允许列表：

**URL** | **用途**  
--- | ---
*.portal.azure.com | 需检查与 Azure 服务的连接并验证时间同步问题。
*.oneget.org | 需下载基于 PowerShell 的 vCenter PowerCLI 模块。

**由于证书验证失败，收集器而无法连接到 Internet**

如果使用拦截代理连接到 Internet，并且尚未将代理证书导入收集器 VM，则可能会发生这种情况。 可以使用[此处](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity)详述的步骤导入代理证书。

收集器不能使用从门户复制的项目 ID 和密钥连接到项目。

请确保你已复制和粘贴正确信息。 若要进行故障排除，请安装 Microsoft Monitoring Agent (MMA)，并验证 MMA 是否可以连接到该项目，如下所述：

1. 在收集器 VM 上，下载 [MMA](https://go.microsoft.com/fwlink/?LinkId=828603)。
2. 请双击下载文件，开始安装。
3. 在设置中的“欢迎”页面上，单击“下一步”。 在“许可条款”页面上，单击“我接受”以接受许可协议。
4. 在“目标文件夹”中，保留或修改默认安装文件夹，然后选择“下一步”。
5. 在“代理安装选项”中，选择“Azure Log Analytics” > “下一步”。
6. 单击“添加”以添加 Log Analytics 工作区。 粘贴复制的项目 ID 和密钥。 然后单击“下一步”。
7. 验证代理是否可以连接到该项目。 如果不能，则验证设置。 如果代理可以连接，但收集器无法连接，请联系支持部门。


### <a name="error-802-date-and-time-synchronization-error"></a>错误 802：日期和时间同步错误

服务器时钟可能与当前时间不同步，相差超过 5 分钟。 更改收集器 VM 上的时钟时间以匹配当前时间，如下所示：

1. 在 VM 上打开管理员命令提示符。
2. 若要检查时区，请运行 w32tm /tz。
3. 若要同步时间，请运行 w32tm /resync。

### <a name="vmware-powercli-installation-failed"></a>无法安装 VMware PowerCLI

Azure Migrate 收集器下载 PowerCLI，并在设备上安装它。 无法安装 PowerCLI 可能是因为 PowerCLI 存储库的终结点无法访问。 若要排除故障，请尝试按照以下步骤操作，在收集器 VM 中手动安装 PowerCLI：

1. 在管理员模式下打开 Windows PowerShell
2. 转到目录 C:\ProgramFiles\ProfilerService\VMWare\Scripts\
3. 运行脚本 InstallPowerCLI.ps1

### <a name="error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception"></a>发生 UnhandledException 内部错误：System.IO.FileNotFoundException

出现此问题可能是由于存在 VMware PowerCLI 安装问题。 请遵循以下步骤来解决该问题：

1. 如果你使用的不是最新版本的收集器设备，请[将收集器升级到最新版本](https://aka.ms/migrate/col/checkforupdates)，然后检查问题是否得到解决。
2. 如果已经是最新版本的收集器，则请手动安装 [VMware PowerCLI 6.5.2](https://www.powershellgallery.com/packages/VMware.PowerCLI/6.5.2.6268016)，然后检查问题是否得到解决。
3. 如果上述步骤都未能解决问题，请导航到 C:\Program Files\ProfilerService 文件夹并删除其中的 VMware.dll 和 VimService65.dll 文件，然后在 Windows 服务管理器中重新启动“Azure Migrate 收集器”服务（打开“运行”并键入“services.msc”，可打开 Windows 服务管理器）。

### <a name="error-unabletoconnecttoserver"></a>UnableToConnectToServer 错误

由于错误，无法连接到 vCenter Server“Servername.com:9443”： https://Servername.com:9443/sdk 处没有可接受消息的终结点侦听。

检查要运行的收集器设备是否是最新版本。如果不是，将设备升级到[最新版本](https://docs.microsoft.com/azure/migrate/concepts-collector#how-to-upgrade-collector)。

如果是最新版本但此问题仍出现，可能是因为收集器设备无法解析指定的 vCenter Server 名称，或指定的端口不正确。 默认情况下，如果端口未指定，收集器会尝试连接到端口号 443。

1. 请尝试在收集器设备中对 Servername.com 执行 ping 操作。
2. 如果步骤 1 失败，请尝试通过 IP 地址连接到 vCenter Server。
3. 确定可连接到 vCenter 的正确端口号。
4. 最后检查 vCenter Server 是否已启动并运行。

### <a name="antivirus-exclusions"></a>防病毒排除项

若要强化 Azure Migrate 设备，需要从防病毒扫描中排除设备中的以下文件夹：

- 具有 Azure Migrate 服务的二进制文件的文件夹。 排除所有子文件夹。
  %ProgramFiles%\ProfilerService  
- Azure Migrate Web 应用程序。 排除所有子文件夹。
  %SystemDrive%\inetpub\wwwroot
- 数据库和日志文件的本地缓存。 Azure Migrate 服务需要对此文件夹拥有 RW 访问权限。
  %SystemDrive%\Profiler

## <a name="dependency-visualization-issues"></a>依赖项可视化效果问题

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>找不到 Azure 政府项目的依赖项可视化效果功能。

Azure Migrate 取决于服务映射以获取依赖项可视化效果功能，并且由于服务映射当前在 Azure 政府中不可用，因此 Azure 政府中不提供此功能。

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>我本地的 VM 上安装了 Microsoft Monitoring Agent (MMA) 和依赖项代理，但依赖项现在显示在 Azure Migrate 门户中。

你安装这些代理后，Azure Migrate 通常需要 15-30 分钟才能显示门户中的依赖项。 如果等待的时间超过 30 分钟，请确保 MMA 代理能够按照以下步骤与 OMS 工作区进行通信：

对于 Windows VM：
1. 请转到“控制面板”，然后启动 Microsoft Monitoring Agent
2. 转到“MMA 属性”弹出窗口中的 Azure Log Analytics (OMS) 选项卡
3. 确保工作区的“状态”为绿色。
4. 如果状态不是绿色，请尝试删除工作区，然后再次将其添加到 MMA。
        ![MMA 状态](./media/troubleshooting-general/mma-status.png)

对于 Linux VM，请确保 MMA 和依赖项代理的安装命令已成功运行。

### <a name="what-are-the-operating-systems-supported-by-mma"></a>MMA 支持的操作系统有哪些？

[此处](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)列出了 MMA 支持的 Windows 操作系统。
[此处](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)列出了 MMA 支持的 Linux 操作系统。

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>依赖项代理支持的操作系统有哪些？

[此处](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)列出了依赖项代理支持的 Windows 操作系统。
[此处](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)列出了依赖项代理支持的 Linux 操作系统。

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>我在 Azure Migrate 中可视化依赖项不能持续超过一小时？
Azure Migrate 允许可视化依赖项最多持续一小时的时间。 尽管 Azure Migrate 允许返回到历史记录中的某一特定日期可以推至上个月，但可视化依赖项的最长持续时间最多为一小时。 例如，你可以使用依赖项映射中的持续时间功能来查看昨天的依赖项，但只能查看一小时。

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>我无法可视化具有 10 个以上 VM 的组的依赖项？
你可以[可视化依赖项的组最多只能有 10 个 VM](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)，如果某个组的 VM 超过 10 个，建议先将该组拆分成较小的组，然后再可视化依赖项。

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>我已安装代理并使用依赖项可视化效果创建组。 现在，在执行故障转移后，计算机显示“安装代理”操作，而不是“查看依赖项”
* 发布计划或计划外故障转移，本地计算机将处于关闭状态，而等效计算机会在 Azure 中处于启动状态。 这些计算机将获得一个不同的 MAC 地址。 它们可能会根据用户是否选择保留本地 IP 地址而获得不同的 IP 地址。 如果 MAC 和 IP 地址不同，Azure Migrate 则不会将本地计算机与任何服务映射依赖项数据关联起来，同时会要求用户安装代理，而不是查看依赖项。
* 发布测试故障转移，本地计算机会按预期保持开启状态。 在 Azure 中启动的等效计算机将获取不同的 MAC 地址，并且可能会获取不同的 IP 地址。 除非用户阻止从这些计算机输出 Log Analytics 流量，否则 Azure Migrate 不会将本地计算机与任何服务映射依赖项数据关联起来，同时会要求用户安装代理，而不是查看依赖项。

## <a name="troubleshoot-azure-readiness-issues"></a>排查 Azure 迁移就绪性问题

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
 - 在 Microsoft Edge/IE 中，记录应自动启动。 如果未启动，请单击绿色播放按钮。
4. 尝试再现该错误。
5. 在记录过程中遇到错误后，停止记录，并保存一份已记录的活动：
 - 在 Chrome 中，右键单击并选择“将内容另存为 HAR”。 此操作会将日志压缩并导出为 .har 文件。
 - 在 Microsoft Edge/IE 中，单击“导出捕获流量”图标。 此操作会压缩并导出文件。
6. 导航到“控制台”选项卡，以查看任何警告或错误。 保存控制台日志：
 - 在 Chrome 中，右键单击控制台日志中的任意位置。 选择“另存为”，以导出和压缩日志。
 - 在 Microsoft Edge/IE 中，右键单击错误并选择“复制所有”。
7. 关闭“开发人员工具”。

## <a name="collector-error-codes-and-recommended-actions"></a>收集器错误代码和建议的操作

| 错误代码 | 错误名称   | 消息   | 可能的原因 | 建议的操作  |
| --- | --- | --- | --- | --- |
| 601       | CollectorExpired               | 收集器已过期。                                                        | 收集器已过期。                                                                                    | 请下载新版本的收集器，然后重试。                                                                                      |
| 751       | UnableToConnectToServer        | 由于出现错误，无法连接到 vCenter Server“%Name;”: %ErrorMessage;     | 检查错误消息以了解更多详细信息。                                                             | 解决问题，然后重试。                                                                                                           |
| 752       | InvalidvCenterEndpoint         | 服务器“%Name;”不是 vCenter Server。                                  | 请提供 vCenter Server 详细信息。                                                                       | 利用正确的 vCenter Server 详细信息，重试操作。                                                                                   |
| 753       | InvalidLoginCredentials        | 由于出现错误，无法连接到 vCenter Server“%Name;”: %ErrorMessage; | 由于登录凭据无效，连接到 vCenter Server 失败。                             | 请确保所提供的登录凭据正确无误。                                                                                    |
| 754       | NoPerfDataAvailable           | 性能数据不可用。                                               | 在 vCenter Server 中检查统计信息级别。 它应设置为 3，性能数据才可用。 | 将统计信息级别更改为 3（适用于 5 分钟、30 分钟和 2 小时持续时间），并在至少等待一天后重试。                   |
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
