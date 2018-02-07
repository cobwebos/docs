---
title: "Azure 到 Azure 复制问题和错误的 Azure Site Recovery 故障排除 | Microsoft Docs"
description: "解决复制 Azure 虚拟机进行灾难恢复时出现的错误和问题"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: sujayt
ms.openlocfilehash: 9e5719cd81408f6732826c90505a3ce8aa10f8ed
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2018
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
151066<br></br>**消息**：Site Recovery configuration failed.（Site Recovery 配置失败。） | 计算机上没有授权和身份验证所需的受信任的根证书。 | - 对于运行 Windows 操作系统的 VM，请确保虚拟机上存在受信任的根证书。 有关信息，请参阅[配置受信任的根和不允许的证书](https://technet.microsoft.com/library/dn265983.aspx)。<br></br>- 对于运行 Linux 操作系统的 VM，请按照 Linux 操作系统版本分销商发布的受信任根证书指南操作。

### <a name="fix-the-problem"></a>解决问题
**Windows**

在 VM 上安装所有最新的 Windows 更新，让虚拟机拥有所有受信任的根证书。 如果处于未联网的环境中，请按照组织中的标准 Windows 更新过程获取证书。 如果 VM 上没有所需的证书，对 Site Recovery 服务的调用会出于安全原因失败。

按照组织中的典型 Windows 更新管理或证书更新管理过程，在 VM 上获取所有最新的根证书和更新的证书吊销列表。

若要验证问题是否已解决，请从 VM 中的浏览器转到 login.microsoftonline.com。

**Linux**

按照 Linux 分销商提供的指南，在 VM 上获取最新的受信任根证书和最新的证书吊销列表。

由于 SuSE Linux 使用符号链接来维护证书列表，请执行以下步骤：

1.  以根用户身份登录。

2.  运行以下命令：

      ``# cd /etc/ssl/certs``

3.  若要查看是否存在 Symantec 根 CA 证书，请运行以下命令：

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4.  如果找不到文件，请运行以下命令：

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

      ``# c_rehash``

5.  若要使用 b204d74a.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem 创建符号链接，请运行以下命令：

      ``# ln -s  VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

6.  检查此命令是否拥有以下输出。 如果没有，则需要创建符号链接：

      ``# ls -l | grep Baltimore
      -rw-r--r-- 1 root root   1303 Apr  7  2016 Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 04:47 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 05:01 653b494a.0 -> Baltimore_CyberTrust_Root.pem``

7. 如果符号链接 653b494a.0 不存在，请使用以下命令创建符号链接：

      ``# ln -s Baltimore_CyberTrust_Root.pem 653b494a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 范围的出站连接（错误代码 151037 或 151072）

若要 Site Recovery 复制正常运行，需要从 VM 到特定 URL 或 IP 范围的出站连接。 如果 VM 位于防火墙后或使用网络安全组 (NSG) 规则来控制出站连接，则可能会看到以下错误消息之一：

**错误代码** | **可能的原因** | **建议**
--- | --- | ---
151037<br></br>**消息**：Failed to register Azure virtual machine with Site Recovery.（未能向 Site Recovery 注册 Azure 虚拟机。） | - 使用 NSG 来控制 VM 的出站访问，且未将所需的 IP 范围加入出站访问允许列表。</br></br>- 使用第三方防火墙工具，且未将所需的 IP 范围/URL 加入允许列表。</br>| - 如果使用防火墙代理来控制 VM 的出站网络连接，请确保已将必备 URL 或数据中心 IP 范围加入白名单。 有关信息，请参阅[防火墙代理指南](https://aka.ms/a2a-firewall-proxy-guidance)。</br></br>- 如果使用 NSG 规则来控制 VM 的出站网络连接，请确保已将必备数据中心 IP 范围加入允许列表。 有关信息，请参阅[网络安全组指南](https://aka.ms/a2a-nsg-guidance)。
151072<br></br>**消息**：Site Recovery configuration failed.（Site Recovery 配置失败。） | 无法建立到 Site Recovery 服务终结点的连接。 | - 如果使用防火墙代理来控制 VM 的出站网络连接，请确保已将必备 URL 或数据中心 IP 范围加入白名单。 有关信息，请参阅[防火墙代理指南](https://aka.ms/a2a-firewall-proxy-guidance)。</br></br>- 如果使用 NSG 规则来控制 VM 的出站网络连接，请确保已将必备数据中心 IP 范围加入允许列表。 有关信息，请参阅[网络安全组指南](https://aka.ms/a2a-nsg-guidance)。

### <a name="fix-the-problem"></a>解决问题
若要将[所需的 URL](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-urls) 或[所需的 IP 范围](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-ip-ranges)加入允许列表，请按照[网络指南文档](site-recovery-azure-to-azure-networking-guidance.md)中的步骤执行操作。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>在计算机中找不到磁盘（错误代码 150039）

必须初始化附加到 VM 的新磁盘。

**错误代码** | **可能的原因** | **建议**
--- | --- | ---
150039<br></br>**消息**：Azure data disk (DiskName) (DiskURI) with logical unit number (LUN) (LUNValue) was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.（具有逻辑单元号 [LUN] [LUNValue] 的 Azure 数据磁盘 [DiskName] [DiskURI] 未映射到具有相同 LUN 值的 VM 报告的相应磁盘。） | - 新数据磁盘已附加到 VM，但该磁盘未初始化。</br></br>- VM 内的数据磁盘未正确报告磁盘附加到 VM 时的 LUN 值。| 请确保数据磁盘已初始化，然后重试操作。</br></br>对于 Windows：[附加并初始化新磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。</br></br>对于 Linux：[在 Linux 中初始化新数据磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

### <a name="fix-the-problem"></a>解决问题
请确保数据磁盘已初始化，然后重试操作：

- 对于 Windows：[附加并初始化新磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。
- 对于 Linux：[在 Linux 中添加新数据磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

如果问题持续出现，请联系支持人员。


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>在“启用复制”选项中看不到 Azure VM

在[启用复制：步骤 2](./site-recovery-azure-to-azure.md#step-2-select-virtual-machines) 的选项中可能看不到 Azure VM。 此问题可能由留在 Azure VM 上的过时 Site Recovery 配置导致。 在以下情况中，过时配置可能会留在 Azure VM 上：

- 使用 Site Recovery 为 Azure VM 启用复制，然后删除 Site Recovery 保管库，而不在 VM 上明确禁用复制。
- 使用 Site Recovery 为 Azure VM 启用复制，然后删除包含 Site Recovery 保管库的资源组，而不在 VM 上明确禁用复制。

### <a name="fix-the-problem"></a>解决问题

可使用[删除过时 ASR 配置脚本](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412)，删除 Azure VM 上的过时 Site Recovery 配置。 删除过时配置后，应在[启用复制：步骤 2](./site-recovery-azure-to-azure.md#step-2-select-virtual-machines) 中看到 VM。


## <a name="next-steps"></a>后续步骤
[复制 Azure 虚拟机](site-recovery-replicate-azure-to-azure.md)
