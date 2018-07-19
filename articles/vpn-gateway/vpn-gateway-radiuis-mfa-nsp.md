---
title: 使用安全 Azure VPN 网关 RADIUS 身份验证和 NPS 服务器实现多重身份验证 | Microsoft Docs
description: 介绍如何将 Azure 网关 RADIUS 身份验证与 NPS 服务器集成实现多重身份验证。
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2018
ms.author: genli
ms.openlocfilehash: 70c760cd0cb571cc95250ab793829b060341e0ed
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056572"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>将 Azure VPN 网关 RADIUS 身份验证与 NPS 服务器集成实现多重身份验证 

本文介绍如何将网络策略服务器 (NPS) 与 Azure VPN 网关 RADIUS 身份验证集成，为点到站点 VPN 连接提供多重身份验证 (MFA)。 

## <a name="prerequisite"></a>先决条件

若要启用 MFA，用户必须位于 Azure Active Directory (Azure AD) 中，后者必须从本地或云环境进行同步。 此外，用户还必须已完成 MFA 的自动注册过程。  有关详细信息，请参阅[为帐户设置双重验证](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>详细步骤

### <a name="step-1-create-a-virtual-network-gateway"></a>步骤 1：创建虚拟网络网关

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在将托管虚拟网络网关的虚拟网络中，依次选择“子网”、“网关子网”以创建子网。 

    ![有关如何添加网关子网的图像](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. 通过指定以下设置创建虚拟网络网关：

    - **网关类型**：选择“VPN”。
    - **VPN 类型**：选择“基于路由”。
    - **SKU**：根据需要选择 SKU 类型。
    - **虚拟网络**：选择已在其中创建网关子网的虚拟网络。

        ![有关虚拟网络网关设置的图像](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>步骤 2：为 Azure MFA 配置 NPS

1. 在 NPS 服务器上，[安装适用于 Azure MFA 的 NPS 扩展](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension)。
2. 打开 NSP 控制台，右键单击“RADUIS 客户端”，然后选择“新建”。 通过指定以下设置创建 RADUIS 客户端：

    - **友好名称**：键入任何名称。
    - **地址(IP 或 DNS)**：键入在步骤 1 中创建的网关子网。
    - **共享机密**：键入任何密钥，并记住它以供将来使用。

    ![有关 RADUIS 客户端设置的图像](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  在“高级”选项卡上，将供应商名称设置为“RADIUS Standard”并确保未选中“其他选项”复选框。

    ![有关 RADUIS 客户端高级设置的图像](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. 转到“策略” > “网络策略”，双击“到 Microsoft 路由和远程访问服务器的连接”策略，选择“授予访问权限”，然后单击“确定”。

### <a name="step-3-configure-the-virtual-network-gateway"></a>步骤 3 配置虚拟网络网关

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 打开已创建的虚拟网络网关。 请确保网关类型设置为 **VPN** 并且 VPN 类型为“基于路由”。
3. 单击“点到站点配置” > “立即配置”，然后指定以下设置：

    - **地址池**：键入在步骤 1 中创建的网关子网。
    - **身份验证类型**：选择“RADIUS 身份验证”。
    - **服务器 IP 地址**：键入 NPS 服务器的 IP 地址。

    ![有关点到站点设置的图像](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>后续步骤

- [Azure 多重身份验证](../active-directory/authentication/multi-factor-authentication.md)
- [将现有 NPS 基础结构与 Azure 多重身份验证集成](../active-directory/authentication/howto-mfa-nps-extension.md)
