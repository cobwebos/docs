---
title: 使用 Azure Site Recovery 对 Azure 到 Azure 灾难恢复的连接进行故障排除
description: 排查 Azure VM 灾难恢复中的连接问题
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: d2cc4133e52e7cab812413d23948da6ac2660e77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80884862"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>排查 Azure 到 Azure VM 网络连接性问题

本文介绍将 Azure 虚拟机（VM）从一个区域复制和恢复到另一个区域时，与网络连接相关的常见问题。 有关网络要求的详细信息，请参阅[复制 Azure VM 的连接性要求](azure-to-azure-about-networking.md)。

要使 Site Recovery 复制正常运行，必须具有从 VM 到特定 URL 或 IP 范围的出站连接。 如果 VM 位于防火墙后或使用网络安全组 (NSG) 规则来控制出站连接，则可能会遇到以下问题之一。

| 代码 | 详细信息 |
|---|---|
| `*.blob.core.windows.net` | 必需，以便从 VM 将数据写入到源区域中的缓存存储帐户。 如果你知道 Vm 的所有缓存存储帐户，则可以使用特定存储帐户 Url 的允许列表。 例如， `cache1.blob.core.windows.net` `cache2.blob.core.windows.net`而不是`*.blob.core.windows.net`。 |
| `login.microsoftonline.com` | 必需，用于向 Site Recovery 服务 URL 进行授权和身份验证。 |
| `*.hypervrecoverymanager.windowsazure.com` | 必需，以便从 VM 进行 Site Recovery 服务通信。 如果你的防火墙代理支持 ip，你可以使用相应的_SITE RECOVERY IP_ 。 |
| `*.servicebus.windows.net` | 必需，以便从 VM 写入 Site Recovery 监视和诊断数据。 如果你的防火墙代理支持 ip，你可以使用相应的_Site Recovery 监视 IP_ 。 |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 范围的出站连接（错误代码 151037 或 151072）

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>问题 1：未能向 Site Recovery 注册 Azure 虚拟机 (151195)

#### <a name="possible-cause"></a>可能的原因

由于域名系统（DNS）解析失败，无法建立到 Site Recovery 终结点的连接。 如果已故障转移 VM，但无法从灾难恢复（DR）区域访问 DNS 服务器，则在重新保护期间，此问题更常见。

#### <a name="resolution"></a>解决方法

如果使用的是自定义 DNS，请确保可从灾难恢复区域访问 DNS 服务器。

若要检查 VM 是否使用自定义 DNS 设置：

1. 打开 "**虚拟机**" 并选择 VM。
1. 导航到 "Vm**设置**"，然后选择 "**网络**"。
1. 在 "**虚拟网络/子网**" 中，选择用于打开虚拟网络资源页面的链接。
1. 请参阅 "**设置**" 并选择 " **DNS 服务器**"。

尝试从虚拟机访问 DNS 服务器。 如果 DNS 服务器不可访问，则可通过在 DNS 服务器上进行故障转移，或者在 DR 网络和 DNS 之间创建该站点的线路来访问它。

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>问题 2：Site Recovery 配置失败 (151196)

> [!NOTE]
> 如果 Vm 位于**标准**内部负载均衡器后面，则默认情况下，它无法访问 Office 365 IPs，如`login.microsoftonline.com`。 请将其更改为**基本**内部负载均衡器类型或创建出站访问，如[使用 Azure CLI 在标准负载均衡器中配置负载平衡和出站规则](/azure/load-balancer/configure-load-balancer-outbound-cli)中所述。

#### <a name="possible-cause"></a>可能的原因

无法建立与 Office 365 身份验证和标识 IP4 终结点的连接。

#### <a name="resolution"></a>解决方法

- Azure Site Recovery 需要访问 Office 365 IP 范围才能进行身份验证。
- 如果使用 Azure 网络安全组（NSG）规则/防火墙代理来控制 VM 的出站网络连接，请确保允许与 Office 365 IP 范围通信。 创建基于[Azure Active Directory （Azure AD）服务标记](/azure/virtual-network/security-overview#service-tags)的 NSG 规则，该规则允许访问与 Azure AD 对应的所有 IP 地址。
- 如果以后将新地址添加到 Azure AD，则需要创建新的 NSG 规则。

### <a name="example-nsg-configuration"></a>NSG 配置示例

此示例演示如何为要复制的 VM 配置 NSG 规则。

- 如果使用 NSG 规则控制出站连接，请对所有必需的 IP 地址范围使用 "**允许 HTTPS 出站**规则" 到端口443。
- 此示例假设 VM 源位置是 "**美国东部**"，目标位置是 "**美国中部**"。

#### <a name="nsg-rules---east-us"></a>NSG 规则 - 美国东部

1. 为 NSG 创建 HTTPS 出站安全规则，如以下屏幕截图所示。 此示例使用**目标服务标记**： _EastUS_和**目标端口范围**： _443_。

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="storage-tag":::

1. 为 NSG 创建 HTTPS 出站安全规则，如以下屏幕截图所示。 此示例使用**目标服务标记**： " _AzureActiveDirectory_ " 和 "**目标端口范围**： _443_"。

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. 为对应于目标位置的 Site Recovery Ip 创建 HTTPS 端口443出站规则：

   | 位置 | Site Recovery IP 地址 | Site Recovery 监视 IP 地址 |
   | --- | --- | --- |
   | 美国中部 | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>NSG 规则 - 美国中部

在此示例中，需要这些 NSG 规则，以便可以在故障转移后将复制从目标区域启用到源区域：

1. 为存储创建 HTTPS 出站安全规则 _。 CentralUS_：

   - **目标服务标记**： _CentralUS_
   - **目标端口范围**： _443_

1. 为_AzureActiveDirectory_创建 HTTPS 出站安全规则。

   - **目标服务标记**： _AzureActiveDirectory_
   - **目标端口范围**： _443_

1. 为对应于源位置的 Site Recovery Ip 创建 HTTPS 端口443出站规则：

   | 位置 | Site Recovery IP 地址 | Site Recovery 监视 IP 地址 |
   | --- | --- | --- |
   | 美国东部 | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>问题 3：Site Recovery 配置失败 (151197)

#### <a name="possible-cause"></a>可能的原因

无法建立与 Azure Site Recovery 服务终结点的连接。

#### <a name="resolution"></a>解决方法

Azure Site Recovery 需要根据区域访问 [Site Recovery IP 范围](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)。 请确保所需的 IP 范围可从 VM 中访问。

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>问题4：当网络流量经过本地代理服务器时，Azure 到 Azure 的复制失败（151072）

#### <a name="possible-cause"></a>可能的原因

自定义代理设置无效，并且 Azure Site Recovery 移动服务代理未从 Internet Explorer （IE）自动检测代理设置。

#### <a name="resolution"></a>解决方法

1. 移动服务代理在 Windows 和`/etc/environment` Linux 上的 IE 中检测代理设置。
1. 如果希望仅为 Azure Site Recovery 移动服务设置代理，可以在位于以下位置的_ProxyInfo_中提供代理详细信息：

   - **Linux**：`/usr/local/InMage/config/`
   - **Windows**：`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo_应采用以下_INI_格式的代理设置：

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery 移动服务代理仅支持**未经身份验证的代理**。

### <a name="fix-the-problem"></a>解决问题

若要允许[所需 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 或[所需 IP 范围](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)，请按照[网络指南文档](site-recovery-azure-to-azure-networking-guidance.md)中的步骤进行操作。

## <a name="next-steps"></a>后续步骤

[将 Azure VM 复制到另一个 Azure 区域](azure-to-azure-how-to-enable-replication.md)
