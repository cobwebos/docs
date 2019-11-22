---
title: 排查 Azure Site Recovery 中的 Azure VM 复制问题
description: 排查复制 Azure 虚拟机进行灾难恢复时出现的错误。
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: b3c459c0eaac98a1cb704b4346153f77ec974188
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084922"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>排查 Azure 到 Azure VM 复制错误

本文介绍如何排查在 Azure Site Recovery 中将 Azure 虚拟机 (VM) 从一个区域复制和恢复到另一个区域期间出现的常见错误。 有关受支持的配置的详细信息，请参阅[复制 Azure VM 支持矩阵](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 资源配额问题（错误代码 150097）

确保启用订阅，以在计划用作灾难恢复区域的目标区域中创建 Azure VM。 此外，请确保订阅具有足够的配额用于创建特定大小的 VM。 默认情况下，Site Recovery 会选择与源 VM 大小相同的目标 VM 大小。 如果匹配的大小不可用，Site Recovery 会自动选择最接近的可用大小。

如果没有任何大小可支持源 VM 配置，将出现以下消息：

> “无法为虚拟机 <VM 名称> 启用复制。”

### <a name="possible-causes"></a>可能的原因

- 你的订阅 ID 未启用，因此无法在目标区域位置创建任何 VM。
- 你的订阅 ID 未启用或没有足够的配额，因此无法在目标区域位置创建特定大小的 VM。
- 对于目标区域位置中的订阅 ID，找不到与源 VM 网络接口卡 (NIC) 计数 (2) 匹配的适当目标 VM 大小。

### <a name="fix-the-problem"></a>解决问题

联系 [Azure 计费支持](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)启用订阅，以便在目标位置中创建所需大小的 VM。 然后重试失败的操作。

如果目标位置存在容量约束，请禁用到目标位置的复制。 然后在订阅具有足够配额的其他位置启用复制，以创建所需大小的 VM。

## <a name="trusted-root-certificates-error-code-151066"></a>受信任的根证书（错误代码 151066）

如果 VM 上不存在所有最新的受信任根证书，则“启用复制”Site Recovery 作业可能会失败。 如果没有这些证书，VM 发出的 Site Recovery 服务调用的身份验证和授权会失败。 

如果“启用复制”作业失败，将出现以下消息：

> “Site Recovery 配置失败。”

### <a name="possible-cause"></a>可能的原因

虚拟机上不存在用于授权和身份验证的必需受信根证书。

### <a name="fix-the-problem"></a>解决问题

#### <a name="windows"></a>Windows

对于运行 Windows 操作系统的 VM，请安装所有最新的 Windows 更新，以便所有受信任的根证书均存在于计算机上。 按照组织中典型的 Windows 更新管理或证书更新管理过程进行操作，在 VM 上获取最新的根证书和更新的证书吊销列表。

如果处于未联网的环境中，请按照组织中的标准 Windows 更新过程获取证书。 如果 VM 上没有所需的证书，对 Site Recovery 服务的调用会出于安全原因失败。

若要验证问题是否已解决，请从 VM 中的浏览器转到 login.microsoftonline.com。

有关详细信息，请参阅[配置受信任根和不允许的证书](https://technet.microsoft.com/library/dn265983.aspx)。

#### <a name="linux"></a>Linux

按照 Linux 操作系统版本分发商提供的指导，在 VM 上获取最新的受信任根证书和最新的证书吊销列表。

由于 SuSE Linux 使用*符号链接*来维护证书列表，因此请按照以下步骤进行操作：

1. 以根用户身份登录。

1. 运行以下命令来切换目录：

    **# cd /etc/ssl/certs**

1. 检查 Symantec 根 CA 证书是否存在：

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. 如果未找到 Symantec 根 CA 证书，请运行以下命令下载该文件。 检查是否有任何错误，对于网络故障执行建议的操作。

    **# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. 检查 Baltimore 根 CA 证书是否存在：

    **# ls Baltimore_CyberTrust_Root.pem**

1. 如果未找到 Baltimore 根 CA 证书，请运行以下命令下载该证书：

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem**

1. 检查 DigiCert_Global_Root_CA 证书是否存在：

    **# ls DigiCert_Global_Root_CA.pem**

1. 如果未找到 DigiCert_Global_Root_CA，请运行以下命令下载该证书：

    **# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem**

1. 运行 rehash 脚本更新新下载的证书的证书使用者哈希：

    **# c_rehash**

1. 运行以下命令，检查是否为证书创建了使用者哈希作为符号链接：

    - 命令：

        **# ls -l | grep Baltimore**

    - 输出：

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - 命令：

        **# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - 输出：

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - 命令：

        **# ls -l | grep DigiCert_Global_Root**

    - 输出：

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. 使用文件名 b204d74a.0 创建文件 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem 的副本：

    **# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0**

1. 使用文件名 653b494a.0 创建文件 Baltimore_CyberTrust_Root.pem 的副本：

    **# cp Baltimore_CyberTrust_Root.pem 653b494a.0**

1. 使用文件名 3513523f.0 创建文件 DigiCert_Global_Root_CA.pem 的副本：

    **# cp DigiCert_Global_Root_CA.pem 3513523f.0**

1. 检查文件是否存在。

    - 命令：

        **# ls -l 653b494a.0 b204d74a.0 3513523f.0**

    - 输出

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 范围的出站连接（错误代码 151037 或 151072）

要使 Site Recovery 复制正常运行，需要建立从 VM 到特定 URL 或 IP 范围的出站连接。 如果 VM 位于防火墙后或使用网络安全组 (NSG) 规则来控制出站连接，则可能会遇到以下问题之一。

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>问题1：未能将 Azure 虚拟机注册 Site Recovery （错误代码151195）

#### <a name="possible-cause"></a>可能的原因 

由于 DNS 解析失败，无法与 Site Recovery 终结点建立连接。

在重新保护期间，对虚拟机进行故障转移但无法从灾难恢复 (DR) 区域访问 DNS 服务器时经常会发生此问题。

#### <a name="fix-the-problem"></a>解决问题

如果你使用的是自定义 DNS，请确保可以从灾难恢复区域访问 DNS 服务器。 若要检查你是否具有自定义 DNS，请在 VM 上转到“灾难恢复网络” *“DNS 服务器”。*  > 

![自定义 DNS 服务器列表](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

尝试从虚拟机访问 DNS 服务器。 如果该服务器不可访问，请通过对 DNS 服务器进行故障转移或创建 DR 网络与 DNS 之间站点的行来使其可访问。

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>问题2： Site Recovery 配置失败（错误代码151196）

#### <a name="possible-cause"></a>可能的原因

无法与 Office 365 身份验证和标识 IP4 终结点建立连接。

#### <a name="fix-the-problem"></a>解决问题

Site Recovery 需要具有对 Office 365 IP 范围的访问权限来进行身份验证。
如果你使用 Azure NSG 规则或防火墙代理控制 VM 上的出站网络连接，请确保允许到 Office 365 IP 范围的通信。 创建一个基于 [Azure Active Directory (Azure AD) 服务标记](../virtual-network/security-overview.md#service-tags)的 NSG 规则，并允许访问与 Azure AD 对应的所有 IP 地址。 如果将来向 Azure AD 添加新地址，则必须创建新的 NSG 规则。

> [!NOTE]
> 如果 Vm 位于*标准*内部负载均衡器后面，则默认情况下，负载均衡器无权访问 OFFICE 365 IP 范围（即 login.microsoftonline.com）。 请将内部负载均衡器类型更改为 *“基本”*，[配置负载均衡和出站规则](https://aka.ms/lboutboundrulescli) 或根据一文中所述创建出站访问权限。

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>问题3： Site Recovery 配置失败（错误代码151197）

#### <a name="possible-cause"></a>可能的原因

无法与 Site Recovery 服务终结点建立连接。

#### <a name="fix-the-problem"></a>解决问题

Site Recovery 需要根据区域访问 [Site Recovery IP 范围](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)。 请确保可以从虚拟机访问所需的 IP 范围。

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>问题4：当网络流量通过本地代理服务器时，Azure 到 Azure 的复制失败（错误代码151072）

#### <a name="possible-cause"></a>可能的原因

自定义代理设置无效，并且 Site Recovery 移动服务代理未在 Internet Explorer 中自动检测到代理设置。

#### <a name="fix-the-problem"></a>解决问题

移动服务代理通过 Windows 上的 Internet Explorer 和 Linux 上的 /etc/environment 检测代理设置。

如果你只想为移动服务设置代理，可以在位于以下位置的 ProxyInfo.conf 文件中提供代理详细信息：

- **Linux**：/usr/local/InMage/config/
- **Windows**： C:\ProgramData\Microsoft Azure Site Recovery\Config

在 ProxyInfo.conf 中，采用以下初始化文件格式提供代理设置：

> [*proxy*]

> Address= *http://1.2.3.4*

> Port=*567*


> [!NOTE]
> Site Recovery 移动服务代理仅支持未经身份验证的代理。

### <a name="more-information"></a>详细信息

若要指定[所需的 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 或[所需的 IP 范围](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)，请遵循[关于 Azure 到 Azure 复制中的网络](site-recovery-azure-to-azure-networking-guidance.md)中的指导。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>在计算机中找不到磁盘（错误代码 150039）

必须初始化附加到 VM 的新磁盘。 如果找不到该磁盘，将显示以下消息：

> “逻辑单元号 *LUN* 为 *LUNValue* 的 Azure 数据磁盘 *DiskName* *DiskURI* 未映射到具有相同 LUN 值的 VM 报告的相应磁盘。”

### <a name="possible-causes"></a>可能的原因

- 新的数据磁盘已附加到 VM，但未初始化。
- VM 中的数据磁盘未正确报告附加到 VM 的磁盘的逻辑单元号 (LUN) 值。

### <a name="fix-the-problem"></a>解决问题

确保数据磁盘已初始化，然后重试该操作。

- **Windows**：[附加并初始化新磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。

- **Linux**：[在 linux 中初始化新的数据磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

如果问题持续出现，请联系支持人员。

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>一个或多个磁盘可用于保护（错误代码 153039）

### <a name="possible-causes"></a>可能的原因

- 最近在保护后将一个或多个磁盘添加到虚拟机。
- 在保护虚拟机之后初始化了一个或多个磁盘。

### <a name="fix-the-problem"></a>解决问题

若要使 VM 的复制状态再次恢复正常，可以选择保护磁盘或消除警告。

#### <a name="to-protect-the-disks"></a>保护磁盘

1. 转到“复制的项”**VM 名称**“磁盘”。 >  > 
1. 选择未受保护的磁盘，然后选择“启用复制”：

    ![在 VM 磁盘上启用复制](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>消除警告

1. 转到“复制的项” **VM 名称。**  > 
1. 选择“概述”部分选择警告，然后选择“确定”。

    ![消除新磁盘警告](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>已完成从保管库删除虚拟机的操作，但出现错误信息（错误代码 150225）

在保护虚拟机时，Site Recovery 会在源虚拟机上创建一些链接。 去除保护或禁用复制时，Site Recovery 会在完成清理作业的过程中删除这些链接。 如果虚拟机存在资源锁，清理作业将会完成但会显示一些信息。 该信息指出，虚拟机已从恢复服务保管库中删除，但某些过期链接无法从源计算机中清理。

如果你今后不打算再次保护此虚拟机，可以忽略此警告。 但是，如果你今后需要保护此虚拟机，请遵循“解决问题”下的步骤清理这些链接。

> [!WARNING]
> 如果不执行清理：
>
> - 通过恢复服务保管库启用复制时，不会列出虚拟机。
> - 如果尝试使用“虚拟机” **“设置”** “灾难恢复”来保护 VM，该操作将会失败并出现消息“无法启用复制，因为 VM 上存在过期的资源链接”。 >  > 

### <a name="fix-the-problem"></a>解决问题

> [!NOTE]
> 在执行以下步骤时，Site Recovery 不会删除源虚拟机或以任何方式影响它。

1. 删除 VM 或 VM 资源组的锁。 例如，在下图中，必须删除名为“MoveDemo”的 VM 上的资源锁：

    ![从 VM 中删除锁](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 下载用于[删除过时的 Site Recovery 配置](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)的脚本。
1. 运行名为 Cleanup-stale-asr-config-Azure-VM.ps1 的脚本。 提供订阅 ID、VM 资源组和 VM 名称作为参数。
1. 如果系统要求提供 Azure 凭据，请提供这些凭据。 然后验证该脚本是否正常运行，而不会出现任何失败。

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>由于 VM 上存在过时的资源链接，无法启用复制（错误代码 150226）

### <a name="possible-cause"></a>可能的原因

虚拟机上存在以前的 Site Recovery 保护中使用的过时配置。

如果你使用 Site Recovery 为 Azure VM 启用了复制，然后执行了以下操作，则 Azure VM 上可能会出现过时的配置：

- 禁用了复制，但源 VM 存在资源锁。
- 在未显式禁用 VM 上的复制的情况下删除了 Site Recovery 保管库。
- 在未显式禁用 VM 上的复制的情况下删除了包含 Site Recovery 保管库的资源组。

### <a name="fix-the-problem"></a>解决问题

> [!NOTE]
> 在执行以下步骤时，Site Recovery 不会删除源虚拟机或以任何方式影响它。

1. 删除 VM 或 VM 资源组的锁。 例如，在下图中，必须删除名为“MoveDemo”的 VM 上的资源锁：

    ![从 VM 中删除锁](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 下载用于[删除过时的 Site Recovery 配置](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)的脚本。
1. 运行名为 Cleanup-stale-asr-config-Azure-VM.ps1 的脚本。 提供订阅 ID、VM 资源组和 VM 名称作为参数。
1. 如果系统要求提供 Azure 凭据，请提供这些凭据。 然后验证该脚本是否正常运行，而不会出现任何失败。

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>无法在“启用复制”作业中看到要选择的 Azure VM 或资源组

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>原因1：资源组和源虚拟机位于不同的位置

Site Recovery 当前要求源区域资源组和虚拟机应位于同一位置。 否则，在尝试应用保护时将无法找到虚拟机或资源组。

一种解决方法是，从 VM 而不是从恢复服务保管库启用复制。 转到“源 VM” **“属性”** “灾难恢复”并启用复制。 >  > 

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>原因2：资源组不属于所选订阅

如果资源组不是给定订阅的一部分，则可能无法在保护时找到该资源组。 确保该资源组属于正在使用的订阅。

### <a name="cause-3-stale-configuration"></a>原因3：过时配置

如果 Azure VM 上留有过时的 Site Recovery 配置，你可能看不到要为其启用复制的 VM。 如果你使用 Site Recovery 为 Azure VM 启用了复制，然后执行了以下操作，则可能会出现这种情况：

- 在未显式禁用 VM 上的复制的情况下删除了 Site Recovery 保管库。
- 在未显式禁用 VM 上的复制的情况下删除了包含 Site Recovery 保管库的资源组。
- 禁用了复制，但源 VM 存在资源锁。

### <a name="fix-the-problem"></a>解决问题

> [!NOTE]
> 请确保在使用本部分所述的脚本之前更新“AzureRM.Resources”模块。  在执行以下步骤时，Site Recovery 不会删除源虚拟机或以任何方式影响它。

1. 删除 VM 或 VM 资源组中的锁（如果有）。 例如，在下图中，必须删除名为“MoveDemo”的 VM 上的资源锁：

    ![从 VM 中删除锁](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 下载用于[删除过时的 Site Recovery 配置](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)的脚本。
1. 运行名为 Cleanup-stale-asr-config-Azure-VM.ps1 的脚本。 提供订阅 ID、VM 资源组和 VM 名称作为参数。
1. 如果系统要求提供 Azure 凭据，请提供这些凭据。 然后验证该脚本是否正常运行，而不会出现任何失败。

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>无法选择虚拟机进行保护

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>原因1：虚拟机安装的扩展处于失败或无响应状态

转到“虚拟机” **“设置”** “扩展”，并检查是否有任何扩展处于失败状态。 >  >  卸载所有失败的扩展，然后重试保护虚拟机。

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>原因2： VM 的预配状态无效

参阅本文稍后的 [VM 的预配状态无效](#the-vms-provisioning-state-is-not-valid-error-code-150019)中的故障排除步骤。

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>VM 的预配状态无效（错误代码 150019）

若要在 VM 上启用复制，预配状态必须是“成功”。 遵循以下步骤检查预配状态：

1. 在 Azure 门户中，从“所有服务”中选择“资源浏览器”。
1. 展开“订阅”列表并选择你的订阅。
1. 展开 **ResourceGroups** 列表并选择 VM 的资源组。
1. 展开“资源”列表并选择你的 VM。
1. 在右侧的实例视图中检查“预配状态”字段。

### <a name="fix-the-problem"></a>解决问题

- 如果 **provisioningState** 是“失败”，请联系支持人员并提供详细信息，以便进行故障排除。
- 如果“预配状态”为“正在更新”，则可能是正在部署另一扩展。 检查 VM 上是否有任何正在进行的操作，等待它们完成，然后重试失败的 Site Recovery“启用复制”作业。

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>无法选择目标 VM（网络选择选项卡不可用）

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>原因1：你的 VM 已连接到已映射到目标网络的网络

如果源 VM 在某个虚拟网络中，并且同一虚拟网络中的另一个 VM 已映射到目标资源组中的某个网络，则网络选择下拉列表框默认将不可用（灰显）。

![网络选择列表不可用](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>原因2：以前使用 Site Recovery 保护了 VM，然后禁用了复制

禁用 VM 复制不会删除网络映射。 必须从保护 VM 的恢复服务保管库中删除映射。 转到“恢复服务保管库” *“Site Recovery 基础结构”* “网络映射” >  > 。

![删除网络映射](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

保护 VM 之后，可以在完成初始设置后更改灾难恢复设置期间配置的目标网络。

![修改网络映射](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

请注意，更改网络映射会影响使用该同一网络映射的所有受保护 VM。

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ 或卷影复制服务错误（错误代码 151025）

发生此错误时，将显示以下消息：

> “Site Recovery 扩展安装失败”

### <a name="possible-causes"></a>可能的原因

- 禁用了 COM+ 系统应用程序服务。
- 禁用了卷影复制服务。

### <a name="fix-the-problem"></a>解决问题

将 COM+ 系统应用程序和卷影复制服务设置为自动或手动启动模式。

1. 在 Windows 中打开“服务”控制台。
1. 确保 COM+ 系统应用程序和卷影复制服务的“启动类型”未设置为“已禁用”。

    ![检查 COM+ 系统应用程序和卷影复制服务的启动类型](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>不支持的托管磁盘大小（错误代码 150172）

发生此错误时，将显示以下消息：

> “无法为虚拟机启用保护，因为它的磁盘 *DiskName* 的大小为 *DiskSize**，小于所支持的最小大小 1024 MB。”

### <a name="possible-cause"></a>可能的原因

磁盘小于支持的大小 (1024 MB)。

### <a name="fix-the-problem"></a>解决问题

确保磁盘大小在支持的大小范围内，然后重试该操作。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>未启用保护，因为 GRUB 配置包含设备名称，而不是 UUID （错误代码151126）

### <a name="possible-cause"></a>可能的原因

Linux GRUB 配置文件（/boot/grub/menu.lst、/boot/grub/grub.cfg、/boot/grub2/grub.cfg 或 /etc/default/grub）可能为 *root* 和 *resume* 参数指定了实际设备名称而不是 UUID 值。 Site Recovery 需要 UUID，因为设备名称可能会更改。 重启后，VM 在故障转移时可能不会使用相同的名称，从而导致出现问题。

以下示例摘自 GRUB 文件的代码行，其中显示了设备名称（以粗体显示）而不是所需的 UUID：

- File /boot/grub2/grub.cfg

  > linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts

- File: /boot/grub/menu.lst

  > kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314


### <a name="fix-the-problem"></a>解决问题

将每个设备名称替换为相应的 UUID：

1. 通过执行命令 **blkid** <***设备名称***> 查找设备的 UUID。 例如：

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 将设备名称替换为其 UUID，格式为 **root=UUID**=*UUID* 和 **resume=UUID**=*UUID*。 例如，在替换后，/boot/grub/menu.lst 中的代码行（前面已讨论）如下所示：

    > kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314

1. 重试保护。

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>由于 GRUB 配置中所述的设备不存在，启用保护失败（错误代码 151124）

### <a name="possible-cause"></a>可能的原因

GRUB 配置文件（/boot/grub/menu.lst、/boot/grub/grub.cfg、/boot/grub2/grub.cfg 或 /etc/default/grub）可能包含参数 *rd.lvm.lv* 或 *rd_LVM_LV*。 这些参数指定了在启动时要发现的逻辑卷管理器 (LVM) 设备。 如果这些 LVM 设备不存在，则受保护的系统本身不会启动，而是停滞在启动过程。 故障转移 VM 上也会出现相同的问题。 以下是几个示例：

- File: /boot/grub2/grub.cfg on RHEL7:

    > linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US.UTF-8

- File: /etc/default/grub on RHEL7:

    > GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"

- File: /boot/grub/menu.lst on RHEL6:

    > kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto **rd_LVM_LV=rootvg/lv_root**  KEYBOARDTYPE=pc KEYTABLE=us **rd_LVM_LV=rootvg/lv_swap** rd_NO_DM rhgb quiet

在每个示例中，以粗体显示的部分指明，GRUB 必须在卷组“rootvg”中检测到名为“root”和“swap”的两个 LVM 设备。

### <a name="fix-the-problem"></a>解决问题

如果 LVM 设备不存在，请创建该设备，或者从 GRUB 配置文件中删除该设备对应的参数。 然后重试启用保护。

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Site Recovery 移动服务更新已完成但出现警告（错误代码 151083）

Site Recovery 移动服务有多个组件，其中一个称为筛选器驱动程序。 筛选器驱动程序只有在系统重启期间才会加载到系统内存中。 每当移动服务更新涉及到筛选器驱动程序的更改时，计算机才会更新，但仍会出现警告，指出某些修复措施需要重启。 之所以出现该警告，是因为仅当已加载新的筛选器驱动程序（只会在重启期间发生）时，筛选器驱动程序修复措施才会生效。

> [!NOTE]
> 这只是一条警告。 即使在新代理更新后，现有的复制也能继续正常工作。 可以选择每当需要获得新筛选器驱动程序的优势时才重启，但如果不重启，旧的筛选器驱动程序也能保持正常工作。
>
> 除了筛选器驱动程序以外，无需重启，移动服务更新中其他任何增强和修复的优势也能生效。  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>由于副本托管磁盘已存在，但目标资源组中不包含预期的标记，因此无法启用保护（错误代码 150161）

### <a name="possible-cause"></a>可能的原因

如果虚拟机过去受保护，但禁用复制时未清理副本磁盘，则可能会出现此问题。

### <a name="fix-the-problem"></a>解决问题

删除错误消息中指出的副本磁盘，然后重试失败的保护作业。

## <a name="next-steps"></a>后续步骤

[复制 Azure 虚拟机](site-recovery-replicate-azure-to-azure.md)
