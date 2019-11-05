---
title: 配置 VPN 客户端以进行 P2S VPN 连接： Azure AD 身份验证 |Microsoft Docs
description: 可以使用 P2S VPN 通过 Azure AD authentication 连接到 VNet
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: cherylmc
ms.openlocfilehash: f4241b2ace2d7c86e1ed9036ca6d1da4a59eb61f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517522"
---
# <a name="configure-a-vpn-client-for-p2s-vpn-connections-azure-ad-authentication-preview"></a>配置 VPN 客户端以进行 P2S VPN 连接： Azure AD 身份验证（预览版）

本文介绍如何使用点到站点 VPN 和 Azure Active Directory 身份验证配置 VPN 客户端以连接到虚拟网络。 必须先配置 Azure AD 租户，然后才能使用 Azure AD 进行连接和身份验证。 有关详细信息，请参阅[Configure a Azure AD 租户](openvpn-azure-ad-tenant.md)。

## <a name="profile"></a>使用客户端配置文件

若要连接，需要在要连接到 VNet 的每台计算机上配置 VPN 客户端配置文件。 你可以在计算机上创建客户端配置文件，将其导出，然后将其导入到其他计算机。

### <a name="cert"></a>创建基于证书的客户端配置文件

使用基于证书的配置文件时，请确保在客户端计算机上安装了相应的证书。 有关证书的详细信息，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>创建 RADIUS 客户端配置文件

  ![半径](./media/openvpn-azure-ad-client/create/create-radius1.jpg)

### <a name="export"></a>导出和分发客户端配置文件

获得工作配置文件并需要将其分发给其他用户后，可以使用以下步骤将其导出：

1. 突出显示要导出的 VPN 客户端配置文件，选择 " **...** "，然后选择 "**导出**"。

    ![先导](./media/openvpn-azure-ad-client/export/export1.jpg)

2. 选择要将此配置文件保存到的位置，将 "文件名" 保留原样，然后选择 "**保存**" 以保存 xml 文件。

    ![先导](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>导入客户端配置文件

1. 在该页上，选择 "**导入**"。

    ![进口](./media/openvpn-azure-ad-client/import/import1.jpg)

2. 浏览到配置文件 xml 文件并将其选中。 选择该文件后，选择 "**打开**"。

    ![进口](./media/openvpn-azure-ad-client/import/import2.jpg)

3. 指定配置文件的名称，然后选择 "**保存**"。

    ![进口](./media/openvpn-azure-ad-client/import/import3.jpg)

4. 选择 "**连接**" 以连接到 VPN。

    ![进口](./media/openvpn-azure-ad-client/import/import4.jpg)

5. 连接后，该图标将变为绿色，并指示**已连接**。

    ![进口](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>删除客户端配置文件

1. 选择要删除的客户端配置文件旁边的省略号。 然后选择 "**删除**"。

    ![删除](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. 选择 "**删除**" 以删除。

    ![删除](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>创建连接

1. 在 "" 页上，选择 " **+** "，然后单击 " **+ 添加**"。

    ![连接](./media/openvpn-azure-ad-client/create/create1.jpg)

2. 填写连接信息。 如果你不确定这些值，请与你的管理员联系。 填写值后，选择 "**保存**"。

    ![连接](./media/openvpn-azure-ad-client/create/create2.jpg)

3. 选择 "**连接**" 以连接到 VPN。

    ![连接](./media/openvpn-azure-ad-client/create/create3.jpg)

4. 选择正确的凭据，并选择 "**继续**"。

    ![连接](./media/openvpn-azure-ad-client/create/create4.jpg)

5. 成功连接后，该图标将变为绿色，并指示**已连接**。

    ![连接](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>自动连接

这些步骤可帮助你将连接配置为通过 Always on 自动连接。

1. 在 VPN 客户端的主页上，选择 " **VPN 设置**"。

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. 在 "切换应用" 对话框中选择 **"是"** 。

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. 请确保要设置的连接尚未连接，然后突出显示该配置文件并选中 "**自动连接**" 复选框。

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. 选择 "**连接**" 以启动 VPN 连接。

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>诊断连接问题

1. 若要诊断连接问题，可以使用**诊断**工具。 选择要诊断的 VPN 连接旁的 " **...** "，以显示菜单。 然后选择 "**诊断**"。

    ![出](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. 在 "**连接属性**" 页上，选择 "**运行诊断**"。

    ![出](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. 用你的凭据登录。

    ![出](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. 查看诊断结果。

    ![出](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[创建用于 P2S 的 Azure Active Directory 租户打开使用 Azure AD 身份验证的 VPN 连接](openvpn-azure-ad-tenant.md)。