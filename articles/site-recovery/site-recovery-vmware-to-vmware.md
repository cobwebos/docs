---
title: "将 VMware VM 或物理服务器的灾难恢复设置到辅助站点 | Microsoft 文档"
description: "本文介绍了如何使用 Azure Site Recovery 服务将本地 VMware VM 或 Windows/Linux 物理服务器复制到辅助站点。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: b2cba944-d3b4-473c-8d97-9945c7eabf63
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2017
ms.author: raynew
ms.openlocfilehash: 8cfaa56735c1f4e2e01b58fdde2ad0e77b388762
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-disaster-recovery-of-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>将 VMware 虚拟机或物理服务器的灾难恢复设置到辅助站点


Azure Site Recovery 中的 InMage Scout 在本地 VMware 站点之间提供实时复制。 InMage Scout 随附在 Azure Site Recovery 服务订阅中。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="create-a-vault"></a>创建保管库
1. 登录到 [Azure 门户](https://portal.azure.com/) > **恢复服务**。
2. 单击“新建”>“管理”>“备份和 Site Recovery (OMS)”。 或者，可以单击“浏览”>“恢复服务保管库”>“添加”。
3. 在“**名称**”中，指定一个友好名称以标识该保管库。 如果有多个订阅，请选择其中一个。
4. 在“**资源组**”中，创建新资源组或选择现有的资源组。 指定 Azure 区域，以完成必填字段。
5. 在“**位置**”中，选择保管库的地理区域。 若要查看支持的区域，请参阅 [Azure Site Recovery 定价](https://azure.microsoft.com/pricing/details/site-recovery/)。
6. 要从仪表板快速访问保管库，请单击“固定到仪表板”，并单击“创建”。
7. 新保管库将显示在“仪表板”>“所有资源”中，以及“恢复服务保管库”主页上。

## <a name="configure-the-vault-and-download-inmage-scout-components"></a>配置保管库并下载 InMage Scout 组件
1. 在“恢复服务保管库”页中选择保管库，并单击“设置”。
2. 在“**设置**” > “**快速启动**”中，单击“**Site Recovery**”>“**步骤 1: 准备基础结构**” > “**保护目标**”。
3. 在“**保护目标**”中选择“到恢复站点”，并选择“是，使用 VMware vSphere 虚拟机监控程序”。 然后，单击“确定”。
4. 在 **Scout 安装**中，单击“下载”以下载 InMage Scout 8.0.1 GA 软件和注册密钥。 所有必需组件的安装程序文件都包含在下载的 .zip 文件中。

## <a name="step-3-install-component-updates"></a>步骤 3：安装组件更新
阅读有关最新[更新](#updates)的信息。 按以下顺序在服务器上安装更新文件：

1. RX 服务器（如果相关）
2. 配置服务器
3. 进程服务器
4. 主目标服务器
5. vContinuum 服务器
6. 源服务器（Windows 和 Linux 服务器）

按以下步骤安装更新：

1. 下载[更新](https://aka.ms/asr-scout-update5)后的 .zip 文件。 此 .zip 文件包含以下文件：

   * RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
   * CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
   * UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
   * UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
   * vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe
   * 对于 RHEL5、OL5、OL6、SUSE 10、SUSE 11 的 UA update4 位：UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
2. 解压缩 .zip 文件。<br>
3. **对于 RX 服务器**：将 **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** 复制到 RX 服务器并将其提取。 在解压缩的文件夹中运行 **/Install**。<br>
4. **对于配置服务器/进程服务器**：将 **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** 复制到配置服务器和进程服务器。 双击以运行该文件。<br>
5. **对于 Windows 主目标服务器**：要更新统一代理，将 **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** 复制到主目标服务器。 双击以运行该文件。 请注意，如果源未更新到 Update4，则统一代理也适用于源服务器。 还应将其安装在源服务器上，如此列表后面部分所述。<br>
6. **对于 vContinuum 服务器**：将 **vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe** 复制到 vContinuum 服务器。  确保已关闭 vContinuum 向导。 双击以运行该文件。<br>
7. **对于 Linux 主目标服务器**：要更新统一代理，将 **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** 复制到主目标服务器并将其提取。 在解压缩的文件夹中运行 **/Install**。<br>
8. 对于 Windows 源服务器：如果源服务器已在运行 Update 4，则无需在该服务器上安装 Update 5 代理。 如果其当前运行的版本低于 Update 4，请应用 Update 5 代理。
要更新统一代理，请将 **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** 复制到源服务器。 双击以运行该文件。 <br>
9. **对于 Linux 源服务器**：要更新统一代理，将相应版本的 UA 文件复制到 Linux 服务器并进行文件提取。 在解压缩的文件夹中运行 **/Install**。  示例：对于 RHEL 6.7 64 位服务器，将 **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** 复制到服务器并进行文件提取。 在解压缩的文件夹中运行 **/Install**。

## <a name="step-4-set-up-replication"></a>步骤 4：设置复制
1. 设置源与目标 VMware 站点之间的复制。
2. 有关指导，请使用随产品下载的 InMage Scout 文档。 也可以访问以下文档：

   * [发行说明](https://aka.ms/asr-scout-release-notes)
   * [兼容性对照表](https://aka.ms/asr-scout-cm)
   * [用户指南](https://aka.ms/asr-scout-user-guide)
   * [RX 用户指南](https://aka.ms/asr-scout-rx-user-guide)
   * [快速安装指南](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>更新
### <a name="azure-site-recovery-scout-801-update-5"></a>Azure Site Recovery Scout 8.0.1 Update 5
Scout Update 5 是累积更新。 它包含从 Update 1 到 Update 4 的所有修复，以及之后的 bug 修复和增强功能。
从 Site Recovery Scout Update4 到 Update5 期间添加的修复均特定于主目标和 vContinuum 组件。 如果所有源服务器、主目标、配置服务器、进程服务器和 RX 都已处于 Site Recovery Scout Update4，则只需要在主目标服务器上应用 Update 5。 

**新的平台支持**
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)

> [!NOTE]
> SLES 11 SP4 64 位 **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** 与基础 Scout GA 包 **InMage_Scout_Standard_8.0.1 GA.zip** 打包在一起。 从门户下载 Scout GA 程序包，如[步骤 1](#step-1-create-a-vault) 中所述。
>

**Bug 修复和增强功能**

* 提高了 Windows 群集支持可靠性
    * 已修复 - 有时候某些 P2V MSCS 群集磁盘在恢复后成为 RAW
    * 已修复 - P2V MSCS 群集恢复由于磁盘顺序不匹配而失败
    * 已修复 - MSCS 群集添加磁盘操作由于磁盘大小不匹配而失败
    * 已修复 - 包含 RDM LUN 的源 MSCS 群集的映射就绪状态检查在验证大小时失败
    * 已修复 - 单节点群集保护由于 SCSI 不匹配问题而失败 
    * 已修复 - 当存在目标群集磁盘时，P2V Windows 群集服务器的重新保护失败。 
    
* 在故障回复保护期间，如果所选的 MT 不与受保护源计算机的 MT（在正向保护期间）位于同一 ESXi 服务器上，则 vContinuum 在故障回复恢复期间会选取错误的 MT，并且随后的恢复操作会失败。

> [!NOTE]
> 
> * 上述 P2V 群集修复仅适用于以 Site Recovery Scout Update5 全新保护的那些物理 MSCS 群集。 要在具有较旧更新的已受保护的 P2V MSCS 群集上利用这些群集修复，需要遵循[发行说明](https://aka.ms/asr-scout-release-notes)中第 12 节“将受保护的 P2V MSCS 群集升级到 Scout Update 5”中提到的升级步骤。
> 
> * 只有满足下述条件时，物理 MSCS 群集的重新保护才能重新使用现有目标磁盘：在进行重新保护时，在每个群集节点上处于活动状态的磁盘集与最初保护这些群集节点时在其上处于活动状态的磁盘集相同。 如果不是，则需执行[发行说明](https://aka.ms/asr-scout-release-notes)的第 12 节中提到的手动步骤将目标端磁盘移动到正确的数据存储路径，以便在重新保护期间重新使用它们。 如果在不执行那些升级步骤的情况下重新保护处于 P2V 模式的 MSCS 群集，则它会在目标 ESXi 服务器上创建新磁盘。 需要手动从数据存储中删除旧磁盘。
> 
> * 每当具有任何 Service Pack 服务器的源 SLES11 或 SLES11 正常重新启动时，都应当手动标记**根**磁盘复制对以便重新同步，因为 CX UI 中不会提供通知。 如果没有标记根磁盘以便重新同步，则可能会看到数据完整性 (DI) 问题。
> 

### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 是累积更新。 它包含从 Update 1 到 Update 3 的所有修复，以及之后的 bug 修复和增强功能。

**新的平台支持**

* 已添加对 vCenter/vSphere 6.0、6.1 和 6.2 的支持
* 已添加对以下 Linux 操作系统的支持
  * Red Hat Enterprise Linux (RHEL) 7.0、7.1 和 7.2
  * CentOS 7.0、7.1 和 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 位 **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** 与基础 Scout GA 程序包 **InMage_Scout_Standard_8.0.1 GA.zip** 一起打包。 从门户下载 Scout GA 程序包，如[步骤 1](#step-1-create-a-vault) 中所述。
>
>

**Bug 修复和增强功能**

* 改进了以下 Linux OS 和克隆的关闭处理方式，以防止意外的重新同步问题。
  * Red Hat Enterprise Linux (RHEL) 6.x
  * Oracle Linux (OL) 6.x
* 对于 Linux，统一代理安装目录中的完整文件夹访问权限现在仅限为本地用户。
* 在 Windows 上，在重负载分布式应用程序（例如 SQL 和 Share Point 群集）上发出通用分布式一致性书签时会出现的超时问题。
* 在 CX 基础安装程序中添加了与日志相关的修复。
* 将 VMware vCLI 6.0 下载链接添加到了 Windows 主目标基础安装程序。
* 在故障转移和 DR 钻取期间针对网络配置更改添加了更多的检查和日志。
* 修复了有时不向 CX 报告保留期信息的问题。  
* 对于物理群集，修复了当发生源卷收缩时，通过 vContinuum 向导进行卷调整操作会失败的问题。
* 修复了当群集磁盘为 PRDM 磁盘时，带有“未能找到磁盘签名”错误提示的群集保护失败问题。
* 修复了 cxps 传输服务器由于超出异常范围而崩溃的问题。
* 在 vContinuum 向导的推送安装页面中，现在可对服务器名称列和 IP 列的大小进行调整了。
* RX API 的增强功能
  * 提供了五个最新可用的通用一致性点（仅适用于“保证”标记）。
  * 提供了有关所有受保护设备的容量及可用空间的详细信息。
  * 提供了源服务器上的 Scout 驱动程序状态。

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** 基础数据包现在包含更新的 CX 基础安装程序 **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** 和 Windows 主目标基础安装程序 **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**。 对于新安装，使用新 CX 和 Windows 主目标 GA 位。
> * Update 4 可直接应用于 8.0.1 GA。
> * 在系统上应用配置服务器和 RX 更新后，便不能将其回滚。
>
>

### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3
Update 3 包含以下 bug 修复和增强功能：

* 配置服务器和 RX 位于代理后面时无法向 Site Recovery 保管库注册。
* 恢复点目标 (RPO) 未满足的小时数不会在运行状况报告中更新。
* 当 ESX 硬件详细信息或网络详细信息包含任何 UTF-8 字符时，配置服务器不会与 RX 同步。
* Windows Server 2008 R2 域控制器无法在恢复之后启动。
* 脱机同步不按预期进行。
* 虚拟机 (VM) 故障转移后，CX UI 中的复制对删除会长时间停滞，用户将不能完成故障回复或恢复操作。
* 由一致性作业执行的整体快照操作已经过优化，可帮助减少应用程序（例如 SQL 客户端）断开连接的问题。
* 通过减少在 Windows 上创建快照所需的内存使用量，改善了一致性工具 (VACP.exe) 的性能。
* 密码大于 16 个字符时，推送安装服务会崩溃。
* vContinuum 不会在凭据更改时检查和提示输入新的 vCenter 凭据。
* 在 Linux 上，主目标缓存管理器 (cachemgr) 不会从进程服务器下载文件，从而发生复制对限制。
* 当所有节点上的物理故障转移群集 (MSCS) 磁盘顺序不一致时，不会设置某些群集卷的复制。
  <br/>请注意，群集需要重新保护才能利用这一修复。  
* 在 RX 从 Scout 7.1 升级到 Scout 8.0.1 之后，SMTP 功能不按预期工作。
* 已在日志中添加更多统计信息，以便回滚操作跟踪完成此操作所需的时间。
* 已添加对源服务器上 Linux 操作系统的支持：
  * Red Hat Enterprise Linux (RHEL) 6 update 7
  * CentOS 6 Update 7
* CX 和 RX UI 现在可以针对进入位图模式的对显示通知。
* 已将以下安全修复程序添加到 RX 中：

| **问题说明** | **实现过程** |
| --- | --- |
| 通过参数篡改绕过授权 |已将访问权限限制为不适用的用户。 |
| 跨站点请求伪造 |实施针对每页随机生成的页面令牌概念。 <br/>通过此过程，会看到： <li> 同一用户仅有单个登录实例。</li><li>页面刷新不起作用--此操作会重定向至仪表板。</li> |
| 恶意文件上传 |将文件限制为特定的扩展名。 允许的扩展名：7z、aiff、asf、avi、bmp、csv、doc、docx、fla、flv、gif、gz、gzip、jpeg、jpg、log、mid、mov、mp3、mp4、mpc、mpeg、mpg、ods、odt、pdf、png、ppt、pptx、pxd、qt、ram、rar、rm、rmi、rmvb、rtf、sdc、sitd、swf、sxc、sxw、tar、tgz、tif、tiff、txt、vsd、wav、wma、wmv、xls、xlsx、xml 和 zip。 |
| 持久性跨站点脚本 |已添加输入验证。 |

> [!NOTE]
> * 所有 Site Recovery 更新都是累积更新。 Update3 包含 Update1 和 Update2 的所有修复程序。 Update 3 可以直接应用于 8.0.1 GA。
> * 在系统上应用配置服务器和 RX 更新后，便不能将其回滚。
>
>

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (Update 03Dec15)
Update 2 中的修复包括：

* **配置服务器**：修复了在 Site Recovery 中注册配置服务器时妨碍 31 天免费计量功能正常使用的问题。
* **统一代理**：修复了 Update 1 中的问题，该问题导致从版本 8.0 升级到 8.0.1 时，更新无法安装在主目标服务器上。

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Update 1 包含以下 bug 修复和新功能：

* 每个服务器实例享有 31 天的免费保护。 这样，便可以测试功能或建立概念认证。
  * 从使用 Site Recovery Scout 首次保护服务器的时间开始计算的前 31 天，服务器上的所有操作（包括故障转移和故障回复）都是免费的。
  * 从第 32 天起，会根据标准实例费率，针对每个受保护的服务器，向客户拥有的站点收取 Azure Site Recovery 保护费用。
  * 在 Azure Site Recovery 保管库的“仪表板”页上随时会显示当前计费的受保护服务器数目。
* 添加了对 vSphere命令行接口 (vCLI) 5.5 Update 2 的支持。
* 已添加对源服务器上 Linux 操作系统的支持：
  * RHEL 6 Update 6
  * RHEL 5 Update 11
  * CentOS 6 Update 6
  * CentOS 5 Update 11
* 用于解决以下问题的 Bug 修复：
  * 配置服务器或 RX 服务器的保管库注册失败。
  * 当群集虚拟机在恢复期间重新受保护时，群集卷不会按预期显示。
  * 当主目标服务器托管在与本地生产虚拟机不同的 ESXi 服务器中时，故障回复失败。
  * 升级到 8.0.1 时，配置文件权限发生更改，从而影响保护和操作。
  * 不按预期强制实施重新同步阈值，从而导致不一致的复制行为。
  * RPO 设置未正常显示在配置服务器界面中。 未压缩的数据值错误地显示压缩值。
  * 在 vContinuum 向导中使用“删除”操作不会按预期执行删除，因而无法从配置服务器界面删除复制内容。
  * 在 vContinuum 向导中保护 MSCS 虚拟机期间，单击磁盘视图中的“**详细信息**”会自动取消选择磁盘。
  * 在执行物理到虚拟 (P2V) 方案期间，所需的 HP 服务（例如 CIMnotify、CqMgHost）不会在虚拟机恢复中变为“手动”。 这会导致启动时间延长。
  * 当主目标服务器上的磁盘数超过 26 个时，Linux 虚拟机保护会失败。

## <a name="next-steps"></a>后续步骤
请在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上发布任何问题。
