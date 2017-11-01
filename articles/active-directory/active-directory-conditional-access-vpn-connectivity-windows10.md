---
title: "针对 VPN 连接的 Azure Active Directory 条件性访问（预览版）| Microsoft Docs"
description: "了解针对 VPN 连接的 Azure Active Directory 条件性访问的工作原理。 "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: e9dadb3291ee760e7b05caedfa6b4128be77aa7d
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-conditional-access-for-vpn-connectivity-preview"></a>针对 VPN 连接的 Azure Active Directory 条件性访问（预览版）

使用 [Azure Active Directory (Azure AD) 条件性访问](active-directory-conditional-access-azure-portal.md)，可以精细控制经授权的用户如何访问资源。 使用针对虚拟专用网络 (VPN) 连接的 Azure AD 条件性访问，有助于保护 VPN 连接。


要配置针对 VPN 连接的条件性访问，需完成以下步骤： 

1.  配置 VPN 服务器。
2.  配置 VPN 客户端。
3.  配置条件性访问策略。


## <a name="before-you-begin"></a>开始之前

本主题假定读者熟悉以下主题：

- [Azure Active Directory 中的条件性访问](active-directory-conditional-access-azure-portal.md)
- [VPN 和条件性访问](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

若要深入了解 Microsoft 如何实现此功能，请参阅[使用自动 VPN 配置文件加强 Windows 10 中的远程访问](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)。   


## <a name="prerequisites"></a>先决条件

若要配置针对 VPN 连接的 Azure Active Directory 条件性访问，需要配置 VPN 服务器。 



## <a name="step-1-configure-your-vpn-server"></a>步骤1：配置 VPN 服务器 

此步骤配置通过 Azure AD 进行 VPN 身份验证时需使用的根证书。 若要配置针对 VPN 连接的条件性访问，需要：

1. 在 Azure 门户中创建一个 VPN 证书。
2. 下载该 VPN 证书。
2. 将该证书部署到 VPN 服务器。

面向 Azure AD 进行身份验证以实现 VPN 连接时，Azure AD 使用 VPN 证书来为颁发给 Windows 10 客户端的证书进行签名。 Windows 10 客户端请求的令牌是一个证书，该证书随后将呈现给应用程序，此示例中，应用程序即为 VPN 服务器。

![下载用于条件性访问的证书](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

在 Azure 门户中，当一个证书即将到期时，可创建两个证书来应对过渡期。 创建证书时，可以选择它是否为主证书，在身份验证过程中将使用主证书对用于连接的证书进行签名。

创建 VPN 证书：

1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。

2. 在左侧菜单中，单击“Azure Active Directory”。 

    ![选择“Azure Active Directory”](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. 在“Azure Active Directory”页的“管理”部分，单击“条件性访问”。

    ![选择“条件性访问”](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. 在“条件性访问”页的“管理”部分中，单击“VPN 连接（预览）”。

    ![测试 VPN 连接](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. 在“VPN 连接”页，单击“新建证书”。

    ![选择“新建证书”](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. 在“新建”页，执行以下步骤：

    ![选择持续时间和“主证书”](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. 对于“持续时间”，请选择“1 年”。

    b. 对于“主证书”，请选择“是”。

    c. 单击“创建” 。

7. 在 VPN 连接页上，单击“下载证书”。


现在已准备就绪，可将新创建的证书部署到 VPN 服务器了。 在 VPN 服务器上，将所下载的证书添加为“VPN 身份验证的受信根证书”。

对于基于 Windows RRAS 的部署，在 NPS 服务器上，通过运行以下命令将根证书添加到 *Enterprise NTauth* 存储中：

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2-configure-your-vpn-client"></a>步骤 2：配置 VPN 客户端 

在此步骤中，将配置 VPN 客户端连接配置文件，如 [VPN 和条件性访问](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)中所述。


## <a name="step-3-configure-your-conditional-access-policy"></a>步骤 3：配置条件性访问策略

本节介绍如何配置 VPN 连接的条件性访问策略。


1. 在“条件性访问”页顶部的工具栏中，单击“添加”。

    ![在“条件性访问”页面上选择“添加”](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. 在“新建”页面上，在“名称”框中，键入策略的名称。 例如，键入“VPN policy”。

    ![在“条件性访问”页面上添加策略名称](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. 在“分配”部分中，单击“用户和组”。

    ![选择“用户和组”](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. 在“用户和组”页，执行以下步骤：

    ![选择测试用户](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. 单击“选择用户和组”。

    b. 单击“选择”。

    c. 在“选择”页中，选择测试用户，并单击“选择”。

    d. 在“用户和组”页，单击“完成”。

7. 在“新建”页，执行以下步骤：

    ![选择云应用](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. 在“分配”部分，单击“云应用”。

    b. 在“云应用”页上，单击“选择应用”，然后单击“选择”。

    c. 在“选择”页面上，在“应用程序”框中，键入“vpn”。

    d. 选择“VPN 服务器”。

    e. 单击“选择”。


13. 在“新建”页的“控制”部分中，单击“授权”，打开“授权”页。

    ![选择“授权”](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. 在“授权”页，执行以下步骤：

    ![选择“需要多重身份验证”](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. 选择“需要多重身份验证”。

    b. 单击“选择”。

15. 在“新建”页中的“启用策略”下，单击“打开”。

    ![启用策略](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. 在“新建”页，单击“创建”。



## <a name="next-steps"></a>后续步骤

若要深入了解 Microsoft 如何实现此功能，请参阅[使用自动 VPN 配置文件加强 Windows 10 中的远程访问](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)。    

