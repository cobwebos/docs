---
title: Azure 到 Azure 复制问题和错误的 Azure Site Recovery 故障排除 | Microsoft Docs
description: 解决复制 Azure 虚拟机进行灾难恢复时出现的错误和问题
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sujayt
ms.openlocfilehash: 44801663165b85edc988dab8ae2b668ef0e613b7
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54381585"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Azure 到 Azure VM 复制问题故障排除

本文介绍将 Azure 虚拟机从一个区域复制和恢复到另一个区域时 Azure Site Recovery 中出现的常见问题，并说明如何解决这些问题。 有关受支持的配置的详细信息，请参阅[复制 Azure VM 支持矩阵](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 资源配额问题（错误代码 150097）
应启用订阅，以在计划用作灾难恢复区域的目标区域中创建 Azure VM。 此外，订阅还应拥有创建特定大小的 VM 所需的足够配额。 默认情况下，Site Recovery 为目标 VM 选取与源 VM 相同的大小。 如果匹配大小不可用，则会自动选取最接近的大小。 如果没有支持源 VM 配置的匹配大小，系统会显示以下错误消息：

**错误代码** | **可能的原因** | **建议**
--- | --- | ---
150097<br></br>**消息**：Replication couldn't be enabled for the virtual machine VmName.（无法为虚拟机 VmName 启用复制。） | - 订阅 ID 可能未启用，无法在目标区域位置创建任何 VM。</br></br>- 订阅 ID 可能未启用，或没有足够的配额来在目标区域位置创建特定 VM 大小。</br></br>- 未在目标区域位置为订阅 ID 找到匹配源 VM NIC 计数 (2) 的合适目标 VM 大小。| 请联系 [Azure 计费支持人员](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)，为订阅启用在目标位置创建所需 VM 大小的选项。 启用后，重试失败的操作。

### <a name="fix-the-problem"></a>解决问题
可联系 [Azure 计费支持人员](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)，让订阅能够在目标位置创建所需大小的 VM。

如果目标位置存在容量限制，则禁用复制，并在订阅拥有足够配额，能够创建所需大小的 VM 的其他位置启用复制。

## <a name="trusted-root-certificates-error-code-151066"></a>受信任的根证书（错误代码 151066）

如果 VM 上没有所有最新的受信任根证书，则“启用复制”作业可能会失败。 如果没有证书，来自 VM 的 Site Recovery 服务调用的身份验证和授权会失败。 系统会显示失败的“启用复制”Site Recovery 作业的错误消息：

**错误代码** | **可能的原因** | **建议**
--- | --- | ---
151066<br></br>**消息**：Site Recovery 配置失败。 | 计算机上没有授权和身份验证所需的受信任的根证书。 | - 对于运行 Windows 操作系统的 VM，请确保虚拟机上存在受信任的根证书。 有关信息，请参阅[配置受信任的根和不允许的证书](https://technet.microsoft.com/library/dn265983.aspx)。<br></br>- 对于运行 Linux 操作系统的 VM，请按照 Linux 操作系统版本分销商发布的受信任根证书指南操作。

### <a name="fix-the-problem"></a>解决问题
**Windows**

在 VM 上安装所有最新的 Windows 更新，让虚拟机拥有所有受信任的根证书。 如果处于未联网的环境中，请按照组织中的标准 Windows 更新过程获取证书。 如果 VM 上没有所需的证书，对 Site Recovery 服务的调用会出于安全原因失败。

按照组织中的典型 Windows 更新管理或证书更新管理过程，在 VM 上获取所有最新的根证书和更新的证书吊销列表。

若要验证问题是否已解决，请从 VM 中的浏览器转到 login.microsoftonline.com。

**Linux**

按照 Linux 分销商提供的指南，在 VM 上获取最新的受信任根证书和最新的证书吊销列表。

由于 SuSE Linux 使用符号链接来维护证书列表，请执行以下步骤：

1.  以根用户身份登录。

2.  运行此命令以更改目录。

      ``# cd /etc/ssl/certs``

1. 检查 Symantec 根 CA 证书是否存在。

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. 如果未找到 Symantec 根 CA 证书，则运行以下命令下载文件。 检查是否有任何错误，对于网络故障执行建议的操作。

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. 检查 Baltimore 根 CA 证书是否存在。

      ``# ls Baltimore_CyberTrust_Root.pem``

4. 如果未找到 Baltimore 根 CA 证书，则下载该证书。  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. 检查 DigiCert_Global_Root_CA 证书是否存在。

    ``# ls DigiCert_Global_Root_CA.pem``

6. 如果未找到 DigiCert_Global_Root_CA，则运行以下命令下载该证书。

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. 运行 rehash 脚本更新新下载的证书的证书使用者哈希。

    ``# c_rehash``

8.  检查是否已为证书创建使用者哈希作为符号链接。

    - 命令

      ``# ls -l | grep Baltimore``

    - 输出

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - 命令

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - 输出

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - 命令

      ``# ls -l | grep DigiCert_Global_Root``

    - 输出

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  使用文件名 b204d74a.0 创建文件 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem 的副本

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. 使用文件名 653b494a.0 创建文件 Baltimore_CyberTrust_Root.pem 的副本

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. 使用文件名 3513523f.0 创建文件 DigiCert_Global_Root_CA.pem 的副本

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. 检查文件是否存在。  

    - 命令

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - 输出

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 范围的出站连接（错误代码 151037 或 151072）

若要 Site Recovery 复制正常运行，需要从 VM 到特定 URL 或 IP 范围的出站连接。 如果 VM 位于防火墙后或使用网络安全组 (NSG) 规则来控制出站连接，则可能会遇到以下问题之一。

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>问题 1：未能向 Site Recovery 注册 Azure 虚拟机 (151195) </br>
- **可能的原因** </br>
  - 由于 DNS 解析失败而无法建立到 Site Recovery 终结点的连接。
  - 在重新保护期间，对虚拟机进行故障转移但无法从 DR 区域访问 DNS 服务器时经常会出现此问题。
  
- **解决方法**
   - 如果你使用的是自定义 DNS，请确保可以从灾难恢复区域访问 DNS 服务器。 若要检查你是否具有自定义 DNS，请转到“VM”>“灾难恢复网络”>“DNS 服务器”。 尝试从虚拟机访问 DNS 服务器。 如果它无法访问，请通过对 DNS 服务器进行故障转移或创建 DR 网络与 DNS 之间站点的行来使其可访问。
  
    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)
 

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>问题 2：Site Recovery 配置失败 (151196)
- **可能的原因** </br>
  - 无法建立到 Office 365 身份验证和标识 IP4 终结点的连接。

- **解决方法**
  - Azure Site Recovery 需要具有对 Office 365 IP 范围的访问权限来进行身份验证。
    如果你使用 Azure 网络安全组 (NSG) 规则/防火墙代理控制 VM 上的出站网络连接，请确保允许到 O365 IP 范围的通信。 创建一个基于 [Azure Active Directory (AAD) 服务标记](../virtual-network/security-overview.md#service-tags)的 NSG 规则以允许访问与 AAD 对应的所有 IP 地址
        - 如果将来要向 Azure Active Directory (AAD) 添加新地址，则需要创建新的 NSG 规则。


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>问题 3：Site Recovery 配置失败 (151197)
- **可能的原因** </br>
  - 无法建立到 Azure Site Recovery 服务终结点的连接。

- **解决方法**
  - Azure Site Recovery 需要根据区域访问 [Site Recovery IP 范围](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)。 请确保可以从虚拟机访问所需的 IP 范围。
    

### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>问题 4：当网络流量通过本地代理服务器时 A2A 复制失败 (151072)
 - **可能的原因** </br>
   - 自定义代理设置无效，并且 ASR 移动服务代理未在 IE 中自动检测到代理设置


 - **解决方法**
   1.   移动服务代理通过 Windows 上的 IE 和 Linux 上的 /etc/environment 检测代理设置。
   2.  如果只想对 ASR 移动服务设置代理，可在位于以下路径的 ProxyInfo.conf 中提供代理详细信息：</br>
       - ***Linux*** 上的 ``/usr/local/InMage/config/``
       - ***Windows*** 上的 ``C:\ProgramData\Microsoft Azure Site Recovery\Config``
   3.   ProxyInfo.conf 应包含采用以下 INI 格式的代理设置。</br>
                   *[proxy]*</br>
                   *Address=http://1.2.3.4*</br>
                   *Port=567*</br>
   4. ASR 移动服务代理仅支持***未经身份验证的代理***。
 

### <a name="fix-the-problem"></a>解决问题
若要将[所需的 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 或[所需的 IP 范围](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)加入允许列表，请按照[网络指南文档](site-recovery-azure-to-azure-networking-guidance.md)中的步骤执行操作。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>在计算机中找不到磁盘（错误代码 150039）

必须初始化附加到 VM 的新磁盘。

**错误代码** | **可能的原因** | **建议**
--- | --- | ---
150039<br></br>**消息**：Azure data disk (DiskName) (DiskURI) with logical unit number (LUN) (LUNValue) was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.（具有逻辑单元号 (LUN) (LUNValue) 的 Azure 数据磁盘 (DiskName) (DiskURI) 未映射到具有相同 LUN 值的 VM 报告的相应磁盘。） | - 新数据磁盘已附加到 VM，但该磁盘未初始化。</br></br>- VM 内的数据磁盘未正确报告磁盘附加到 VM 时的 LUN 值。| 请确保数据磁盘已初始化，然后重试操作。</br></br>对于 Windows：[附加并初始化新的磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。</br></br>对于 Linux：[在 Linux 中初始化新的数据磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

### <a name="fix-the-problem"></a>解决问题
请确保数据磁盘已初始化，然后重试操作：

- 对于 Windows：[附加并初始化新的磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。
- 对于 Linux：[在 Linux 中添加新数据磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

如果问题持续出现，请联系支持人员。


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>在“启用复制”选项中看不到 Azure VM

 **原因 1：资源组和源虚拟机位于不同的位置** <br>
Azure Site Recovery 当前强制要求源区域资源组和虚拟机应位于同一位置。 如果不是这种情况，那么在保护期间将无法找到虚拟机。

**原因 2：资源组不是所选订阅的一部分** <br>
如果资源组不是给定订阅的一部分，则可能无法在保护期间找到该资源组。 确保资源组属于正在使用的订阅。

 **原因 3：过时配置** <br>
如果看不到要为其启用复制的虚拟机，可能是因为有过时的 Site Recovery 配置保留在 Azure VM 中。 在以下情况中，过时配置可能会留在 Azure VM 上：

- 使用 Site Recovery 为 Azure VM 启用复制，然后删除 Site Recovery 保管库，而不在 VM 上明确禁用复制。
- 使用 Site Recovery 为 Azure VM 启用复制，然后删除包含 Site Recovery 保管库的资源组，而不在 VM 上明确禁用复制。

### <a name="fix-the-problem"></a>解决问题

>[!NOTE] 
>
>请确保在使用以下脚本之前更新“AzureRM.Resources”模块。 

可使用[删除过时 ASR 配置脚本](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412)，删除 Azure VM 上的过时 Site Recovery 配置。 删除过时配置后，应能够看到该 VM。

## <a name="unable-to-select-virtual-machine-for-protection"></a>无法选择虚拟机进行保护 
 **原因 1：虚拟机安装的某些扩展处于失败或无响应状态** <br>
 转到“虚拟机”>“设置”>“扩展”，并检查是否存在任何失败状态的扩展。 卸载失败的扩展，然后重试保护虚拟机。<br>
 **原因 2：[VM 的预配状态无效](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>VM 的预配状态无效（错误代码 150019）

若要在 VM 上启用复制，预配状态应为“已成功”。 可以通过执行以下步骤来检查 VM 状态。

1.  从 Azure 门户的“所有服务”中选择“资源浏览器”。
2.  展开“订阅”列表并选择你的订阅。
3.  展开 **ResourceGroups** 列表并选择 VM 的资源组。
4.  展开“资源”列表并选择你的虚拟机
5.  在右侧的“实例”视图中检查 **provisioningState** 字段。

### <a name="fix-the-problem"></a>解决问题

- 如果 **provisioningState** 是“失败”，请联系支持人员并提供详细信息，以便进行故障排除。
- 如果 **provisioningState** 是“正在更新”，可以部署其他扩展。 检查 VM 上是否有任何正在进行的操作，等待这些操作完成，然后重试失败的 Site Recovery 的“启用复制”作业。

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>无法选择目标虚拟网络 - 网络选择选项卡灰显。

**原因 1：VM 附加到了已映射至“目标网络”的网络。**
- 如果源 VM 在某个虚拟网络中，并且同一虚拟网络中的另一个 VM 已映射到目标资源组中的某个网络，则默认将禁用网络选择下拉列表。

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**原因 2：之前已使用 Azure Site Recovery 保护了 VM，并禁用了复制。**
 - 禁用 VM 复制不会删除网络映射。 必须从保护 VM 的恢复服务保管库中删除网络映射。 </br>
 导航到恢复服务保管库并选择“Site Recovery 基础结构”>“网络映射”。 </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - 保护 VM 之后，可以在完成初始设置后更改灾难恢复设置期间配置的目标网络。 </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - 请注意，更改网络映射会影响使用该特定网络映射的所有受保护 VM。


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM+/卷影复制服务错误（错误代码 151025）
**错误代码** | **可能的原因** | **建议**
--- | --- | ---
151025<br></br>**消息**：Site Recovery 扩展安装失败 | - 禁用了“COM + 系统应用程序”服务。</br></br>- 禁用了“卷影复制”服务。| 将“COM + 系统应用程序”和“卷影复制”服务设置为自动或手动启动模式。

### <a name="fix-the-problem"></a>解决问题

可以打开“服务”控制台并确保“COM + 系统应用程序”和“卷影复制”的“启动类型”未设置为“已禁用”。
  ![com-error](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>不支持的托管磁盘大小（错误代码 150172）


**错误代码** | **可能的原因** | **建议**
--- | --- | ---
150172<br></br>**消息**：无法为虚拟机启用保护，因为它具有 (DiskName)，其大小 (DiskSize) 小于最小支持的大小 10 GB。 | - 磁盘小于支持的大小 (1024 MB)| 请确保磁盘大小在支持的大小范围内，然后重试该操作。 

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>启用保护失败，因为 GRUB 配置中提到的设备名不是 UUID（错误代码 151126）

**可能的原因：** </br>
GRUB 配置文件（“/boot/grub/menu.lst”、“/boot/grub/grub.cfg”、“/boot/grub2/grub.cfg”或“/etc/default/grub”）可能包含参数“root”和“resume”的值作为实际设备名而非 UUID。 Site Recovery 要求 UUID 方法，因为设备名可能会在 VM 重启时发生更改，由于故障转移时 VM 可能不会出现相同的名称，从而导致问题。 例如： </br>


- 以下行来自 GRUB 文件 /boot/grub2/grub.cfg。 <br>
*linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- 以下行来自 GRUB 文件 /boot/grub/menu.lst
*kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

如果发现上面的粗体字符串，GRUB 具有参数“root”和“resume”的实际设备名，而不是 UUID。
 
**如何修复：**<br>
设备名应替换为相应的 UUID。<br>


1. 执行命令“blkid <device name>”找到设备的 UUID。 例如：<br>
```
blkid /dev/sda1 
```<br>
```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
```blkid /dev/sda2```<br> 
```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
```<br>



1. Now replace the device name with its UUID in the format like "root=UUID=<UUID>". For example, if we replace the device names with UUID for root and resume parameter mentioned above in the files "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub: then the lines in the files looks like. <br>
*kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*


## Next steps
[Replicate Azure virtual machines](site-recovery-replicate-azure-to-azure.md)
