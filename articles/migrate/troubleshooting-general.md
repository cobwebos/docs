---
title: 排查 Azure Migrate 问题 | Microsoft 文档
description: 概述 Azure Migrate 服务中的已知问题, 以及常见错误的疑难解答提示。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 7fea6d16c8846909a8ce9bb33aae74ce343018fa
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142324"
---
# <a name="troubleshoot-azure-migrate"></a>排查 Azure Migrate 问题

[Azure Migrate](migrate-services-overview.md)提供了一个用于评估和迁移的工具中心, 以及第三方独立软件供应商 (ISV) 产品。 本文将帮助你对 Azure Migrate、Azure Migrate 服务器评估和 Azure Migrate 服务器迁移的错误进行故障排除。

## <a name="azure-migrate-project-issues"></a>Azure Migrate 项目问题

### <a name="i-cant-find-my-existing-azure-migrate-project"></a>找不到现有的 Azure Migrate 项目。

Azure Migrate 有[两个版本](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)。 根据你在其中创建项目的版本, 请使用以下方法之一:

* 如果正在查找使用 Azure Migrate 的早期版本 (旧经验) 创建的项目, 请按照以下步骤查找项目:

    1. 中转到[Azure 门户](https://portal.azure.com), 搜索**Azure Migrate**。
    2. 在 Azure Migrate "仪表板" 上, 你会看到一条表明访问较早项目的横幅。 仅当你已使用旧体验创建了项目时, 才会看到此横幅。 选择标题。

       ![访问现有项目](./media/troubleshooting-general/access-existing-projects.png)

    3. 现在, 你将看到 Azure Migrate 的以前版本创建的现有项目的列表。

* 如果正在查找使用当前版本 (新的体验) 创建的项目, 请按照以下步骤查找项目:

    1. 中转到[Azure 门户](https://portal.azure.com), 搜索**Azure Migrate**。
    2. 在 Azure Migrate 仪表板上, 转到左侧窗格中的 "**服务器**" 页, 然后选择右上角的 "**更改**"。

       ![切换到现有 Azure Migrate 项目](./media/troubleshooting-general/switch-project.png)

    3. 选择相应的订阅并 Azure Migrate 项目。

### <a name="how-do-i-create-a-second-azure-migrate-project"></a>如何实现创建另一个 Azure Migrate 项目？

按照以下步骤创建新 Azure Migrate 项目:

1. 中转到[Azure 门户](https://portal.azure.com), 搜索**Azure Migrate**。
2. 在 Azure Migrate 仪表板上, 转到左侧窗格中的 "**服务器**" 页, 然后选择右上角的 "**更改**"。

   ![更改 Azure Migrate 项目](./media/troubleshooting-general/switch-project.png)

3. 若要创建新项目, 请选择 **"单击此处"** 。

   ![创建第二个 Azure Migrate 项目](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-does-azure-migrate-support"></a>哪些 Azure 地理位置 Azure Migrate 支持？

请参阅适用于[VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)和[hyper-v](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)的列表。

### <a name="how-do-i-delete-azure-migrate-projects-and-associated-log-analytics-workspaces"></a>如何实现删除 Azure Migrate 项目和关联的 Log Analytics 工作区？

删除 Azure Migrate 项目时, 将删除有关发现的计算机的迁移项目*和*元数据。 但是, 如果已将 Log Analytics 工作区附加到服务器评估工具, 则不会自动删除 Log Analytics 工作区。 (同一 Log Analytics 工作区可用于多个用例。)如果还想要删除 Log Analytics 工作区, 则必须手动执行此操作:

1. 浏览到附加到该项目的 Log Analytics 工作区。
     * 如果尚未删除迁移项目, 可以从 "概要" 部分中的 "服务器评估概述" 页找到指向工作区的链接。

       ![LA 工作区](./media/troubleshooting-general/loganalytics-workspace.png)

     * 如果已删除迁移项目, 请在 Azure 门户的左窗格中选择 "**资源组**"。 转到创建该工作区的资源组, 然后浏览到该工作区。
2. 按照[使用 Azure 门户删除 Azure Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)中的说明进行操作。

### <a name="migration-project-creation-failed-with-a-requests-must-contain-user-identity-headers-error"></a>迁移项目创建失败, 出现 "请求必须包含用户标识标头" 错误。

对于无权访问组织的 Azure Active Directory (Azure AD) 租户的用户, 可能会发生此问题。 当用户首次添加到 Azure AD 租户时, 用户将收到电子邮件邀请以加入租户。 用户必须接受邀请才能成功添加到租户。 如果看不到此电子邮件, 请联系已经有权访问该租户的用户, 并通过使用[向来宾用户重新发送邀请](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)中指定的步骤, 请求他们重新发送邀请。

收到邀请电子邮件后, 必须打开电子邮件, 并选择接受邀请的链接。 然后, 你必须注销 Azure 门户并重新登录。 (刷新浏览器将不起作用。)然后, 你可以开始创建迁移项目。

## <a name="appliance-issues"></a>设备问题

### <a name="deployment-of-an-azure-migrate-appliance-for-vmware-failed-with-a-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry-error"></a>为 VMware 部署 Azure Migrate 设备失败, 出现 "提供的清单文件无效:OVF 清单条目 "错误无效。

1. 通过检查 Azure Migrate 的设备的哈希值, 验证是否已正确下载了设备 .OVA 文件。 有关指南, 请参阅[准备 VMware vm 以进行评估和迁移到 Azure](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)。 如果哈希值不匹配, 请重新下载 .OVA 文件, 然后重试部署。
2. 如果部署仍失败, 并且你正在使用 VMware vSphere 客户端部署 OVF 文件, 请尝试通过 vSphere web 客户端进行部署。 如果部署仍失败, 请尝试使用不同的 web 浏览器。
3. 如果使用的是 vSphere web 客户端, 并尝试将其部署在 vCenter Server 6.5 或6.7 上, 请执行以下步骤, 尝试直接在 ESXi 主机上部署 .OVA:
   - 使用 web 客户端 (https://<*主机 IP 地址*>/ui) 直接连接到 ESXi 主机, 而不是 vCenter Server。
   - 请参阅**家庭** > **清单**。
   - 选择 "**文件** > " "**部署 OVF 模板**"。 浏览到 .OVA 并完成部署。
4. 如果部署仍然失败, 请联系 Azure Migrate 支持部门。

### <a name="the-appliance-cant-connect-to-the-internet"></a>设备无法连接到 internet。

如果正在使用的计算机位于代理后面, 则会出现此行为。 如果代理需要, 请确保提供身份验证凭据。
如果使用任何基于 URL 的防火墙代理控制出站连接, 请确保将所需的 Url 添加到允许列表中:

- [针对 VMware 的服务器评估](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
- [适用于 Hyper-v 的服务器评估](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
- [适用于 VMware 的服务器迁移 (无代理)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
- [适用于 VMware 的服务器迁移 (基于代理)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
- [适用于 Hyper-v 的服务器迁移](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

如果你使用拦截代理连接到 internet, 则必须将代理证书导入到设备 VM。 可以按照[Azure Migrate 设备](https://docs.microsoft.com/azure/migrate/concepts-collector)中的步骤操作来导入代理证书。

### <a name="error-802-date-and-time-synchronization-error"></a>错误 802：日期和时间同步错误。

服务器时钟可能会与当前时间同步不到五分钟以上。 更改收集器 VM 上的时钟时间以匹配当前时间，如下所示：

1. 在 VM 上打开管理员命令提示符。
2. 若要检查时区, 请运行**w32tm/tz**。
3. 若要同步时间, 请运行**w32tm/resync**。


###  <a name="connection-failed-error-unabletoconnecttoserver"></a>连接失败。 错误：UnableToConnectToServer.

可能无法连接到 vCenter Server *Servername*. .com: 9443。 错误详细信息表示没有终结点在 https://*servername*: 9443/sdk 上侦听, 这可能接受消息。

在这种情况下, 请检查是否正在运行最新版本的收集器设备。 如果不是, 请将该设备升级到[最新版本](https://docs.microsoft.com/azure/migrate/concepts-collector)。

如果此问题仍然出现在最新版本中, 收集器计算机可能无法解析指定的 vCenter Server 名称, 或者指定的端口可能是错误的。 默认情况下, 如果未指定端口, 收集器会尝试连接到端口号443。

1. 从收集器计算机 Ping *Servername*.com。
2. 如果步骤1失败, 请尝试使用 IP 地址连接到 vCenter 服务器。
3. 确定可连接到 vCenter 的正确端口号。
4. 验证 vCenter 服务器是否已启动并正在运行。


### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>设备可能未成功注册到 Azure Migrate 项目 (错误 ID:60052)。

如果用于注册设备的 Azure 帐户没有足够的权限, 则会发生此错误。 请确保用于注册设备的 Azure 用户帐户至少具有订阅的参与者权限。 [详细了解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)所需的 Azure 角色和权限。

### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>设备可能未成功注册到 Azure Migrate 项目 (错误 ID:60039)。

找不到所选的用于注册设备的 Azure Migrate 项目。 在这种情况下, 注册将失败。 中转到 Azure 门户, 并检查资源组中是否存在该项目。 如果项目不存在, 请在资源组中创建新的 Azure Migrate 项目, 并再次注册设备。 [了解](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)有关创建新 Azure Migrate 项目的详细信息。

### <a name="an-azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Azure Key Vault 管理操作失败 (错误 ID:60030、60031)。

请确保用于注册设备的 Azure 用户帐户至少具有订阅的参与者权限。 此外, 请确保该帐户具有对错误消息中指定的密钥保管库的访问权限, 然后重试该操作。 如果问题持续出现，请联系 Microsoft 支持。 [详细了解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)所需的 Azure 角色和权限。

### <a name="discovery-couldnt-be-initiated-because-of-an-error-the-operation-failed-for-the-specified-list-of-hosts-or-clusters-error-id-60028"></a>由于出错, 无法启动发现。 对于指定的主机或群集列表, 操作失败 (错误 ID:60028)。

由于访问或检索 VM 信息时出现问题, 无法在错误中列出的主机上启动发现。 已成功添加其余的主机。 使用**add host**选项再次添加错误中列出的主机。 如果出现验证错误, 请查看更正指南以修复错误, 然后再次尝试 "**保存并启动发现**" 选项。

### <a name="an-azure-ad-operation-failed-the-error-occurred-while-creating-or-updating-the-azure-ad-application-error-id-60025"></a>Azure AD 操作失败。 创建或更新 Azure AD 应用程序时出错 (错误 ID:60025)。

用于启动发现的 Azure 用户帐户与用于注册设备的帐户不同时，会遇到此错误。 可以执行以下操作之一：
1. 确保启动发现的用户帐户与用于注册设备的用户帐户相同。
1. 为发现操作失败的其他用户帐户提供 AAD 应用程序访问权限。
1. 删除先前为 Azure Migrate 项目创建的资源组, 并创建另一个资源组以重新开始。

[了解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)有关 AAD 应用程序权限的详细信息。

## <a name="discovery-issues"></a>发现问题

### <a name="i-started-discovery-but-dont-see-the-discovered-vms-on-the-azure-portal-the-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>我已经开始发现, 但在 Azure 门户看不到发现的虚拟机。 **服务器评估**和**服务器迁移**磁贴显示状态 "正在发现"。
从设备启动发现后, 请等待一段时间让发现的计算机显示在 Azure 门户上。 VMware 发现大约要花费15分钟, 对于 Hyper-v 发现, 每个添加的主机约2分钟。 如果在这些等待时间之后仍看到 "正在进行的发现", 请在 "**服务器**" 选项卡上选择 "**刷新**"。这会在 "服务器评估" 和 "服务器迁移" 磁贴中显示发现的服务器的计数。


### <a name="im-using-an-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-shown-in-the-portal"></a>我使用的是持续发现本地环境的设备, 但在我的本地环境中删除的 Vm 仍会显示在门户中。

设备收集的发现数据需要长达30分钟的时间才能在门户中反映出来。 如果在30分钟后仍未看到最新信息, 请按照以下步骤刷新数据:

1. 前往服务器**Azure Migrate 服务器评估**", 然后选择"**概述**"。 > 
2. 在 "**管理**" 下, 选择**代理运行状况**
3. 选择 "**刷新代理**"。 你将在代理列表下看到此选项。
4. 等待刷新操作完成。 现在, 你应会看到 Vm 上的最新信息。

### <a name="i-dont-see-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>我在门户上看不到本地 Vm 上的最新信息。

设备收集的发现数据需要长达30分钟的时间才会反映在门户上。 如果在30分钟后仍未看到最新信息, 请按照以下步骤刷新数据:

1. 前往服务器**Azure Migrate 服务器评估**", 然后选择"**概述**"。 > 
2. 在 "**管理**" 下, 选择**代理运行状况**
3. 选择 "**刷新代理**"。 你将在代理列表下看到此选项。
4. 等待刷新操作完成。 现在, 你应会看到 Vm 上的最新信息。

### <a name="cant-connect-to-a-host-or-cluster-because-the-server-name-cant-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>无法连接到主机或群集, 因为无法解析服务器名称。 WinRM 错误代码:0x803381B9 (错误 ID:50004)。
如果设备的 Azure DNS 服务无法解析提供的群集或主机名, 则会发生此错误。 如果在群集上看到此错误, 请尝试提供群集的完全限定的域名 (FQDN)。

对于群集中的主机, 你可能还会看到此错误。 在这种情况下, 设备可以连接到群集, 但群集会返回不是 Fqdn 的主机名。

若要解决此错误, 请通过添加 IP 地址和主机名的映射来更新设备上的 hosts 文件:
1. 以管理员身份打开记事本。 然后打开 C:\Windows\System32\Drivers\etc\hosts 文件。
2. 在行中添加 IP 地址和主机名。 对于出现此错误的每个主机或群集重复此操作。
3. 保存并关闭 hosts 文件。
4. 你可以使用 "设备管理" 应用来检查设备是否可以连接到主机。 30分钟后, 你应该能够在 Azure 门户上看到这些主机的最新信息。


## <a name="assessment-issues"></a>评估问题

### <a name="azure-readiness-issues"></a>Azure 就绪问题

问题 | 更正
--- | ---
启动类型不受支持 | Azure 不支持具有 EFI 启动类型的虚拟机。 在运行迁移之前, 建议将启动类型转换为 BIOS。 <br/><br/>你可以使用 Azure Migrate 服务器迁移来处理此类 Vm 的迁移。 在迁移过程中, 它会将 VM 的启动类型转换为 BIOS。
有条件支持的 Windows OS | 操作系统已超过其支持的截止日期, 需要自定义支持协议 (CSA) 以[支持 Azure 中的支持](https://aka.ms/WSosstatement)。 请考虑在迁移到 Azure 之前升级 OS。
不支持的 Windows OS | Azure 仅支持[所选的 WINDOWS 操作系统版本](https://aka.ms/WSosstatement)。 请考虑在迁移到 Azure 之前升级计算机的操作系统。
有条件认可的 Linux OS | Azure 予以认可仅[选择 LINUX 操作系统版本](../virtual-machines/linux/endorsed-distros.md)。 请考虑在迁移到 Azure 之前升级计算机的操作系统。
不认可的 Linux OS | 计算机可能会在 Azure 中启动, 但 Azure 不提供 OS 支持。 在迁移到 Azure 之前, 请考虑将 OS 升级到[认可的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)。
未知操作系统 | 在 vCenter Server 中, 将 VM 的操作系统指定为 "其他"。 此行为会阻止 Azure Migrate 验证 VM 的 Azure 准备情况。 在迁移计算机之前, 请确保 Azure[支持](https://aka.ms/azureoslist)计算机的操作系统。
操作系统位数不受支持 | 具有32位操作系统的 Vm 可能会在 Azure 中启动, 但建议在迁移到 Azure 之前, 将 VM 的 OS 升级到64位。
需要 Microsoft Visual Studio 订阅 | 计算机正在运行 Windows 客户端操作系统, 此操作系统仅支持通过 Visual Studio 订阅。
找不到所需存储性能的 VM | 计算机所需的存储性能 (每秒输入/输出操作数 [IOPS] 和吞吐量) 超过了 Azure VM 支持。 在迁移之前，减少计算机的存储需求。
找不到所需网络性能的 VM | 计算机所需的网络性能（输入/输出）超出了 Azure VM 支持。 减少计算机的网络要求。
在指定位置找不到 VM | 在迁移之前使用不同目标位置。
一个或多个不适用的磁盘 | 附加到 VM 的一个或多个磁盘不满足 Azure 要求。 “Azure Migrate:服务器评估目前不支持超级 SSD 磁盘, 并根据高级托管磁盘 (32 TB) 的磁盘限制来评估磁盘。  对于附加到 VM 的每个磁盘, 请确保磁盘大小 < 64 TB (超级 SSD 磁盘支持), 如果没有, 请在迁移到 Azure 之前减小磁盘大小, 或者在 Azure 中使用多个磁盘并将[它们放在一起](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping), 以获得更高的存储限制。 确保 Azure[托管的虚拟机磁盘](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)支持每个磁盘所需的性能 (IOPS 和吞吐量)。
存在一个或多个不合适网络适配器。 | 在迁移之前从计算机中删除未使用的网络适配器。
磁盘计数超出限制 | 在迁移之前从计算机中删除未使用的磁盘。
磁盘大小超出限制 | “Azure Migrate:服务器评估目前不支持超级 SSD 磁盘, 并基于高级磁盘限制 (32 TB) 评估磁盘。 但是, Azure 支持最大为 64 TB 大小的磁盘 (超级 SSD 磁盘支持)。 在迁移之前将磁盘缩减为小于 64 TB, 或在 Azure 中使用多个磁盘并将它们放在[一起](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping), 以获得更高的存储限制。
指定位置中磁盘不可用 | 在迁移之前请确保磁盘已在目标位置。
不可用于指定冗余的磁盘 | 磁盘应使用在评估设置中定义的冗余存储类型（默认为 LRS）。
由于内部错误, 无法确定磁盘的适用性 | 请尝试为组创建一个新评估。
未找到具有所需内核和内存的 VM | Azure 找不到合适的 VM 类型。 在迁移之前请减少本地计算机的内存和内核数。
由于内部错误, 无法确定 VM 适用性 | 请尝试为组创建一个新评估。
由于出现内部错误, 无法确定一个或多个磁盘的适用性 | 请尝试为组创建一个新评估。
由于出现内部错误, 无法确定一个或多个网络适配器的适用性 | 请尝试为组创建一个新评估。

### <a name="i-cant-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>我无法在评估属性中将企业协议 (EA) 指定为 Azure 产品/服务。
Azure Migrate Server 评估目前不支持基于企业协议 (EA) 的定价。 若要解决此限制, 请使用即用即**付**作为 Azure 产品/服务, 并使用 "**折扣**" 属性指定收到的任何自定义折扣。 [了解有关如何自定义评估的详细信息](https://aka.ms/migrate/selfhelp/eapricing)。

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready"></a>为什么服务器评估将我的 Linux Vm 标记为 "有条件就绪"？
服务器评估中存在一个已知的间隔, 可防止它检测本地 Vm 上安装的 Linux 操作系统的次版本 (例如, 对于 RHEL 6.10, 当前服务器评估只检测到 RHEL 6 作为操作系统版本)。 由于 Azure 予以认可仅特定版本的 Linux, 因此 Linux Vm 当前在服务器评估中被标记为有条件准备就绪。 可以通过查看[Azure Linux 支持文档](https://aka.ms/migrate/selfhost/azureendorseddistros)来确定本地 VM 上运行的 Linux 操作系统是否已在 azure 中认可。 验证了认可的分发后, 可以忽略此警告。

### <a name="why-does-the-vm-sku-recommended-by-server-assessment-have-more-cores-and-more-memory-than-whats-allocated-on-premises"></a>为什么服务器评估建议使用的 VM SKU 比本地分配的内核和内存更多？
服务器评估中的 VM SKU 建议取决于评估属性。 您可以在服务器评估中创建两种评估:*基于性能*和*在本地*。 对于基于性能的评估, 服务器评估会考虑本地 Vm 的使用率数据 (CPU、内存、磁盘和网络使用率), 以确定本地 Vm 的正确目标 VM SKU。 此外, 对于基于性能的大小调整, 确定有效利用率时, 应考虑舒适因素。 对于本地大小调整, 不考虑性能数据, 建议根据本地分配的内容使用目标 SKU。

例如, 假设有一个具有4个核心和 8 GB 内存的本地 VM, 其 CPU 使用率为 50%, 内存使用率为 50%, 在评估中指定1.3 了。 如果评估的大小调整条件为 **"本地**", 则建议使用具有4个内核的 AZURE VM SKU 和8千兆字节 (GB) 的内存。

但是, 假设大小调整条件是基于性能的。 根据有效的 CPU 和内存利用率 (4 个内核的 50% * 1.3 = 2.6 个核心, 8 GB 内存的 50% * 1.3 = 5.3-GB 内存), 建议使用四个核心 (最接近支持的核心计数) 和 8 GB 内存的最便宜的 VM SKU (最接近支持的内存大小)。 [了解有关服务器评估如何执行大小调整的详细信息。](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)。

### <a name="why-is-the-disk-sku-recommended-by-server-assessment-bigger-than-whats-allocated-on-premises"></a>为什么服务器评估建议的磁盘 SKU 比本地分配的要大？
服务器评估中的磁盘大小调整取决于两个评估属性: 大小调整条件和存储类型。 如果大小调整条件**基于性能**并且存储类型设置为 "**自动**", 则会将磁盘的 IOPS 和吞吐量值视为标识目标磁盘类型 (标准 HDD、标准 SSD 或高级磁盘)。 然后建议使用磁盘类型的磁盘 SKU, 此建议考虑本地磁盘的大小要求。 如果大小调整条件**基于性能**并且存储类型为 "**高级**", 则建议使用 Azure 中的高级磁盘 SKU, 根据本地磁盘的 IOPS、吞吐量和大小需求进行推荐。 当大小调整条件为**本地**且存储类型为**标准 HDD**、**标准 SSD**或**高级**时, 将使用相同的逻辑来执行磁盘大小调整。

例如, 如果你有一个具有 32 GB 内存的本地磁盘, 但该磁盘的聚合读取和写入 IOPS 为 800 IOPS, 则服务器评估将建议使用高级磁盘类型 (因为需要更高的 IOPS 要求), 然后建议一个可支持的磁盘 SKU所需的 IOPS 和大小。 本示例中最接近的匹配项将是 P15（256 GB，1100 IOPS）。 尽管本地磁盘所需的大小为 32 GB, 服务器评估建议使用较大的磁盘, 因为本地磁盘的 IOPS 要求较高。

### <a name="why-does-my-assessment-report-indicate-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>为什么我的评估报表向某些 Vm 显示 "PercentageOfCoresUtilizedMissing" 或 "PercentageOfMemoryUtilizedMissing"？
如果 Azure Migrate 设备无法收集本地 Vm 的性能数据, 则会报告这些问题。 如果在创建评估 (最后一天/每周/每月) 时关闭了 Vm, 则可能会发生此行为。 当 VM 关闭时, 它无法收集 VM 的性能数据。 如果仅缺少内存计数器, 而你要尝试评估 Hyper-v Vm, 请检查是否已在这些 Vm 上启用动态内存。 存在一个已知问题, 即 Azure Migrate 设备无法为未启用动态内存的 Vm 收集内存使用率数据。 此问题只会影响 Hyper-v Vm, 而不会影响 VMware Vm。 如果缺少任何性能计数器, Azure Migrate 服务器评估将回退到分配的内核和内存, 并建议相应的 VM 大小。

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>VM 的操作系统许可证成本是否包括在服务器评估估计的计算成本中？
服务器评估目前只考虑 Windows 计算机的操作系统许可成本。 目前尚不考虑 Linux 计算机的 OS 许可成本。

### <a name="what-impact-do-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>性能历史记录和百分比利用率对大小建议有什么影响？
这些属性仅适用于基于性能的大小调整。 服务器评估不断收集本地计算机的性能数据，并使用它来针对 Azure 中的 VM SKU 和磁盘 SKU 提出建议。 服务器评估将收集此性能数据, 如下所示:
- Azure Migrate 设备连续对本地环境进行配置, 以便每隔20秒为 VMware Vm 收集实时利用率数据, 每隔30秒对 Hyper-v Vm 收集一次。
- 该设备汇总了20秒和30秒的样本, 每10分钟创建一个数据点。 若要创建单一数据点, 设备将从所有20秒和30秒的示例中选择峰值值, 然后将其发送到 Azure。
- 在服务器评估中创建评估时，根据性能持续时间和性能历史记录百分位值，确定代表性利用率值。 例如, 如果性能历史记录为一周, 百分比利用率为 95, 则 Azure Migrate 按升序排序最后一周的所有10分钟示例点, 然后选择第95个百分点作为代表值。
95% 的值可以确保忽略任何离群值, 如果选择 99% 的百分点值, 则可能会包含这些离群值。 如果要选择该时间段的高峰使用量, 而不想错过任何离群值, 则应选择 99% 百分位以实现百分比利用率。

## <a name="dependency-visualization-issues"></a>依赖项可视化效果问题

### <a name="i-cant-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>找不到 Azure 政府项目的依赖项可视化功能。

Azure Migrate 依赖于依赖项可视化功能服务映射。 由于服务映射当前在 Azure 政府版中不可用, 因此此功能在 Azure 政府版中不可用。

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>我本地的 VM 上安装了 Microsoft Monitoring Agent (MMA) 和依赖项代理，但依赖项现在显示在 Azure Migrate 门户中。

安装代理后, Azure Migrate 通常需要15-30 分钟才能在门户中显示依赖项。 如果等待超过30分钟, 请按照以下步骤操作, 确保 MMA 可以与 OMS 工作区通信。

对于 Windows VM：
1. 请在 "控制面板" 中, 开始 Microsoft Monitoring Agent。
2. 在 " **Microsoft Monitoring Agent 属性**" 对话框中的 " **AZURE Log Analytics (OMS)** " 选项卡上, 确保工作区的**状态**为绿色。
3. 如果状态不是绿色，请尝试删除工作区，然后再次将其添加到 MMA。

      !["MMA 属性" 对话框](./media/troubleshooting-general/mma-status.png)

对于 Linux VM, 请确保 MMA 和依赖项代理的安装命令已成功。

### <a name="what-operating-systems-does-mma-support"></a>MMA 支持哪些操作系统？

 [下面是 MMA 支持的 Windows 操作系统的列表](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)。
[下面是 MMA 支持的 Linux 操作系统的列表](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)。

### <a name="what-operating-systems-does-the-dependency-agent-support"></a>依赖关系代理支持哪些操作系统？

[下面是[用于 VM 的 Azure Monitor 支持的 Windows 和 Linux 操作系统](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)的列表。

### <a name="i-cant-visualize-dependencies-in-azure-migrate-for-more-than-a-one-hour-duration"></a>我无法将 Azure Migrate 中的依赖项可视化到超过1小时的持续时间。
在 Azure Migrate 中, 最多可将依赖项可视化到一小时的时间。 尽管 Azure Migrate 允许您返回到上个月的特定日期, 但您可以对依赖项进行可视化的最长持续时间为一小时。

例如, 你可以使用 "依赖关系映射" 中的 "持续时间" 功能来查看昨天的依赖项, 但你可以仅查看一小时的时间段。 但是, 可以使用 Azure Monitor 日志来查询更长时间[的依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

### <a name="i-cant-visualize-dependencies-for-groups-that-have-more-than-10-vms"></a>我无法直观显示具有10个以上 Vm 的组的依赖项。
可以可视化最多为10个 Vm 的[组的依赖项](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies), 如果有10个以上的 vm, 则建议将该组拆分为较小的组, 并将依赖关系可视化。

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>我已安装代理并使用依赖项可视化效果创建组。 现在, 故障转移后, 计算机会显示 "安装代理" 操作, 而不是 "查看依赖关系"。
* 在进行计划内或非计划的故障转移后, 本地计算机将关闭, 并在 Azure 中启动等效的计算机。 这些计算机将获得一个不同的 MAC 地址。 根据用户是否选择保留本地 IP 地址, 它们可能会获取不同的 IP 地址。

  如果 MAC 和 IP 地址不同, Azure Migrate 不会将本地计算机与任何服务映射的依赖关系数据相关联。 相反, 它会要求用户安装代理, 而不是查看依赖项。
* 后期测试故障转移后, 本地计算机仍按预期方式开启。 在 Azure 中启动的等效计算机获取不同的 MAC 地址, 并可能获取不同的 IP 地址。 除非用户阻止从这些计算机传出 Azure Monitor 日志流量, 否则 Azure Migrate 不会将本地计算机与任何服务映射依赖关系数据相关联, 并要求用户安装代理, 而不是查看依赖项。

## <a name="collect-azure-portal-logs"></a>收集 Azure 门户日志

**如何实现收集 Azure 门户网络流量日志？**

1. 打开浏览器, 中转[到门户](https://portal.azure.com), 然后登录。
2. 按 F12 开始开发人员工具。 如果需要, 请清除 "**在导航上清除条目**" 设置。
3. 选择 "**网络**" 选项卡, 开始捕获网络流量:
   - 在 Chrome 中，选择“保留日志”。 记录应自动启动。 红色圆圈表示正在捕获流量。 如果未显示红色圆圈, 请选择要开始的黑色圆圈。
   - 在 Microsoft Edge 和 Internet Explorer 中, 记录应自动启动。 如果没有, 请选择绿色的 "播放" 按钮。
4. 尝试再现该错误。
5. 在记录过程中遇到错误后，停止记录，并保存一份已记录的活动：
   - 在 Chrome 中, 右键单击并选择 "**另存为 HAR 内容**"。 此操作将日志压缩并导出为 har 文件。
   - 在 Microsoft Edge 或 Internet Explorer 中, 选择 "**导出捕获的流量**" 选项。 此操作将压缩并导出日志。
6. 选择 "**控制台**" 选项卡以检查是否有任何警告或错误。 保存控制台日志：
   - 在 Chrome 中，右键单击控制台日志中的任意位置。 选择 "**另存为**"、"导出" 和 "压缩日志"。
   - 在 Microsoft Edge 或 Internet Explorer 中, 右键单击错误并选择 "**全部复制**"。
7. 关闭“开发人员工具”。
