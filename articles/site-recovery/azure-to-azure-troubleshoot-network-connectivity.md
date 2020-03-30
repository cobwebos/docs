---
title: 使用 Azure 站点恢复解决 Azure 与 Azure 灾难恢复的连接问题
description: 解决 Azure VM 灾难恢复中的连接问题
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 08/05/2019
ms.openlocfilehash: b082e1aca094dcb335a7268e4c116376d756fd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292029"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>排查 Azure 到 Azure VM 网络连接性问题

本文介绍了在不同区域之间复制和恢复 Azure 虚拟机时与网络连接相关的常见问题。 有关网络要求的详细信息，请参阅[复制 Azure VM 的连接性要求](azure-to-azure-about-networking.md)。

若要 Site Recovery 复制正常运行，需要从 VM 到特定 URL 或 IP 范围的出站连接。 如果 VM 位于防火墙后或使用网络安全组 (NSG) 规则来控制出站连接，则可能会遇到以下问题之一。

**Url** | **详细信息**  
--- | ---
* .blob.core.windows.net | 必需，以便从 VM 将数据写入到源区域中的缓存存储帐户。 如果您知道 VM 的所有缓存存储帐户，则可以允许列出特定的存储帐户 URL（例如，cache1.blob.core.windows.net和cache2.blob.core.windows.net），而不是 *.blob.core.windows.net
login.microsoftonline.com | 必需，用于向 Site Recovery 服务 URL 进行授权和身份验证。
*.hypervrecoverymanager.windowsazure.com | 必需，以便从 VM 进行 Site Recovery 服务通信。 如果防火墙代理支持 IP，则可以使用相应的“Site Recovery IP”。
*.servicebus.windows.net | 必需，以便从 VM 写入 Site Recovery 监视和诊断数据。 如果防火墙代理支持 IP，则可以使用相应的“Site Recovery 监视 IP”。

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 范围的出站连接（错误代码 151037 或 151072）

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>问题 1：未能向 Site Recovery 注册 Azure 虚拟机 (151195) </br>
- **可能的原因** </br>
  - 由于 DNS 解析失败而无法建立到 Site Recovery 终结点的连接。
  - 在重新保护期间，对虚拟机进行故障转移但无法从 DR 区域访问 DNS 服务器时经常会出现此问题。

- **分辨率**
   - 如果你使用的是自定义 DNS，请确保可以从灾难恢复区域访问 DNS 服务器。 若要检查你是否具有自定义 DNS，请转到“VM”>“灾难恢复网络”>“DNS 服务器”。 尝试从虚拟机访问 DNS 服务器。 如果它无法访问，请通过对 DNS 服务器进行故障转移或创建 DR 网络与 DNS 之间站点的行来使其可访问。

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>问题 2：Site Recovery 配置失败 (151196)

> [!NOTE]
> 如果虚拟机位于**标准**内部负载均衡器后面，则默认情况下无法访问 O365 IP（即login.microsoftonline.com）。 请将其更改为**基本**内部负载均衡器类型或创建[此文](https://aka.ms/lboutboundrulescli)中提到的出站访问权限。

- **可能的原因** </br>
  - 无法建立到 Office 365 身份验证和标识 IP4 终结点的连接。

- **分辨率**
  - Azure Site Recovery 需要具有对 Office 365 IP 范围的访问权限来进行身份验证。
    如果你使用 Azure 网络安全组 (NSG) 规则/防火墙代理控制 VM 上的出站网络连接，请确保允许到 O365 IP 范围的通信。 创建基于[Azure 活动目录 （Azure AD） 服务标记](../virtual-network/security-overview.md#service-tags)的 NSG 规则，允许访问与 Azure AD 对应的所有 IP 地址
      - 如果将来将新地址添加到 Azure AD 中，则需要创建新的 NSG 规则。

### <a name="example-nsg-configuration"></a>NSG 配置示例

此示例演示如何为要复制的 VM 配置 NSG 规则。

- 如果使用 NSG 规则控制出站连接，请对所有必需的 IP 地址范围使用端口 443 的“允许 HTTPS 出站”规则。
- 此示例假设 VM 源位置是“美国东部”，目标位置是“美国中部”。

### <a name="nsg-rules---east-us"></a>NSG 规则 - 美国东部

1. 基于 NSG 规则为“Storage.EastUS”创建出站 HTTPS (443) 安全规则，如以下屏幕截图所示。

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. 基于 NSG 规则为“AzureActiveDirectory”创建出站 HTTPS (443) 安全规则，如以下屏幕截图所示。

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. 为对应于目标位置的 Site Recovery IP 创建出站 HTTPS (443) 规则：

   **位置** | **Site Recovery IP 地址** |  **Site Recovery 监视 IP 地址**
    --- | --- | ---
   美国中部 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG 规则 - 美国中部

必须创建这些规则，才能在故障转移后启用从目标区域到源区域的复制：

1. 基于 NSG 为“Storage.CentralUS”创建出站 HTTPS (443) 安全规则。

2. 基于 NSG 规则为“AzureActiveDirectory”创建出站 HTTPS (443) 安全规则。

3. 为对应于源位置的 Site Recovery IP 创建出站 HTTPS (443) 规则：

   **位置** | **Site Recovery IP 地址** |  **Site Recovery 监视 IP 地址**
    --- | --- | ---
   美国中部 | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>问题 3：Site Recovery 配置失败 (151197)
- **可能的原因** </br>
  - 无法建立到 Azure Site Recovery 服务终结点的连接。

- **分辨率**
  - Azure Site Recovery 需要根据区域访问 [Site Recovery IP 范围](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags)。 请确保可以从虚拟机访问所需的 IP 范围。


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>问题 4：当网络流量通过本地代理服务器 （151072） 时，A2A 复制失败
- **可能的原因** </br>
  - 自定义代理设置无效，并且 Azure Site Recovery 移动服务代理未在 IE 中自动检测到代理设置


- **分辨率**
  1. 移动服务代理通过 Windows 上的 IE 和 Linux 上的 /etc/environment 检测代理设置。
  2. 如果只想对 Azure Site Recovery 移动服务设置代理，可在位于以下路径的 ProxyInfo.conf 中提供代理详细信息：</br>
     - ***Linux*** 上的 ``/usr/local/InMage/config/``
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``在***Windows***上
  3. ProxyInfo.conf 应包含采用以下 INI 格式的代理设置。</br>
                *[代理]*</br>
                *地址*http://1.2.3.4*</br>
                *Port=567*</br>
  4. Azure 站点恢复移动服务代理仅支持***未经身份验证的代理***。

### <a name="fix-the-problem"></a>解决问题
若要允许[所需 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 或[所需 IP 范围](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)，请按照[网络指南文档](site-recovery-azure-to-azure-networking-guidance.md)中的步骤进行操作。


## <a name="next-steps"></a>后续步骤
[复制 Azure 虚拟机](site-recovery-replicate-azure-to-azure.md)
