---
title: 使用 Azure Site Recovery 将 VMware VM 或物理服务器的灾难恢复设置到辅助站点 | Microsoft 文档
description: 了解如何使用 Azure Site Recovery 将 VMware VM 或 Windows 和 Linux 物理服务器的灾难恢复设置到辅助站点。
services: site-recovery
author: nsoneji
manager: gauarvd
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 7d172b89a0846c8ccf19896400e127a3bec0b55e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852211"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>将本地 VMware 虚拟机或物理服务器的灾难恢复设置到辅助站点

[Azure Site Recovery](site-recovery-overview.md) 中的 InMage Scout 在本地 VMware 站点之间提供实时复制。 InMage Scout 随附在 Azure Site Recovery 服务订阅中。 


## <a name="prerequisites"></a>先决条件

完成本教程：

- [查看](vmware-physical-secondary-support-matrix.md)所有组件的支持要求。
- 确保要复制的计算机符合[复制计算机支持](vmware-physical-secondary-support-matrix.md#replicated-vm-support)。


## <a name="create-a-vault"></a>创建保管库

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>选择保护目标

选择要复制的内容以及要将内容复制到的位置。

1. 单击“Site Recovery” > “准备基础结构” > “保护目标”。
2. 选择“到恢复站点” > “是，使用 VMware vSphere 虚拟机监控程序”。 然后单击“确定”。
3. 在“Scout 安装程序”中，下载 InMage Scout 8.0.1 GA 软件和注册密钥。 所有组件的安装程序文件都包含在下载的 .zip 文件中。

## <a name="download-and-install-component-updates"></a>下载并安装组件更新

 查看并安装最新的[更新](#updates)。 应按以下顺序在服务器上安装更新：

1. RX 服务器（如果适用）
2. 配置服务器
3. 进程服务器
4. 主目标服务器
5. vContinuum 服务器
6. 源服务器（Windows 和 Linux 服务器）

按以下步骤安装更新：

> [!NOTE]
>所有 Scout 组件的文件更新版本可能与更新 .zip 文件中的版本不同。 较旧的版本表示此更新自上一次更新以来组件中没有任何更改。

下载[更新](https://aka.ms/asr-scout-update6)后的 .zip 文件。 文件包含以下组件： 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - 对于 RHEL5、OL5、OL6、SUSE 10、SUSE 11 的 UA update4 位：UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. 解压缩 .zip 文件。
2. RX 服务器：将 RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz 复制到 RX 服务器并将其解压缩。 在解压缩的文件夹中运行 **/Install**。
3. 配置服务器和进程服务器：将 CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe 复制到配置服务器和进程服务器。 双击以运行该文件。<br>
4. Windows 主目标服务器：要更新统一代理，请将 UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe 复制到该服务器。 双击以运行该文件。 相同的统一代理更新也适用于源服务器。 如果源尚未更新到 Update 4，则应更新统一代理。
  更新不需要应用于备有 InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe 的主目标服务器，因为这是拥有所有最新更改的新 GA 安装程序。
5. vContinuum 服务器：将 vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe 复制到该服务器。  确保已关闭 vContinuum 向导。 双击以运行该文件。
    更新不需要应用于备有 InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe 的主目标服务器，因为这是拥有所有最新更改的新 GA 安装程序。
6. Linux 主目标服务器：要更新统一代理，请将 UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz 复制到主目标服务器并将其解压缩。 在解压缩的文件夹中运行 **/Install**。
7. Windows 源服务器：要更新统一代理，请将 UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe 复制到该源服务器。 双击以运行该文件。 
    如果源服务器已更新到 Update 4 或源代理已安装有最新的基本安装程序 InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe，则不需要在源服务器上安装 Update 5 代理。
8. Linux 源服务器：要更新统一代理，请将相应版本的统一代理文件复制到 Linux 服务器并将其解压缩。 在解压缩的文件夹中运行 **/Install**。  示例：对于 RHEL 6.7 64 位服务器，将 UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz 复制到该服务器并将其解压缩。 在解压缩的文件夹中运行 **/Install**。

## <a name="enable-replication"></a>启用复制

1. 设置源与目标 VMware 站点之间的复制。
2. 请参阅以下文档，了解有关安装、保护和恢复的详细信息：

   * [发行说明](https://aka.ms/asr-scout-release-notes)
   * [兼容性对照表](https://aka.ms/asr-scout-cm)
   * [用户指南](https://aka.ms/asr-scout-user-guide)
   * [RX 用户指南](https://aka.ms/asr-scout-rx-user-guide)
   * [快速安装指南](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>更新

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Update 6 
更新时间：2017 年 10 月 12 日

下载 [Scout Update 6](https://aka.ms/asr-scout-update6)。

Scout Update 6 是累积更新。 其中包含从 Update 1 到 Update 5 的所有修补程序，以及下面所述的新修补程序和增强功能。 

#### <a name="new-platform-support"></a>新的平台支持
* 已添加对 Source Windows Server 2016 的支持
* 已添加对以下 Linux 操作系统的支持：
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* 已添加对 VMware Center 6.5 的支持

> [!NOTE]
> * 已刷新面向 Windows 的基本统一代理 (UA) 安装程序，以支持 Windows Server 2016。 新的安装程序 InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe 与基本 Scout GA 程序包一起打包 (InMage_Scout_Standard_8.0.1 GA-Oct17.zip)。 相同的安装程序将用于所有受支持的 Windows 版本。 
> * 已刷新基本 Windows vContinuum 和主目标安装程序来支持 Windows Server 2016。 新的安装程序 InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe 与基本 Scout GA 程序包一起打包 (InMage_Scout_Standard_8.0.1 GA-Oct17.zip)。 相同的安装程序将用于部署 Windows 2016 主目标和 Windows 2012R2 主目标。
> * 从门户中下载 GA 包，如[创建保管库](#create-a-vault)中所述。
>

#### <a name="bug-fixes-and-enhancements"></a>Bug 修复和增强功能
- Linux VM 的故障回复保护失败，而且在配置结束时，要复制的磁盘列表为空。

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Update 5
Scout Update 5 是累积更新。 其中包含从 Update 1 到 Update 4 的所有修补程序，以及下面所述的新修补程序。
- Site Recovery Scout Update 4 至 Update 5 中的修补程序专门用于主目标组件和 vContinuum 组件。
- 如果源服务器、主目标、配置、进程和 RX 服务器已在运行 Update 4，则仅将其应用在主目标服务器上。 

#### <a name="new-platform-support"></a>新的平台支持
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)
* SLES 11 SP4 64 位 InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz 与基础 Scout GA 包 (**InMage_Scout_Standard_8.0.1 GA.zip**) 打包在一起。 从门户中下载 GA 包，如[创建保管库](#create-a-vault)中所述。


#### <a name="bug-fixes-and-enhancements"></a>Bug 修复和增强功能

* 用于提高 Windows 群集支持可靠性的修补程序：
    * 已修复 - 某些 P2V MSCS 群集磁盘在恢复后成为 RAW。
    * 已修复 - P2V MSCS 群集恢复由于磁盘顺序不匹配而失败。
    * 已修复 - 添加磁盘的 MSCS 群集操作失败并出现磁盘大小不匹配错误。
    * 已修复 - 包含 RDM LUN 的源 MSCS 群集的映射就绪状态检查在验证大小时失败。
    * 已修复 - 单节点群集保护由于 SCSI 不匹配问题而失败。 
    * 已修复 - 当存在目标群集磁盘时，P2V Windows 群集服务器的重新保护失败。 
    
* 已修复：在故障回复保护期间，如果所选主目标服务器与受保护源计算机（在正向保护期间）不在同一 ESXi 服务器上，则 vContinuum 在故障回复恢复期间会选取错误的主目标服务器，并且恢复操作会失败。

> [!NOTE]
> * P2V 群集修补程序仅适用于使用 Site Recovery Scout Update 5 全新保护的物理 MSCS 群集。 若要在使用较旧更新程序保护的 P2V MSCS 群集上安装群集修补程序，请按照 [Site Recovery Scout 发行说明](https://aka.ms/asr-scout-release-notes)第 12 节中所述的升级步骤进行操作。
> * 如果在重新保护时，同一组磁盘在最初受保护的每个群集节点上都处于活动状态，则物理 MSCS 群集的重新保护只能重新使用现有的目标磁盘。 如果不是，则需执行 [Site Recovery Scout 发行说明](https://aka.ms/asr-scout-release-notes)第 12 节中提到的手动步骤，将目标端磁盘移动到正确的数据存储路径，以便在重新保护期间重新使用它们。 如果在不执行以下升级步骤的情况下重新保护处于 P2V 模式的 MSCS 群集，则它会在目标 ESXi 服务器上创建新磁盘。 你将需要手动从数据存储中删除旧磁盘。
> * 当源 SLES11 或 SLES11（具有任何 Service Pack）服务器正常重新启动时，手动标记根磁盘复制对，以便重新同步。 CX 界面中不显示任何通知。 如果不标记用于重新同步的根磁盘，则可能会发现数据完整性问题。


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 是累积更新。 其中包含从 Update 1 到 Update 3 的所有修补程序，以及下面所述的新修补程序。

#### <a name="new-platform-support"></a>新的平台支持

* 已添加对 vCenter/vSphere 6.0、6.1 和 6.2 的支持
* 已添加对以下 Linux 操作系统的支持：
  * Red Hat Enterprise Linux (RHEL) 7.0、7.1 和 7.2
  * CentOS 7.0、7.1 和 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 位 InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz 与基础 Scout GA 包 InMage_Scout_Standard_8.0.1 GA.zip 一起打包。 从门户中下载 Scout GA 包，如[创建保管库](#create-a-vault)中所述。

#### <a name="bug-fixes-and-enhancements"></a>Bug 修复和增强功能

* 改进了以下 Linux 操作系统和克隆的关闭处理方式，以防止意外的重新同步问题：
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* 对于 Linux，统一代理安装目录中的所有文件夹访问权限现在仅限为本地用户。
* 在 Windows 上，修复了在重负载分布式应用程序（例如 SQL Server 和 Share Point 群集）上发出通用分布式一致性书签时出现的超时问题。
* 配置服务器基本安装程序中日志相关的修复。
* 将 VMware vCLI 6.0 的下载链接添加到了 Windows 主目标基本安装程序中。
* 在故障转移和灾难恢复演练期间，针对网络配置更改添加了其他检查和日志。
* 修复了导致保留信息无法报告给配置服务器的问题。  
* 对于物理群集，修复了在 vContinuum 向导中收缩源卷时导致卷大小调整失败的问题。
* 修复了当群集磁盘为 PRDM 磁盘时，群集保护失败并出现“未能找到磁盘签名”错误的问题。
* 修复了 cxps 传输服务器由于超出范围的异常而崩溃的问题。
* 在 vContinuum 向导的“推送安装”页面中，现在可对服务器名称列和 IP 地址列的大小进行调整。
* RX API 的增强功能：
  * 现在提供了 5 个最新可用的通用一致性点（仅适用于“保证”标记）。
  * 显示有关所有受保护设备的容量及可用空间的详细信息。
  * 提供源服务器上的 Scout 驱动程序状态。

> [!NOTE]
> * InMage_Scout_Standard_8.0.1_GA.zip 基础包中含有：
    * 更新的配置服务器基本安装程序 (InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe)
    * Windows 主目标基本安装程序 (InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe)。
    * 对于所有新安装，请使用新的配置服务器和 Windows 主目标 GA 位。
> * Update 4 可以直接应用于 8.0.1 GA。
> * 应用配置服务器和 RX 更新后，不能进行回滚。


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3

所有 Site Recovery 更新都是累积更新。 Update 3 包含来自 Update 1 和 Update 2 的所有修补程序。 Update 3 可以直接应用于 8.0.1 GA。 应用配置服务器和 RX 更新后，不能进行回滚。

#### <a name="bug-fixes-and-enhancements"></a>Bug 修复和增强功能
Update 3 修复了以下问题：

* 配置服务器和 RX 位于代理后面时不会在保管库进行注册。
* 恢复点目标 (RPO) 未达到的小时数不会在运行状况报告中更新。
* 当 ESX 硬件详细信息或网络详细信息包含任何 UTF-8 字符时，配置服务器不会与 RX 同步。
* Windows Server 2008 R2 域控制器在恢复之后不启动。
* 脱机同步不按预期工作。
* VM 故障转移后，复制对删除的进度在配置服务器控制台中长时间没有变化。 用户无法完成故障回复或恢复操作。
* 由一致性作业执行的整体快照操作已经过优化，可帮助减少应用程序（例如 SQL Server 客户端）断开连接的问题。
* 改进了一致性工具 (VACP.exe) 的性能。 减少了在 Windows 上创建快照所需的内存使用量。
* 密码超过 16 个字符时，推送安装服务会崩溃。
* 修改凭据后，vContinuum 不检查和提示输入新的 vCenter 凭据。
* 在 Linux 上，主目标缓存管理器 (cachemgr) 不会从进程服务器下载文件。 这会导致复制对限制。
* 当物理故障转移群集 (MSCS) 磁盘顺序在所有节点上都不相同时，不会为某些群集卷设置复制。 群集必须获得重新保护才能利用这一修复。  
* 在 RX 从 Scout 7.1 升级到 Scout 8.0.1 之后，SMTP 功能无法正常工作。
* 已在回滚操作日志中添加更多统计信息，以跟踪完成此操作所需的时间。
* 已添加对源服务器上 Linux 操作系统的支持：
  * Red Hat Enterprise Linux (RHEL) 6 update 7
  * CentOS 6 Update 7
* 配置服务器和 RX 控制台现在显示进入位图模式的对的通知。
* 已将以下安全修复程序添加到 RX 中：
    * 通过参数篡改绕过授权：已将访问权限限制为不适用的用户。
    * 跨网站请求伪造：实现页令牌概念，将为每一页随机生成。 这意味着同一用户只有一个登录实例，并且页面刷新不起作用。 相反，它将重定向到仪表板。
    * 恶意文件上传：限制文件使用特定的扩展名：z、aiff、asf、avi、bmp、csv、doc、docx、fla、flv、gif、gz、gzip、jpeg、jpg、log、mid、mov、mp3、mp4、mpc、mpeg、mpg、ods、odt、pdf、png、ppt、pptx、pxd、qt、ram、rar、rm、rmi、rmvb、rtf、sdc、sitd、swf、sxc、sxw、tar、tgz、tif、tiff、txt、vsd、wav、wma、wmv、xls、xlsx、xml 和 zip。
    * 持久性跨站点脚本：添加了输入验证。

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (Update 03Dec15)

Update 2 中的修复包括：

* 配置服务器：在 Site Recovery 中注册配置服务器时阻止 31 天免费计量功能正常使用的问题。
* 统一代理：修复了 Update 1 中导致从版本 8.0 升级到 8.0.1 期间，更新无法安装在主目标服务器上的问题。

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Update 1 包含以下 bug 修复和新功能：

* 每个服务器实例享有 31 天的免费保护。 这样，便可以测试功能或建立概念认证。
* 服务器上的所有操作（包括故障转移和故障回复），前 31 天都可免费使用。 从第一次使用 Site Recovery Scout 保护服务器开始计时。 从第 32 天起，会根据标准实例费率，针对每个受保护的服务器，向客户拥有的站点收取 Site Recovery 保护费用。
* 在保管库中的“仪表板”上随时会显示当前计费的受保护服务器数目。
* 添加了对 vSphere 命令行接口 (vCLI) 5.5 Update 2 的支持。
* 添加了对源服务器上以下 Linux 操作系统的支持：
    * RHEL 6 Update 6
    * RHEL 5 Update 11
    * CentOS 6 Update 6
    * CentOS 5 Update 11
* 用于解决以下问题的 Bug 修复：
  * 配置服务器或 RX 服务器的保管库注册失败。
  * 当群集 VM 在恢复期间被重新保护时，群集卷不会按预期显示。
  * 当主目标服务器托管在与本地生产 VM 不同的 ESXi 服务器中时，故障回复失败。
  * 升级到 8.0.1 时，配置文件权限发生更改。 此更改会影响保护和操作。
  * 重新同步阈值不按预期强制执行，导致复制行为不一致。
  * RPO 设置未正常显示在配置服务器控制台中。 未压缩的数据值错误地显示压缩值。
  * 在 vContinuum 向导中使用“删除”操作不会按预期执行删除，因而无法从配置服务器控制台删除复制内容。
  * 在 vContinuum 向导中保护 MSCS VM 期间，单击磁盘视图中的“详细信息”会自动取消选择磁盘。
  * 在物理到虚拟 (P2V) 方案中，所需的 HP 服务（例如 CIMnotify、CqMgHost）不会在 VM 恢复中变为“手动”。 此问题会导致启动时间延长。
  * 当主目标服务器上的磁盘数超过 26 个时，Linux VM 保护会失败。

