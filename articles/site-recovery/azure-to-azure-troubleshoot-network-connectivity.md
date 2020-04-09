---
title: 使用 Azure 站点恢复解决 Azure 与 Azure 灾难恢复的连接问题
description: 解决 Azure VM 灾难恢复中的连接问题
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: d2cc4133e52e7cab812413d23948da6ac2660e77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884862"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>排查 Azure 到 Azure VM 网络连接性问题

本文介绍将 Azure 虚拟机 （VM） 从一个区域复制到另一个区域时与网络连接相关的常见问题。 有关网络要求的详细信息，请参阅[复制 Azure VM 的连接性要求](azure-to-azure-about-networking.md)。

若要 Site Recovery 复制正常运行，需要从 VM 到特定 URL 或 IP 范围的出站连接。 如果 VM 位于防火墙后或使用网络安全组 (NSG) 规则来控制出站连接，则可能会遇到以下问题之一。

| 代码 | 详细信息 |
|---|---|
| `*.blob.core.windows.net` | 必需，以便从 VM 将数据写入到源区域中的缓存存储帐户。 如果您知道 VM 的所有缓存存储帐户，则可以使用特定存储帐户 URL 的允许列表。 例如，`cache1.blob.core.windows.net`而不是`cache2.blob.core.windows.net``*.blob.core.windows.net`。 |
| `login.microsoftonline.com` | 必需，用于向 Site Recovery 服务 URL 进行授权和身份验证。 |
| `*.hypervrecoverymanager.windowsazure.com` | 必需，以便从 VM 进行 Site Recovery 服务通信。 如果您的防火墙代理支持 IP，则可以使用相应的_站点恢复 IP。_ |
| `*.servicebus.windows.net` | 必需，以便从 VM 写入 Site Recovery 监视和诊断数据。 如果您的防火墙代理支持 IP，则可以使用相应的_站点恢复监视 IP。_ |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 范围的出站连接（错误代码 151037 或 151072）

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>问题 1：未能向 Site Recovery 注册 Azure 虚拟机 (151195)

#### <a name="possible-cause"></a>可能的原因

由于域名系统 （DNS） 解析失败，无法建立与站点恢复终结点的连接。 当您在 VM 上失败，但无法从灾难恢复 （DR） 区域到达 DNS 服务器时，此问题在重新保护期间更常见。

#### <a name="resolution"></a>解决方法

如果使用自定义 DNS，请确保 DNS 服务器可从灾难恢复区域访问。

要检查 VM 是否使用自定义 DNS 设置，请进行以下检查：

1. 打开**虚拟机**并选择 VM。
1. 导航到 VM**设置**并选择 **"网络**"。
1. 在**虚拟网络/子网**中，选择链接以打开虚拟网络的资源页面。
1. 转到 **"设置"** 并选择**DNS 服务器**。

尝试从虚拟机访问 DNS 服务器。 如果 DNS 服务器无法访问，则通过在 DNS 服务器上失败或在 DR 网络和 DNS 之间创建站点行来访问它。

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>问题 2：Site Recovery 配置失败 (151196)

> [!NOTE]
> 如果 VM 位于**标准**内部负载均衡器后面，默认情况下，它将无法访问 Office 365 IP（如`login.microsoftonline.com`）。 将其更改为**基本**内部负载均衡器类型，或者创建出站访问，如文章"[使用 Azure CLI 配置负载平衡器"中的负载平衡和出站规则](/azure/load-balancer/configure-load-balancer-outbound-cli)。

#### <a name="possible-cause"></a>可能的原因

无法连接到 Office 365 身份验证和标识 IP4 终结点。

#### <a name="resolution"></a>解决方法

- Azure 站点恢复需要访问 Office 365 IP 范围进行身份验证。
- 如果使用 Azure 网络安全组 （NSG） 规则/防火墙代理来控制 VM 上的出站网络连接，请确保允许与 Office 365 IP 范围通信。 创建基于[Azure 活动目录 （Azure AD） 服务标记的](/azure/virtual-network/security-overview#service-tags)NSG 规则，允许访问与 Azure AD 对应的所有 IP 地址。
- 如果将来将新地址添加到 Azure AD 中，则需要创建新的 NSG 规则。

### <a name="example-nsg-configuration"></a>NSG 配置示例

此示例演示如何为要复制的 VM 配置 NSG 规则。

- 如果使用 NSG 规则来控制出站连接，请使用允许**HTTPS 出站**规则来移植 443 以处理所有必需的 IP 地址范围。
- 该示例假定 VM 源位置**为美国东部**，目标位置为美国**中部**。

#### <a name="nsg-rules---east-us"></a>NSG 规则 - 美国东部

1. 为 NSG 创建 HTTPS 出站安全规则，如下图所示。 此示例使用**目标服务标记** _：Storage.EastUS_和目标**端口范围**： _443_。

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="storage-tag":::

1. 为 NSG 创建 HTTPS 出站安全规则，如下图所示。 此示例使用**目标服务标记** _：AzureActiveDirectory_和目标**端口范围**： _443_。

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. 为与目标位置对应的站点恢复 IP 创建 HTTPS 端口 443 出站规则：

   | 位置 | Site Recovery IP 地址 | Site Recovery 监视 IP 地址 |
   | --- | --- | --- |
   | 美国中部 | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>NSG 规则 - 美国中部

在此示例中，需要这些 NSG 规则，以便可以在故障转移后从目标区域启用复制到源区域：

1. 为存储创建 HTTPS 出站安全规则 _。_

   - **目标服务标签**：_存储.中央US_
   - **目标端口范围**： _443_

1. 为_Azure ActiveDirectory_创建 HTTPS 出站安全规则。

   - **目标服务标记**： _Azure ActiveDirectory_
   - **目标端口范围**： _443_

1. 为与源位置对应的站点恢复 IP 创建 HTTPS 端口 443 出站规则：

   | 位置 | Site Recovery IP 地址 | Site Recovery 监视 IP 地址 |
   | --- | --- | --- |
   | 美国东部 | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>问题 3：Site Recovery 配置失败 (151197)

#### <a name="possible-cause"></a>可能的原因

无法建立到 Azure 站点恢复服务终结点的连接。

#### <a name="resolution"></a>解决方法

Azure Site Recovery 需要根据区域访问 [Site Recovery IP 范围](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)。 确保从 VM 可访问所需的 IP 范围。

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>问题 4：当网络流量通过本地代理服务器 （151072） 时，Azure 到 Azure 复制失败

#### <a name="possible-cause"></a>可能的原因

自定义代理设置无效，Azure 站点恢复移动服务代理未从 Internet 资源管理器 （IE） 自动检测代理设置。

#### <a name="resolution"></a>解决方法

1. 移动服务代理在 Windows 和 Linux 上检测来自`/etc/environment`IE 的代理设置。
1. 如果希望只为 Azure 站点恢复移动服务设置代理，则可以在_ProxyInfo.conf_中提供代理详细信息，该详细信息位于：

   - **Linux**：`/usr/local/InMage/config/`
   - **窗口**：`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _代理信息.conf_应具有以下_INI_格式的代理设置：

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure 站点恢复移动服务代理仅支持**未经身份验证的代理**。

### <a name="fix-the-problem"></a>解决问题

若要允许[所需 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 或[所需 IP 范围](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)，请按照[网络指南文档](site-recovery-azure-to-azure-networking-guidance.md)中的步骤进行操作。

## <a name="next-steps"></a>后续步骤

[将 Azure VM 复制到另一个 Azure 区域](azure-to-azure-how-to-enable-replication.md)
