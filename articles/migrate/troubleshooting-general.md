---
title: 排查 Azure Migrate 问题 | Microsoft 文档
description: 概述 Azure Migrate 服务中的已知问题，并针对常见错误提供故障排除技巧。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: raynew
ms.openlocfilehash: 0e2a8f269a98babc17f36ceff209ee2f057e6911
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302313"
---
# <a name="troubleshoot-azure-migrate"></a>排查 Azure Migrate 问题

[Azure Migrate](migrate-services-overview.md)提供了一个用于评估和迁移的 Microsoft 工具中心, 以及第三方独立软件供应商 (ISV) 产品。 本文档提供有关排查 Azure Migrate 的错误、Azure Migrate 的帮助:服务器评估和 Azure Migrate:服务器迁移。

## <a name="azure-migrate-project-issues"></a>Azure Migrate 项目问题

### <a name="i-am-unable-to-find-my-existing-azure-migrate-project"></a>我找不到现有的 Azure Migrate 项目。

Azure Migrate 有[两个版本](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)。 根据你在其中创建项目的版本, 请按照以下步骤查找项目:

1. 如果正在查找使用 Azure Migrate 的早期版本 (旧经验) 创建的项目, 请按照以下步骤查找项目。

    1. 请参阅[Azure 门户](https://portal.azure.com), 搜索**Azure Migrate**。
    2. 在 Azure Migrate 仪表板上, 你将看到一个横幅, 其中讨论了如何访问较早的项目。 只有在创建了具有旧经验的项目时, 才会看到此横幅。 单击横幅。

    ![访问现有项目](./media/troubleshooting-general/access-existing-projects.png)

    3. 现在, 你将看到 Azure Migrate 的以前版本创建的现有项目的列表。

2. 如果正在查找使用当前版本 (新的体验) 创建的项目, 请按照以下步骤查找项目。

    1. 请参阅[Azure 门户](https://portal.azure.com), 搜索**Azure Migrate**。
    2. 在 Azure Migrate 仪表板上, 转到左侧窗格中的 "**服务器**" 页, 然后在右上角选择 "**更改**":

    ![切换到现有 Azure Migrate 项目](./media/troubleshooting-general/switch-project.png)

    3. 选择相应的**订阅**并**迁移项目**。

### <a name="i-am-unable-to-create-a-second-azure-migrate-project"></a>我无法创建第二个 Azure Migrate 项目。

按照以下步骤创建新 Azure Migrate 项目。

1. 请参阅[Azure 门户](https://portal.azure.com), 搜索**Azure Migrate**。
2. 在 Azure Migrate 仪表板上, 转到左侧窗格中的 "**服务器**" 页, 然后在右上角选择 "**更改**":

   ![更改 Azure Migrate 项目](./media/troubleshooting-general/switch-project.png)

3. 若要创建新项目, 请选择 "**单击此处**", 如下面的屏幕截图中所示:

   ![创建第二个 Azure Migrate 项目](./media/troubleshooting-general/create-new-project.png)

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>删除 Azure Migrate 项目和关联的 Log Analytics 工作区

删除 Azure Migrate 项目时, 它会删除迁移项目以及有关发现的计算机的元数据。 但是, 如果已将 Log Analytics 工作区附加到服务器评估工具, 它不会自动删除 Log Analytics 工作区。 这是因为相同的 Log Analytics 工作区可能用于多个用例。 如果也想要删除 Log Analytics 工作区，需要手动执行该操作。

1. 浏览到附加到该项目的 Log Analytics 工作区。
     1. 如果还未删除迁移项目, 可以从 "Essentials" 部分的 "服务器评估概述" 页中找到工作区的链接。

     ![LA 工作区](./media/troubleshooting-general/loganalytics-workspace.png)

     2. 如果已删除迁移项目, 请在 Azure 门户的左窗格中选择 "**资源组**"。 转到创建该工作区的资源组, 然后浏览到该工作区。
2. 按照[本文](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)中的说明删除工作区。

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>无法创建迁移项目，错误消息为“请求必须包含用户标识头” 

如果用户无权访问组织的 Azure Active Directory (Azure AD) 租户，可能会发生此问题。 首次添加到 Azure AD 租户中时，用户会收到一封电子邮件，邀请其加入租户。 用户需要访问电子邮件并接受邀请，才能被成功添加到租户中。 如果看不到电子邮件，请联系已有权访问租户的用户，让其按照[此处](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)介绍的步骤操作，重新向你发送邀请。

收到邀请电子邮件后，需要打开电子邮件，并单击电子邮件中的链接来接受邀请。 完成此操作后, 你需要注销 Azure 门户并再次登录, 刷新浏览器将不起作用。 然后，可以尝试创建迁移项目。

## <a name="appliance-issues"></a>设备问题

### <a name="deployment-of-azure-migrate-appliance-for-vmware-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>为 VMware 部署 Azure Migrate 设备失败, 出现以下错误:提供的清单文件无效：OVF 清单条目无效。

1. 通过检查 Azure Migrate 的设备的哈希值来验证是否已正确下载设备 .OVA 文件。 请参阅[本文](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance)来验证哈希值。 如果哈希值不匹配，请再次下载 OVA 文件并重试部署。
2. 如果仍然失败，并且使用的是 VMware vSphere 客户端来部署 OVF，请尝试通过 vSphere Web 客户端对其进行部署。 如果仍然失败，请尝试使用其他 Web 浏览器。
3. 如果使用的是 vSphere Web 客户端并尝试在 vCenter Server 6.5 或 6.7 上部署，请按照以下步骤尝试直接在 ESXi 主机上部署 OVA：
   - 使用 web 客户端 (https://<*主机 IP 地址*>/ui) 直接连接到 ESXi 主机, 而不是 vCenter Server。
   - 请参阅**家庭** > **清单**。
   - 单击 "**文件** > " "**部署 OVF 模板**"。 浏览到 .OVA 并完成部署。
4. 如果部署仍然失败，请联系 Azure Migrate 支持部门。

### <a name="appliance-is-not-able-to-connect-to-the-internet"></a>设备无法连接到 internet

若所用的计算机使用代理，则可能发生这种情况。 请确保提供代理所需的授权凭据。
如果使用任何基于 URL 的防火墙代理控制出站连接, 请确保将所需的 Url 添加到允许列表中:

应用场景 | URL 列表
--- | ---
针对 VMware 的服务器评估 | [这儿](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
适用于 Hyper-v 的服务器评估 | [这儿](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
适用于 VMware 的服务器迁移 (无代理) | [这儿](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
适用于 VMware 的服务器迁移 (基于代理) | [这儿](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
适用于 Hyper-v 的服务器迁移 | [这儿](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

如果使用截取代理连接到 Internet, 则需要将代理证书导入到设备 VM。 可以使用[此处](https://docs.microsoft.com/azure/migrate/concepts-collector)详述的步骤导入代理证书。

### <a name="error-802-date-and-time-synchronization-error"></a>错误 802：日期和时间同步错误

服务器时钟可能与当前时间不同步，相差超过 5 分钟。 更改收集器 VM 上的时钟时间以匹配当前时间，如下所示：

1. 在 VM 上打开管理员命令提示符。
2. 若要检查时区，请运行 w32tm /tz。
3. 若要同步时间，请运行 w32tm /resync。


### <a name="error-unabletoconnecttoserver"></a>UnableToConnectToServer 错误

由于错误，无法连接到 vCenter Server“Servername.com:9443”： https://Servername.com:9443/sdk 处没有可接受消息的终结点侦听。

检查要运行的收集器设备是否是最新版本。如果不是，将设备升级到[最新版本](https://docs.microsoft.com/azure/migrate/concepts-collector)。

如果是最新版本但此问题仍出现，可能是因为收集器设备无法解析指定的 vCenter Server 名称，或指定的端口不正确。 默认情况下，如果端口未指定，收集器会尝试连接到端口号 443。

1. 请尝试在收集器设备中对 Servername.com 执行 ping 操作。
2. 如果步骤 1 失败，请尝试通过 IP 地址连接到 vCenter Server。
3. 确定可连接到 vCenter 的正确端口号。
4. 最后检查 vCenter Server 是否已启动并运行。

## <a name="discovery-issues"></a>发现问题

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migrate-tiles-show-a-status-of-discovery-in-progress"></a>我已经开始发现, 但在 Azure 门户看不到发现的虚拟机。 服务器评估和服务器迁移磁贴显示状态 "正在发现"
从设备启动发现后, 请等待一段时间让发现的计算机显示在 Azure 门户上。 VMware 发现大约要花费15分钟, 对于 Hyper-v 发现, 每个添加的主机约2分钟。 如果仍在此时间之后仍看到 "正在发现", 请在 "**服务器**" 选项卡上单击 "**刷新**"。这会在 "服务器评估" 和 "服务器迁移" 磁贴中显示发现的服务器的计数。


### <a name="i-am-using-the-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>我使用的是持续发现本地环境的设备, 但在我的本地环境中删除的 Vm 仍会显示在门户中。

设备收集的发现数据需要长达30分钟的时间才能在门户中反映出来。 如果在30分钟后仍未看到最新信息, 请使用以下步骤对数据发出刷新:

1. 在服务器 > Azure Migrate:服务器评估中, 单击 "**概述**"。
2. 在 "**管理**" 下, 单击**代理运行状况**
3. 单击 "**刷新代理**" 选项。 你将在代理列表下方看到下面的选项。
4. 等待刷新操作完成。 验证是否能够查看 Vm 上的最新信息。

### <a name="i-dont-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>我不是门户上本地 Vm 的最新信息

设备收集的发现数据需要长达30分钟的时间才能在门户中反映出来。 如果在30分钟后仍未看到最新信息, 请使用以下步骤对数据发出刷新:

1. 在服务器 > Azure Migrate:服务器评估中, 单击 "**概述**"。
2. 在 "**管理**" 下, 单击**代理运行状况**
3. 单击 "**刷新代理**" 选项。 你将在代理列表下看到此选项。
4. 等待刷新操作完成。 现在, 你应会看到 Vm 上的最新信息。

### <a name="unable-to-connect-to-hosts-or-cluster-as-the-server-name-cannot-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>无法连接到主机或群集, 因为无法解析服务器名称。 WinRM 错误代码:0x803381B9 (错误 ID:50004)
如果为设备提供服务的 DNS 无法解析提供的群集或主机名, 则会发生此错误。 如果在群集上看到此错误, 请尝试提供群集的完全限定的域名。 

对于群集中的主机, 也可能出现此错误。 在这种情况下, 设备可以连接到群集。 但群集返回了不是完全限定的域名的主机名。 

若要解决此错误, 请更新设备上的 hosts 文件, 并添加 IP 地址和主机名的映射。
1. 以管理员用户身份打开记事本。 打开文件 C:\Windows\System32\Drivers\etc\hosts。
2. 在行中添加 IP 地址和主机名。 对于出现此错误的每个主机或群集重复此操作。
3. 保存并关闭 hosts 文件。
4. 你可以使用设备管理应用来检查设备是否可以连接到主机。 30分钟后, 你应该能够在 Azure 门户上看到这些主机上的最新信息。 


## <a name="assessment-issues"></a>评估问题

### <a name="azure-readiness-issues"></a>Azure 就绪问题

问题 | 补救
--- | ---
引导类型不受支持 | Azure 不支持引导类型为 EFI 的 VM。 在运行迁移之前, 建议将启动类型转换为 BIOS。 <br/><br/>你可以使用 Azure Migrate 服务器迁移来执行此类 Vm 的迁移, 因为它会在迁移过程中将 VM 的启动类型转换为 BIOS。
有条件支持的 Windows OS | OS 已过了其支持日期结束时间，并且需要一个自定义支持协议 (CSA) 才能[在 Azure 中受支持](https://aka.ms/WSosstatement)，请考虑在迁移到 Azure 之前升级 OS。
不受支持的 Windows OS | Azure 仅支持[所选的 Windows OS 版本](https://aka.ms/WSosstatement)，请考虑在迁移到 Azure 之前升级计算机的 OS。
有条件认可的 Linux OS | Azure 仅认可[所选的 Linux OS 版本](../virtual-machines/linux/endorsed-distros.md)，请考虑在迁移到 Azure 之前升级计算机的 OS。
未经认可的 Linux OS | 计算机可以在 Azure 中引导，但是 Azure 未提供 OS 支持，请考虑在迁移到 Azure 之前将 OS 升级到[经认可的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)
未知操作系统 | VM 的操作系统在 vCenter Server 中指定为“其他”，因此，Azure Migrate 无法识别 VM 的 Azure 迁移就绪性。 在迁移计算机之前，请确保 Azure [支持](https://aka.ms/azureoslist)计算机中运行的 OS。
不受支持的 OS 位数 | 采用 32 位 OS 的 VM 可以在 Azure 中引导，但建议在迁移到 Azure 之前将 VM 的 OS 从 32 位升级到 64 位。
需要 Visual Studio 订阅。 | 计算机上运行的是 Windows 客户端 OS, 这仅在 Visual Studio 订阅中受支持。
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

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>我无法将企业协议 (EA) 指定为评估属性中的 Azure 产品/服务？
Azure Migrate：服务器评估目前不支持基于企业协议 (EA) 的定价。 解决方法是使用 "即用即付" 作为 Azure 产品/服务, 并使用 "折扣" 属性指定收到的任何自定义折扣。 [了解有关如何自定义评估的详细信息](https://aka.ms/migrate/selfhelp/eapricing)。

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>为什么服务器评估将我的 Linux Vm 标记为 "有条件就绪"。 要解决此问题, 需要执行哪些操作？
服务器评估中有一个已知的间隔, 即无法检测本地 Vm 上安装的 Linux 操作系统的次版本 (例如, 对于 RHEL 6.10, 当前服务器评估只检测 RHEL 6 作为操作系统版本)。 由于 Azure 予以认可仅特定版本的 Linux, 因此 Linux Vm 当前在服务器评估中被标记为有条件准备就绪。 可以通过查看[Azure Linux 支持文档](https://aka.ms/migrate/selfhost/azureendorseddistros), 手动确保本地 VM 上运行的 Linux 操作系统是否已在 azure 中认可。 验证认可的发行版后, 可以忽略此警告。

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>服务器评估建议的 VM SKU 具有更多的内核数和比本地分配的内存大小更大的内存。 为什么要这样做呢？
服务器评估中的 VM SKU 建议取决于评估属性。 你可以在服务器评估中创建两种评估, 即 "基于性能" 和 "作为本地" 评估。 对于基于性能的评估, 服务器评估会考虑本地 Vm 的使用率数据 (CPU、内存、磁盘和网络使用率), 以确定本地 Vm 的正确目标 VM SKU。 此外, 对于基于性能的大小调整, 会考虑到确定有效利用率的舒适因素。 对于 "按本地大小调整", 不考虑性能数据, 建议根据本地分配的内容使用目标 SKU。

例如, 假设有一个具有4个核心和 8 GB 内存的本地 VM, 其 CPU 使用率为 50%, 内存使用率为 50%, 并且在评估中指定1.3 了。 如果评估的大小调整条件为 "本地", 则建议使用具有4个核心和 8 GB 内存的 Azure VM SKU, 但如果大小调整条件基于有效的 CPU 和内存利用率 (4 个内核的 50% * 1.3 = 2.6 个核心, 50%, 共 8 GB内存 * 1.3 = 5.3 GB 内存), 建议使用最便宜的四核 VM SKU (最接近受支持的内核数) 和 8 GB 的内存大小 (最近支持的内存大小)。 [详细了解服务器评估如何执行大小调整。](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>服务器评估建议的磁盘 SKU 的大小比本地分配的要大。 为什么要这样做呢？
服务器评估中的磁盘大小取决于两个评估属性-大小调整条件和存储类型。 如果大小调整条件为 "基于性能" 且存储类型设置为 "自动", 则会将磁盘的 IOPS 和吞吐量值视为标识目标磁盘类型 (标准 HDD、标准 SSD 或高级磁盘)。 然后, 建议考虑磁盘类型中的磁盘 SKU, 考虑本地磁盘的大小要求。 如果大小调整条件是 "基于性能" 且存储类型为 "高级", 则建议使用 Azure 中的高级磁盘 SKU, 根据本地磁盘的 IOPS、吞吐量和大小需求进行推荐。 当调整大小标准为 "本地" 时, 使用相同的逻辑调整大小, 存储类型为 "标准 HDD"、"标准 SSD" 或 "高级"。

例如, 如果你有一个包含 32 GB 内存的本地磁盘, 但该磁盘的聚合读取和写入 IOPS 为 800 IOPS, 则服务器评估将建议使用高级磁盘类型 (由于 IOPS 要求较高), 然后建议使用磁盘 SKU, 这可支持所需 IOPS 和大小。 在此示例中, 最接近的匹配为 P15 (256 GB, 1100 IOPS)。 即使本地磁盘所需的大小为 32 GB, 服务器评估也建议使用大小较大的磁盘, 因为本地磁盘的 IOPS 要求较高。

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>VM 的操作系统许可证成本是否包括在服务器评估估计的计算成本中？
服务器评估目前只考虑 Windows 计算机的 OS 许可证成本, 当前不会考虑 Linux 计算机的 OS 许可成本。 

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>性能历史记录和利用百分率对大小建议有什么影响？
这些属性仅适用于 "基于性能" 的大小调整。 服务器评估持续收集本地计算机的性能数据, 并使用它在 Azure 中建议 VM SKU 和磁盘 SKU。 下面是服务器评估收集性能数据的方式:
- Azure Migrate 设备连续对本地环境进行配置, 以便每隔20秒为 VMware Vm 收集实时利用率数据, 每隔30秒对 Hyper-v Vm 收集一次。
- 设备汇总了 20/30 秒的示例, 每10分钟创建一个数据点。 若要创建单一数据点, 设备将从所有 20/30 秒示例中选择峰值值, 并将其发送到 Azure。
- 当你在 "服务器评估" 中创建评估时, 根据 "性能持续时间" 和 "性能历史记录百分比" 值, 将识别出代表性使用率值。 例如, 如果性能历史记录为一周, 百分比利用率为 95, Azure Migrate 将按升序对最后一周的所有10分钟样本点进行排序, 然后选择 95% 作为代表值。
95% 的值可确保忽略任何离群值, 如果选择 99% 的百分点值, 则可能会包含这些离群值。 如果要选择该时间段的高峰使用量, 并且不想错过任何离群值, 则应选择 99% 百分点作为百分比利用率。

## <a name="dependency-visualization-issues"></a>依赖项可视化效果问题

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>找不到 Azure 政府项目的依赖项可视化效果功能。

Azure Migrate 取决于服务映射以获取依赖项可视化效果功能，并且由于服务映射当前在 Azure 政府中不可用，因此 Azure 政府中不提供此功能。

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>我本地的 VM 上安装了 Microsoft Monitoring Agent (MMA) 和依赖项代理，但依赖项现在显示在 Azure Migrate 门户中。

你安装这些代理后，Azure Migrate 通常需要 15-30 分钟才能显示门户中的依赖项。 如果等待的时间超过 30 分钟，请确保 MMA 代理能够按照以下步骤与 OMS 工作区进行通信：

对于 Windows VM：
1. 请在 **"控制面板"** 中, 启动**Microsoft Monitoring Agent**。
2. 在 MMA 属性弹出窗口中转到 " **Azure Log Analytics (OMS)** " 选项卡。
3. 确保工作区的  “状态”为绿色。
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
Azure Migrate 允许可视化依赖项最多持续一小时的时间。 尽管 Azure Migrate 允许返回到历史记录中的某一特定日期可以推至上个月，但可视化依赖项的最长持续时间最多为一小时。 例如，你可以使用依赖项映射中的持续时间功能来查看昨天的依赖项，但只能查看一小时。 但是, 可以使用 Azure Monitor 日志来查询更长时间[的依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>我无法可视化具有 10 个以上 VM 的组的依赖项？
你可以[可视化依赖项的组最多只能有 10 个 VM](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)，如果某个组的 VM 超过 10 个，建议先将该组拆分成较小的组，然后再可视化依赖项。

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>我已安装代理并使用依赖项可视化效果创建组。 现在，在执行故障转移后，计算机显示“安装代理”操作，而不是“查看依赖项”
* 发布计划或计划外故障转移，本地计算机将处于关闭状态，而等效计算机会在 Azure 中处于启动状态。 这些计算机将获得一个不同的 MAC 地址。 它们可能会根据用户是否选择保留本地 IP 地址而获得不同的 IP 地址。 如果 MAC 和 IP 地址不同，Azure Migrate 则不会将本地计算机与任何服务映射依赖项数据关联起来，同时会要求用户安装代理，而不是查看依赖项。
* 发布测试故障转移，本地计算机会按预期保持开启状态。 在 Azure 中启动的等效计算机将获取不同的 MAC 地址，并且可能会获取不同的 IP 地址。 除非用户阻止传出 Azure Monitor 记录来自这些计算机的流量, 否则 Azure Migrate 不会将本地计算机与任何服务映射依赖关系数据相关联, 并要求用户安装代理, 而不是查看依赖项。

## <a name="collect-azure-portal-logs"></a>收集 Azure 门户日志

**如何实现收集 Azure 门户网络流量日志？**

1. 打开浏览器并导航到门户, 并登录[到该门户](https://portal.azure.com)。
2. 按 F12 键启动“开发人员工具”。 如果需要，请清除设置“清除导航上的条目”  。
3. 单击“网络”  选项卡，然后开始捕获网络流量：
   - 在 Chrome 中，选择“保留日志”  。 记录应自动启动。 红色圆圈指示正在捕获流量。 如果未显示, 请单击黑色圆圈开始。
   - 在 Microsoft Edge/IE 中，记录应自动启动。 如果未启动，请单击绿色播放按钮。
4. 尝试再现该错误。
5. 在记录过程中遇到错误后，停止记录，并保存一份已记录的活动：
   - 在 Chrome 中，右键单击并选择“将内容另存为 HAR”  。 这会将日志压缩并导出为 har 文件。
   - 在 Microsoft Edge/IE 中，单击“导出捕获流量”图标  。 这会压缩并导出日志。
6. 导航到“控制台”  选项卡，以查看任何警告或错误。 保存控制台日志：
   - 在 Chrome 中，右键单击控制台日志中的任意位置。 选择 "**另存为**"、"导出" 和 "压缩日志"。
   - 在 Microsoft Edge/IE 中，右键单击错误并选择“复制所有”  。
7. 关闭“开发人员工具”。
