---
title: 排查 Azure Site Recovery 中的 Azure VM 复制问题
description: 解决复制 Azure 虚拟机进行灾难恢复时出现的错误。
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: 3f97975f09d846cd3277bb8a53a4ad922f1b5b69
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902555"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>排查 Azure 到 Azure VM 复制错误

本文介绍如何解决在将 Azure 虚拟机（Vm）从一个区域复制和恢复到另一个区域的过程中 Azure Site Recovery 的常见错误。 有关受支持的配置的详细信息，请参阅[复制 Azure VM 支持矩阵](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 资源配额问题（错误代码 150097）

请确保已启用订阅，以便在你计划用作灾难恢复区域的目标区域中创建 Azure Vm。 此外，请确保你的订阅具有足够的配额来创建所需大小的 Vm。 默认情况下，Site Recovery 选择与源 VM 大小相同的目标 VM 大小。 如果匹配大小不可用，Site Recovery 会自动选择最接近的可用大小。

如果没有支持源 VM 配置的大小，将显示以下消息：

> "无法为虚拟机*VmName*启用复制"。

### <a name="possible-causes"></a>可能的原因

- 你的订阅 ID 未启用，无法在目标区域位置创建任何 Vm。
- 订阅 ID 未启用或没有足够的配额，无法在目标区域位置中创建特定的 VM 大小。
- 对于目标区域位置中的订阅 ID，找不到适合源 VM 的网络接口卡（NIC）数（2）的合适的目标 VM 大小。

### <a name="fix-the-problem"></a>解决问题

请联系[Azure 计费支持人员](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)，使订阅能够在目标位置创建所需大小的 vm。 然后，重试失败的操作。

如果目标位置具有容量约束，则禁用向其复制。 然后，启用到不同位置的复制，以便订阅具有足够的配额来创建所需大小的 Vm。

## <a name="trusted-root-certificates-error-code-151066"></a>受信任的根证书（错误代码 151066）

如果 VM 上不存在所有最新的受信任根证书，则 "启用复制" Site Recovery 作业可能会失败。 在没有这些证书的情况下，VM 中的 Site Recovery 服务调用的身份验证和授权会失败。 

如果 "启用复制" 作业失败，将显示以下消息：

> "Site Recovery 配置失败。"

### <a name="possible-cause"></a>可能的原因

虚拟机上不存在授权和身份验证所需的受信任的根证书。

### <a name="fix-the-problem"></a>解决问题

#### <a name="windows"></a>Windows

对于运行 Windows 操作系统的 VM，请在 VM 上安装最新的 Windows 更新，使所有受信任的根证书都存在于计算机上。 按照组织中典型的 Windows 更新管理或证书更新管理过程操作，在 Vm 上获取最新的根证书和更新的证书吊销列表。

如果处于未联网的环境中，请按照组织中的标准 Windows 更新过程获取证书。 如果 VM 上没有所需的证书，对 Site Recovery 服务的调用会出于安全原因失败。

若要验证问题是否已解决，请从 VM 中的浏览器转到 login.microsoftonline.com。

有关详细信息，请参阅[配置受信任的根和不允许的证书](https://technet.microsoft.com/library/dn265983.aspx)。

#### <a name="linux"></a>Linux

按照 Linux 操作系统版本分销商提供的指导，在 VM 上获取最新的受信任根证书和最新的证书吊销列表。

由于 SUSE Linux 使用符号链接（或*符号链接*）来维护证书列表，请遵循以下步骤：

1. 以根用户身份登录。

1. 运行以下命令以更改目录：

    **# cd/etc/ssl/certs**

1. 检查是否存在 Symantec 根 CA 证书：

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5 pem**

1. 如果找不到 Symantec 根 CA 证书，请运行以下命令下载该文件。 检查是否有任何错误，并针对网络故障执行建议的操作。

    **# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5 pem**

1. 检查是否存在巴尔的摩根 CA 证书：

    **# ls Baltimore_CyberTrust_Root pem**

1. 如果未找到巴尔的摩根 CA 证书，请运行以下命令下载证书：

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root pem**

1. 检查是否存在 DigiCert_Global_Root_CA 证书：

    **# ls DigiCert_Global_Root_CA pem**

1. 如果找不到 DigiCert_Global_Root_CA，请运行以下命令下载证书：

    **# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509-in DigiCertGlobalRootCA-通知 der-outform pem-out DigiCert_Global_Root_CA pem**

1. 运行 rehash 脚本以更新新下载证书的证书使用者哈希：

    **# c_rehash**

1. 运行以下命令，检查是否已为证书创建了使用者哈希作为符号链接：

    - 命令：

        **# ls-l |grep 巴尔的摩**

    - 输出：

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - 命令：

        **# ls-l |grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - 输出：

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - 命令：

        **# ls-l |grep DigiCert_Global_Root**

    - 输出：

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. 使用文件名 b204d74a.0 创建文件 VeriSign_Class_3_Public_Primary_Certification_Authority_G5 的副本：

    **# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5 pem b204d74a。0**

1. 使用文件名653b494a.0 创建文件 Baltimore_CyberTrust_Root 的副本：

    **# cp Baltimore_CyberTrust_Root pem 653b494a。0**

1. 使用文件名3513523f 创建文件 DigiCert_Global_Root_CA 的副本：

    **# cp DigiCert_Global_Root_CA pem 3513523f**

1. 检查文件是否存在：

    - 命令：

        **# ls-l 653b494a.0 0 b204d74a.0 3513523f. 0**

    - 输出

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 范围的出站连接（错误代码 151037 或 151072）

要使 Site Recovery 复制正常运行，需要从 VM 到特定 Url 或 IP 范围的出站连接。 如果 VM 位于防火墙后或使用网络安全组 (NSG) 规则来控制出站连接，则可能会遇到以下问题之一。

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>问题1：未能将 Azure 虚拟机注册 Site Recovery （错误代码151195）

#### <a name="possible-cause"></a>可能的原因 

由于 DNS 解析失败，无法建立与 Site Recovery 终结点的连接。

当你对虚拟机进行故障转移，但无法从灾难恢复（DR）区域访问 DNS 服务器时，在重新保护期间最常发生此问题。

#### <a name="fix-the-problem"></a>解决问题

如果使用的是自定义 DNS，请确保可从灾难恢复区域访问 DNS 服务器。 若要确定是否有自定义 DNS，请在 VM 上，中转到 "*灾难恢复网络*" > **DNS 服务器**"。

![自定义 DNS 服务器列表](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

尝试从虚拟机访问 DNS 服务器。 如果该服务器不可访问，请通过故障转移 DNS 服务器或在 DR 网络和 DNS 之间创建站点线路，使该服务器可访问。

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>问题2： Site Recovery 配置失败（错误代码151196）

#### <a name="possible-cause"></a>可能的原因

无法建立到 Office 365 身份验证和标识 IP4 终结点的连接。

#### <a name="fix-the-problem"></a>解决问题

Site Recovery 需要访问 Office 365 IP 范围才能进行身份验证。
如果你使用 Azure NSG 规则或防火墙代理来控制 VM 上的出站网络连接，请确保允许与 Office 365 IP 范围通信。 基于[Azure Active Directory （Azure AD）服务标记](../virtual-network/security-overview.md#service-tags)创建 NSG 规则，以允许访问与 Azure AD 对应的所有 IP 地址。 如果以后将新地址添加到 Azure AD，则必须创建新的 NSG 规则。

> [!NOTE]
> 如果 Vm 位于*标准*内部负载均衡器后面，则默认情况下，负载均衡器无权访问 OFFICE 365 IP 范围（即 login.microsoftonline.com）。 将内部负载均衡器类型更改为 "*基本*"，或创建 "[配置负载均衡和出站规则](https://aka.ms/lboutboundrulescli)" 一文中所述的出站访问权限。

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>问题3： Site Recovery 配置失败（错误代码151197）

#### <a name="possible-cause"></a>可能的原因

无法建立与 Site Recovery 服务终结点的连接。

#### <a name="fix-the-problem"></a>解决问题

Site Recovery 需要访问[Site Recovery 的 IP 范围](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)，具体取决于区域。 确保所需的 IP 范围可从虚拟机访问。

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>问题4：当网络流量通过本地代理服务器时，Azure 到 Azure 的复制失败（错误代码151072）

#### <a name="possible-cause"></a>可能的原因

自定义代理设置无效，并且 Site Recovery 移动服务代理未自动检测 Internet Explorer 中的代理设置。

#### <a name="fix-the-problem"></a>解决问题

移动服务代理从 Windows 上的 Internet Explorer 和 Linux 上的/etc/environment 检测代理设置。

如果希望仅为移动服务设置代理，可在以下位置的 ProxyInfo 文件中提供代理详细信息：

- **Linux**：/usr/local/InMage/config/
- **Windows**： C:\ProgramData\Microsoft Azure Site Recovery\Config

在 ProxyInfo 中，提供以下初始化文件格式的代理设置：

> [*proxy*]

> Address = *http://1.2.3.4*

> 端口 =*567*


> [!NOTE]
> Site Recovery 移动服务代理仅支持*未经身份验证的代理*。

### <a name="more-information"></a>详细信息

若要指定[所需的 url](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)或[所需的 IP 范围](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)，请按照[关于 Azure 中的网络到 azure 的复制](site-recovery-azure-to-azure-networking-guidance.md)中的指导进行操作。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>在计算机中找不到磁盘（错误代码 150039）

必须初始化附加到 VM 的新磁盘。 如果找不到该磁盘，将显示以下消息：

> "Azure data disk *DiskName* *DiskURI* With logical unit number *LUN* *LUNVALUE*未映射到从具有相同 lun 值的 VM 报告的相应磁盘。

### <a name="possible-causes"></a>可能的原因

- 新的数据磁盘已附加到 VM，但未初始化。
- VM 中的数据磁盘未正确报告磁盘已附加到 VM 的逻辑单元号（LUN）值。

### <a name="fix-the-problem"></a>解决问题

请确保数据磁盘已初始化，然后重试该操作。

- **Windows**：[附加并初始化新磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。

- **Linux**：[在 linux 中初始化新的数据磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

如果问题持续出现，请联系支持人员。

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>有一个或多个磁盘可用于保护（错误代码153039）

### <a name="possible-causes"></a>可能的原因

- 在保护后，一个或多个磁盘最近添加到了虚拟机中。
- 在保护虚拟机后，一个或多个磁盘已初始化。

### <a name="fix-the-problem"></a>解决问题

若要使 VM 的复制状态再次正常运行，可以选择保护磁盘或消除警告。

#### <a name="to-protect-the-disks"></a>保护磁盘

1.  > *VM 名称* > **磁盘**中转到 "**复制的项**"。
1. 选择未受保护的磁盘，然后选择 "**启用复制**"：

    ![在 VM 磁盘上启用复制](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>消除警告

1.  > *VM 名称*中转到 "**复制的项**"。
1. 在 "**概述**" 部分选择该警告，然后选择 **"确定"** 。

    ![消除新磁盘警告](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>从保管库中删除已完成的虚拟机，并提供信息（错误代码150225）

在保护虚拟机时，Site Recovery 会在源虚拟机上创建一些链接。 删除保护或禁用复制时，Site Recovery 会将这些链接作为清除作业的一部分删除。 如果虚拟机具有资源锁，则使用该信息完成清理作业。 此信息指出，已从恢复服务保管库中删除了虚拟机，但无法清除源计算机上的某些过期链接。

如果你不打算再次保护此虚拟机，则可以忽略此警告。 但如果你以后需要保护此虚拟机，请按照 "修复问题" 下的步骤来清理链接。

> [!WARNING]
> 如果不执行清除操作：
>
> - 通过恢复服务保管库启用复制时，不会列出虚拟机。
> - 如果尝试使用**虚拟机** > **设置** > **灾难恢复**来保护 VM，则操作将失败，并出现消息 "由于 VM 上存在现有过时资源链接，无法启用复制"。

### <a name="fix-the-problem"></a>解决问题

> [!NOTE]
> 执行这些步骤时，Site Recovery 不会删除源虚拟机或以任何方式影响源虚拟机。

1. 删除 VM 或 VM 资源组中的锁。 例如，在下图中，必须删除名为 "MoveDemo" 的 VM 上的资源锁：

    ![从 VM 中删除锁定](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 下载脚本以[删除过时的 Site Recovery 配置](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)。
1. 运行脚本，该脚本称为 Cleanup-stale-asr-config-Azure-VM。 提供订阅 ID、VM 资源组和 VM 名称作为参数。
1. 如果系统要求提供 Azure 凭据，请提供凭据。 然后，验证脚本是否正常运行。

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>无法启用复制，因为 VM 上的资源链接过时（错误代码150226）

### <a name="possible-cause"></a>可能的原因

虚拟机具有以前 Site Recovery 保护的陈旧配置。

如果通过使用 Site Recovery 为 Azure VM 启用复制，则在 Azure VM 上可能会发生过时的配置，然后执行以下操作：

- 已禁用复制，但源 VM 具有资源锁。
- 删除了 Site Recovery 保管库，但未在 VM 上明确禁用复制。
- 已删除包含 Site Recovery 保管库的资源组，而不在 VM 上明确禁用复制。

### <a name="fix-the-problem"></a>解决问题

> [!NOTE]
> 执行这些步骤时，Site Recovery 不会删除源虚拟机或以任何方式影响源虚拟机。

1. 删除 VM 或 VM 资源组中的锁。 例如，在下图中，必须删除名为 "MoveDemo" 的 VM 上的资源锁：

    ![从 VM 中删除锁定](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 下载脚本以[删除过时的 Site Recovery 配置](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)。
1. 运行脚本，该脚本称为 Cleanup-stale-asr-config-Azure-VM。 提供订阅 ID、VM 资源组和 VM 名称作为参数。
1. 如果系统要求提供 Azure 凭据，请提供凭据。 然后，验证脚本是否正常运行。

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>无法在 "启用复制" 作业中查看所选内容的 Azure VM 或资源组

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>原因1：资源组和源虚拟机位于不同的位置

Site Recovery 当前要求源区域资源组和虚拟机位于同一位置。 否则，你将无法在尝试应用保护时找到虚拟机或资源组。

作为一种解决方法，你可以从 VM 而不是恢复服务保管库启用复制。 请 > **属性**中转到**源 VM** > **灾难恢复**，并启用复制。

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>原因2：资源组不属于所选订阅

如果资源组不属于所选订阅，则在保护时找不到资源组。 请确保资源组属于你正在使用的订阅。

### <a name="cause-3-stale-configuration"></a>原因3：过时配置

如果 Azure VM 上已保留过时的 Site Recovery 配置，则可能看不到要为复制启用的 VM。 如果使用 Site Recovery 为 Azure VM 启用了复制，则可能出现此情况，然后执行以下操作：

- 删除了 Site Recovery 保管库，但未在 VM 上明确禁用复制。
- 已删除包含 Site Recovery 保管库的资源组，而不在 VM 上明确禁用复制。
- 已禁用复制，但源 VM 具有资源锁。

### <a name="fix-the-problem"></a>解决问题

> [!NOTE]
> 请确保在使用本节中所述的脚本之前，更新 "AzureRM" 模块。  执行这些步骤时，Site Recovery 不会删除源虚拟机或以任何方式影响源虚拟机。

1. 删除 VM 或 VM 资源组中的锁（如果有）。 例如，在下图中，必须删除名为 "MoveDemo" 的 VM 上的资源锁：

    ![从 VM 中删除锁定](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 下载脚本以[删除过时的 Site Recovery 配置](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)。
1. 运行脚本，该脚本称为 Cleanup-stale-asr-config-Azure-VM。 提供订阅 ID、VM 资源组和 VM 名称作为参数。
1. 如果系统要求提供 Azure 凭据，请提供凭据。 然后，验证脚本是否正常运行。

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>无法选择要保护的虚拟机

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>原因1：虚拟机安装的扩展处于失败或无响应状态

中转到 "**虚拟机**" > **设置**" > **扩展**"，并检查是否有任何处于 "失败" 状态的扩展。 请卸载任何失败的扩展，然后重试以保护虚拟机。

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>原因2： VM 的预配状态无效

请参阅本文后面的[VM 预配状态](#the-vms-provisioning-state-is-not-valid-error-code-150019)中的故障排除步骤无效。

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>VM 的预配状态无效（错误代码150019）

若要在 VM 上启用复制，其预配状态必须为 "**成功**"。 请按照以下步骤检查预配状态：

1. 在 Azure 门户中，从 "**所有服务**" 中选择 "**资源浏览器**"。
1. 展开“订阅”列表并选择你的订阅。
1. 展开 **ResourceGroups** 列表并选择 VM 的资源组。
1. 展开 "**资源**" 列表并选择 VM。
1. 在右侧的实例视图中检查**provisioningState**字段。

### <a name="fix-the-problem"></a>解决问题

- 如果 **provisioningState** 是“失败”，请联系支持人员并提供详细信息，以便进行故障排除。
- 如果正在**更新** **provisioningState** ，则可能会部署其他扩展。 检查 VM 上是否有任何正在进行的操作，等待它们完成，然后重试失败的 "启用复制" 作业 Site Recovery。

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>无法选择目标 VM （网络选择选项卡不可用）

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>原因1：你的 VM 已连接到已映射到目标网络的网络

如果源 VM 是虚拟网络的一部分，并且同一虚拟网络中的另一个 VM 已与目标资源组中的网络映射，则默认情况下 "网络选择" 下拉列表框不可用（显示为灰色）。

![网络选择列表不可用](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>原因2：以前使用 Site Recovery 保护了 VM，然后禁用了复制

禁用 VM 复制不会删除网络映射。 必须从保护 VM 的恢复服务保管库中删除该映射。 请参阅*恢复服务保管库* > **Site Recovery 基础结构** > **网络映射**。

![删除网络映射](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

在虚拟机受到保护后，可在初始设置后更改在灾难恢复设置过程中配置的目标网络：

![修改网络映射](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

请注意，更改网络映射会影响使用同一网络映射的所有受保护的 Vm。

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM + 或卷影复制服务错误（错误代码151025）

发生此错误时，将显示以下消息：

> "Site Recovery 扩展安装失败"

### <a name="possible-causes"></a>可能的原因

- COM + 系统应用程序服务已禁用。
- 卷影复制服务已禁用。

### <a name="fix-the-problem"></a>解决问题

将 COM + 系统应用程序和卷影复制服务设置为自动或手动启动模式。

1. 在 Windows 中打开 "服务" 控制台。
1. 确保 "COM + 系统应用程序" 和 "卷影复制服务" 不设置为 "**已禁用**" 作为**启动类型**。

    ![检查 COM + 系统应用程序和卷影复制服务的启动类型](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>托管磁盘大小不受支持（错误代码150172）

发生此错误时，将显示以下消息：

> "无法为虚拟机启用保护，因为它的*DiskName*大小为*DiskSize*，小于支持的最小大小 1024 MB。"

### <a name="possible-cause"></a>可能的原因

磁盘小于支持的大小 1024 MB。

### <a name="fix-the-problem"></a>解决问题

请确保磁盘大小在支持的大小范围内，然后重试该操作。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>未启用保护，因为 GRUB 配置包含设备名称，而不是 UUID （错误代码151126）

### <a name="possible-cause"></a>可能的原因

Linux GRUB 配置文件（/boot/grub/menu.lst "、/boot/grub/grub.cfg、/boot/grub2/grub.cfg 或/etc/default/grub）可能会指定实际的设备名称，而不是*root*和*RESUME*参数的 UUID 值。 Site Recovery 需要 Uuid，因为设备名称可能会改变。 重新启动时，VM 可能不会在故障转移时出现相同的名称，导致出现问题。

以下示例是来自 GRUB 文件的行，其中显示的设备名称（以粗体显示）而不是所需的 Uuid：

- 文件/boot/grub2/grub.cfg

  > linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts

- 文件：/boot/grub/menu.lst

  > 内核/boot/vmlinuz-3.0.101-63-default **root =/dev/sda2** **resume =/dev/sda1**闪屏 = 缄默 crashkernel = 256M-： 128M showopts vga = 0x314


### <a name="fix-the-problem"></a>解决问题

将每个设备名称替换为相应的 UUID：

1. 通过执行命令**blkid** ***device name***查找设备的 UUID。 例如：

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 将设备名称替换为其 UUID，格式为**root = uuid**=*uuid* ， **resume = uuid**=*uuid*。 例如，在替换后，/boot/grub/menu.lst 中的行（前面讨论过）将如下所示：

    > 内核/boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b**闪屏 = 缄默 crashkernel = 256M-： 128M showopts vga = 0x314

1. 请重试保护。

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>启用保护失败，因为 GRUB 配置中提到的设备不存在（错误代码151124）

### <a name="possible-cause"></a>可能的原因

GRUB 配置文件（/boot/grub/menu.lst、/boot/grub/grub.cfg、/boot/grub2/grub.cfg 或/etc/default/grub）可能包含参数*rd.lvm.lv*或*rd_LVM_LV*。 这些参数标识要在启动时发现的逻辑卷管理器（LVM）设备。 如果这些 LVM 设备不存在，则受保护的系统本身将不会启动，并且会停滞在启动过程中。 故障转移 VM 也会出现相同的问题。 以下是几个示例：

- 文件： RHEL7 上的/boot/grub2/grub.cfg：

    > linux16/vmlinuz-3.10.0-957.el7. x86_64 root =/dev/mapper/rhel_mup--rhel7u6 ro crashkernel = 128M\@Ed-64m **。 lv =** rootvg/= rootvg/swap rhgb en_US quiet。UTF-8

- 文件： RHEL7 上的/etc/default/grub：

    > GRUB_CMDLINE_LINUX = "crashkernel =" **rootvg = rootvg/root =/swap** rhgb quiet "

- 文件： RHEL6 上的/boot/grub/menu.lst：

    > 内核/vmlinuz-2.6.32-754.el6. x86_64 ro root = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US。UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = auto **rd_LVM_LV = rootvg/lv_root** KEYBOARDTYPE = pc KEYTABLE = 美国**rd_LVM_LV = rootvg/lv_swap** rd_NO_DM rhgb quiet

在每个示例中，以粗体显示的部分显示，GRUB 必须检测两个 LVM 设备，其名称分别为 "root"、"rootvg"。

### <a name="fix-the-problem"></a>解决问题

如果 LVM 设备不存在，请创建它或从 GRUB 配置文件中删除相应的参数。 然后重试以启用保护。

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Site Recovery 移动服务更新已完成，但出现警告（错误代码151083）

Site Recovery 移动服务包含许多组件，其中一个组件称为筛选器驱动程序。 仅当系统重新启动时，筛选器驱动程序才会加载到系统内存中。 每当移动服务更新包含筛选器驱动程序更改时，计算机就会更新，但仍会看到一些修补程序需要重启的警告。 出现警告，因为筛选器驱动程序修复仅在新的筛选器驱动程序加载时才会生效，这仅在重启过程中发生。

> [!NOTE]
> 这只是一条警告。 即使在新代理更新后，现有复制仍可继续工作。 您可以选择在需要新筛选器驱动程序的优点时重新启动，但旧筛选器驱动程序会在不重新启动的情况下继续运行。
>
> 除了筛选器驱动程序外，移动服务更新中任何其他增强功能和修补程序的优点在无需重新启动的情况下才会生效。  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>无法启用保护，因为在目标资源组中没有预期标记的副本托管磁盘已存在（错误代码150161）

### <a name="possible-cause"></a>可能的原因

如果以前保护了虚拟机，但在禁用复制时未清理副本磁盘，则会出现此问题。

### <a name="fix-the-problem"></a>解决问题

请删除错误消息中标识的副本磁盘，然后重试失败的保护作业。

## <a name="next-steps"></a>后续步骤

[复制 Azure 虚拟机](site-recovery-replicate-azure-to-azure.md)
