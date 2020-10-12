---
title: 用于 S2S VPN 的 IPsec/IKE 策略 & VNet 到 VNet 连接： Azure 门户
titleSuffix: Azure VPN Gateway
description: 使用 azure 资源管理器和 Azure 门户为 S2S 或 VNet 到 VNet 的连接配置 IPsec/IKE 策略。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: eda920640667abc6620c5c90ee7d04a44789353e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994261"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>为 S2S VPN 或 VNet 到 VNet 的连接配置 IPsec/IKE 策略： Azure 门户

本文指导完成使用 Azure 门户为 VPN 网关站点到站点 VPN 或 VNet 到 VNet 的连接配置 IPsec/IKE 策略的步骤。 以下部分将帮助你创建和配置 IPsec/IKE 策略，并将策略应用到新的或现有的连接。

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>关于 IPsec 和 IKE 策略参数

IPsec 和 IKE 协议标准支持采用各种组合的各种加密算法。 请参阅 [关于加密要求和 AZURE VPN 网关](vpn-gateway-about-compliance-crypto.md) ，了解这可如何帮助确保跨界连接和 VNet 到 vnet 连接，以满足你的符合性或安全要求。

本文介绍如何创建和配置 IPsec/IKE 策略，并将其应用到新的或现有的 VPN 网关连接。

### <a name="considerations"></a>注意事项

* IPsec/IKE 策略仅适用于以下网关 Sku：
  * ***VpnGw1 ~ 5 and VpnGw1AZ ~ 5AZ***
  * ***Standard*** 和 ***高性能***
* 一个给定的连接只能指定一个策略组合。
* 必须指定 IKE（主模式）和 IPsec（快速模式）的所有算法和参数。 不允许指定部分策略。
* 请查阅 VPN 设备供应商规范，确保本地 VPN 设备支持该策略。 如果策略不兼容，则无法建立 S2S 或 VNet 到 VNet 的连接。

## <a name="workflow"></a><a name ="workflow"></a>工作流

本部分概述了对 S2S VPN 或 VNet 到 VNet 的连接创建和更新 IPsec/IKE 策略的工作流程：

1. 创建虚拟网络和 VPN 网关。
2. 为跨界连接创建本地网络网关，或为 VNet 到 VNet 连接创建另一个虚拟网络和网关。
3. 创建 (IPsec 或 VNet2VNet) 的连接。
4. 在连接资源上配置/更新/删除 IPsec/IKE 策略。

本文中的说明将帮助你设置和配置 IPsec/IKE 策略，如图中所示：

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE 策略关系图" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>支持的加密算法 & 密钥强度

### <a name="algorithms-and-keys"></a><a name ="table1"></a>算法和密钥

下表列出了支持的加密算法和密钥强度，客户可自行配置：

| **IPsec/IKE**    | **选项**    |
| ---              | ---            |
| IKE 加密   | AES256、AES192、AES128、DES3、DES                  |
| IKE 完整性    | SHA384、SHA256、SHA1、MD5                          |
| DH 组         | DHGroup24、ECP384、ECP256、DHGroup14、DHGroup2048、DHGroup2、DHGroup1、无 |
| IPsec 加密 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、无    |
| IPsec 完整性  | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1、MD5 |
| PFS 组        | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、无   |
| QM SA 生存期   |  (**可选**：如果未指定，则使用默认值) <br>秒（整数；至少为 300 秒/默认为 27000 秒）<br>KB（整数；至少为 1024 KB/默认为 102400000 KB）    |
| 流量选择器 | UsePolicyBasedTrafficSelectors**（$True/$False; **可选**，如果未指定，则使用默认值 $False）    |
| DPD 超时      | 秒 (整数：最小值为 9/最大 3600;默认45秒)  |
|  |  |

#### <a name="important-requirements"></a>重要要求

* 本地 VPN 设备配置必须匹配，或者必须包含可在 Azure IPsec/IKE 策略中指定的以下算法和参数：
  * IKE 加密算法（主模式 / 阶段 1）
  * IKE 完整性算法（主模式 / 阶段 1）
  * DH 组（主模式 / 阶段 1）
  * IPsec 加密算法（快速模式 / 阶段 2）
  * IPsec 完整性算法（快速模式 / 阶段 2）
  * PFS 组 (快速模式/阶段 2) # A0 * 流量选择器 (如果使用了 UsePolicyBasedTrafficSelectors) 
  * SA 生存期是本地规范，不需匹配。

* 如果 GCMAES 用作 IPsec 加密算法，则必须为 IPsec 完整性选择相同的 GCMAES 算法和密钥长度;例如，将 GCMAES128 用于这两者。

* 在上面的 [算法和键表](#table1) 中：
  * IKE 对应于主模式或阶段1
  * IPsec 对应于快速模式或阶段 2
  * DH 组指定在主模式或阶段 1 中使用的 Diffie-Hellmen 组
  * PFS 组指定在快速模式或阶段 2 中使用的 Diffie-Hellmen 组

* 在 Azure VPN 网关上，IKE 主模式 SA 生存期固定为28800秒。

* 如果将 **UsePolicyBasedTrafficSelectors** 设置为在连接时 $True，它会将 Azure VPN 网关配置为连接到本地基于策略的 vpn 防火墙。 如果启用 PolicyBasedTrafficSelectors，则需确保对于本地网络（本地网关）前缀与 Azure 虚拟网络前缀的所有组合，VPN 设备都定义了与之匹配的流量选择器（而不是任意到任意）。 例如，如果本地网络前缀为 10.1.0.0/16 和 10.2.0.0/16，虚拟网络前缀为 192.168.0.0/16 和 172.16.0.0/16，则需指定以下流量选择器：
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   有关基于策略的流量选择器的详细信息，请参阅[连接多个基于策略的本地 VPN 设备](vpn-gateway-connect-multiple-policybased-rm-ps.md)。

* DPD timeout-Azure VPN 网关上的默认值为45秒。 将超时设置为较短的时间段将导致 IKE 更主动地重新生成密钥，导致连接似乎在某些实例中断开连接。 如果本地位置远离 VPN 网关所在的 Azure 区域，或者物理链接条件可能会导致数据包丢失，则这可能不是必需的。 一般的建议是将超时设置为 **30 到 45** 秒。

### <a name="diffie-hellman-groups"></a>Diffie-Hellman 组

下表列出了自定义策略支持的相应 Diffie-Hellman 组：

| **Diffie-Hellman 组**  | **DHGroup**              | **PFSGroup** | **密钥长度** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768 位 MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 位 MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 位 MODP  |
| 19                        | ECP256                   | ECP256       | 256 位 ECP    |
| 20                        | ECP384                   | ECP384       | 384 位 ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 位 MODP  |

如需更多详细信息，请参阅 [RFC3526](https://tools.ietf.org/html/rfc3526) 和 [RFC5114](https://tools.ietf.org/html/rfc5114)。

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>具有 IPsec/IKE 策略的 S2S VPN

本部分将指导你完成使用 IPsec/IKE 策略创建站点到站点 VPN 连接的步骤。 以下步骤创建连接，如下图所示：

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="IPsec/IKE 策略关系图" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>步骤 1 - 创建虚拟网络、VPN 网关和本地网关

创建以下资源，如以下屏幕截图所示。 有关步骤，请参阅 [创建站点到站点 VPN 连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。

* **虚拟网络：**  TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="IPsec/IKE 策略关系图":::

* **VPN 网关：** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="IPsec/IKE 策略关系图":::

* **本地网络网关：** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="IPsec/IKE 策略关系图":::

* **连接：** VNet1 到 Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="IPsec/IKE 策略关系图":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>步骤 2-在 S2S VPN 连接上配置 IPsec/IKE 策略

在本部分中，使用以下算法和参数配置 IPsec/IKE 策略：

* IKE： AES256、SHA384、DHGroup24、DPD timeout 45 秒
* IPsec： AES256、SHA256、PFS 无、SA 生存期30000秒和102400000KB

1. 在 Azure 门户中导航到连接资源 **VNet1toSite6**。 选择 " **配置** " 页，然后选择 " **自定义** IPsec/IKE 策略" 以显示所有配置选项。 下面的屏幕截图按列表显示配置：

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="IPsec/IKE 策略关系图":::

1. 如果将 GCMAES 用于 IPsec，必须为 IPsec 加密和完整性使用相同的 GCMAES 算法和密钥长度。 例如，下面的屏幕截图为 IPsec 加密和 IPsec 完整性指定 GCMAES128：

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="IPsec/IKE 策略关系图" 以启用 Azure vpn 网关以连接到基于策略的 vpn 设备。

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="IPsec/IKE 策略关系图" 以将更改提交到连接资源。 将在大约一分钟内强制实施策略。

> [!IMPORTANT]
>
> * 对连接指定 IPsec/IKE 策略后，Azure VPN 网关将仅发送或接收对特定连接采用指定的加密算法和密钥强度的 IPsec/IKE 提议。 确保连接的本地 VPN 设备使用或接受确切策略组合，否则无法建立 S2S VPN 隧道。
>
> * 可以用**默认**策略指定**基于策略的流量选择器**和**DPD 超时**选项，而无需自定义 IPsec/IKE 策略，如上面的屏幕截图中所示。
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>VNet 到 VNet 和 IPsec/IKE 策略

使用 IPsec/IKE 策略创建 VNet 到 VNet 连接的步骤与 S2S VPN 连接的步骤类似。

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="IPsec/IKE 策略关系图" border="false":::

1. 使用 [创建 vnet 到 vnet 连接](vpn-gateway-vnet-vnet-rm-ps.md) 一文中的步骤创建 VNet 到 vnet 的连接。

2. 完成这些步骤后，你将看到两个 VNet 到 VNet 连接，如以下屏幕截图中的 VNet2GW 资源所示：

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="IPsec/IKE 策略关系图" 以显示自定义策略选项。 选择具有相应密钥长度的加密算法。

   屏幕截图显示了不同的 IPsec/IKE 策略，其中包含以下算法和参数：
   * IKE： AES128、SHA1、DHGroup14、DPD timeout 45 秒
   * IPsec：GCMAES128、GCMAES128、PFS14、SA Lifetime 14400 seconds & 102400000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="IPsec/IKE 策略关系图" **保存** "，在连接资源上应用策略更改。

5. 将相同的策略应用到其他连接资源 VNet2toVNet1。 如果不是，IPsec/IKE VPN 隧道将无法连接，因为策略不匹配。

   > [!IMPORTANT]
   > 对连接指定 IPsec/IKE 策略后，Azure VPN 网关将仅发送或接收对特定连接采用指定的加密算法和密钥强度的 IPsec/IKE 提议。 确保两个连接的 IPsec 策略相同，否则无法建立 VNet 到 VNet 的连接。

6. 完成这些步骤后，将在几分钟内建立连接，你将拥有以下网络拓扑：

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE 策略关系图" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>从连接中删除自定义 IPsec/IKE 策略

1. 若要从连接中删除自定义策略，请导航到连接资源并转到 " **配置** " 页，查看当前策略。

2. 选择 " **IPsec/IKE 策略**" 选项上的 "**默认值**"。 这将删除之前在连接上指定的所有自定义策略，并在此连接上还原默认的 IPsec/IKE 设置：

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="IPsec/IKE 策略关系图" 以删除自定义策略，并还原连接上的默认 IPSEC/IKE 设置。

## <a name="next-steps"></a>后续步骤

有关基于策略的流量选择器的详细信息，请参阅[连接多个基于策略的本地 VPN 设备](vpn-gateway-connect-multiple-policybased-rm-ps.md)。
