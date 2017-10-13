---
title: "使用 NPS 扩展将 VPN 与 Azure MFA 集成 |Microsoft 文档"
description: "本文将讨论如何使用 Microsoft Azure 的网络策略服务器 (NPS) 扩展将 VPN 基础结构与Azure MFA 进行集成。"
services: active-directory
keywords: "Azure MFA 集成了 VPN、Azure Active Directory、网络策略服务器扩展"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: kgremban
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: b47e9b321b2fd0d0db9762003531b0fe9f045f07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-your-vpn-infrastructure-with-azure-multi-factor-authentication-mfa-using-the-network-policy-server-nps-extension-for-azure"></a>使用 Azure 的网络策略服务器 (NPS) 扩展将 VPN 基础结构与 Azure 多重身份验证 (MFA) 进行集成。

## <a name="overview"></a>概述

通过 Azure 的网络策略服务 (NPS) 扩展，可使组织使用提供双重验证的基于云的 [Azure 多重身份验证 (MFA)](multi-factor-authentication-get-started-server-rdg.md) 来保护远程身份验证拨入用户服务 (RADIUS) 客户端身份验证。

本文将介绍如何使用 Azure 的 NPS 扩展将 NPS 基础结构与 Azure MFA 进行集成，以为尝试使用 VPN 连接你的网络的用户启用安全双重验证。 

网络策略和访问服务 (NPS) 使组织能够执行以下操作：

* 通过指定网络请求的管理和控制中心位置，以指定执行连接操作的人员、允许连接的时间、连接持续时间，以及客户端必须用于连接的安全级别等。 这些策略可以集中在一个位置一次性指定，而不是在每个 VPN 或远程桌面 (RD) 网关服务器上进行指定。 RADIUS 协议用于提供集中身份验证、授权和计帐 (AAA)。 
* 建立和强制执行网络访问保护 (NAP) 客户端健康策略，可确定是授予设备对网络资源的无限制还是受限制的访问权限。
* 提供一种强制进行身份验证和授权，以访问支持 802.1x 无线访问点和以太网交换机的方式。    

有关详细信息，请参阅[网络策略服务器 (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)。 

若要增强安全性，并提供高级别的符合性，组织可以将 NPS 与 Azure MFA 集成，以确保用户使用双重验证能够连接到 VPN 服务器上的虚拟端口。 已授予访问权限的用户，他们必须提供其用户名/密码组合与用户在其控件中的信息。 此信息必须受信任且不容易复制，例如手机号码、座机号码、移动设备上的应用程序等等。

在为 Azure 提供 NPS 扩展之前，希望对集成的 NPS 和 Azure MFA 环境实施双重验证的客户，必须在本地环境中配置和维护单独的 MFA 服务器，如使用 RADIUS 的远程桌面网关和 Azure 多重身份验证服务器中所述。

现在提供了 Azure 的 NPS 扩展之后，可使组织选择部署基于内部部署的 MFA 解决方案或基于云的 MFA 解决方案来保护 RADIUS 客户端身份验证。
 
## <a name="authentication-flow"></a>身份验证流
当用户连接到 VPN 服务器上的虚拟端口时，必须首先使用各种协议进行身份验证，这允许将用户名/密码和基于证书的身份验证方法组合使用。 

除了进行身份验证和验证标识外，用户还必须具有适当的拨入权限。 在简单实现中，可直接在 Active Directory 用户对象上设置这些允许访问的拨入权限。 

 ![用户属性](./media/nps-extension-vpn/image1.png)

对于简单实现，每个 VPN 服务器将根据每个本地 VPN 服务器上定义的策略授予或拒绝访问。

在更大和更具缩放性的实现中，授予或拒绝 VPN 访问的策略集中在 RADIUS 服务器上。 在这种情况下，VPN 服务器充当向 RADIUS 服务器转发连接请求和帐户消息的访问服务器（RADIUS 客户端）。 若要连接到 VPN 服务器上的虚拟端口，用户必须进行身份验证，并满足 RADIUS 服务器上集中定义的条件。 

当 Azure 的 NPS 扩展与 NPS 集成时，成功的身份验证流如下：

1. VPN 服务器从包含用户名和密码的 VPN 用户接收身份验证请求，用于连接到资源，如远程桌面会话。 
2. 作为 RADIUS 客户端，VPN 服务器将请求转换为 RADIUS 访问请求消息，并将此消息（密码已加密）发送到安装了 NPS 扩展的 RADIUS (NPS) 服务器。 
3. 用户名和密码组合在 Active Directory 中进行验证。 如果用户名/密码不正确，RADIUS 服务器将发送访问被拒绝消息。 
4. 如果满足 NPS 连接请求和网络策略中指定的所有条件（例如，时间或组成员资格限制），NPS 扩展将触发对 Azure MFA 进行辅助身份验证的请求。 
5. Azure MFA 与 Azure Active Directory 通信，检索用户的详细信息并使用用户配置的方法（文本消息、移动应用等）执行辅助身份验证。 
6. 在 MFA 质询成功之后，Azure MFA 将结果传递给 NPS 扩展。
7. 对连接尝试进行身份验证和授权之后，在其上安装了扩展的 NPS 服务器会将 RADIUS 访问接受消息发送到 VPN 服务器（RADIUS 客户端）。
8. 用户被授予对 VPN 服务器上虚拟端口的访问权限，并建立加密的 VPN 隧道。

## <a name="prerequisites"></a>先决条件
本部分将详细介绍将 Azure MFA 与远程桌面网关集成之前的必备条件。 开始集成之前，必须具备以下先决条件：

* VPN 基础结构
* 网络策略和访问服务 (NPS) 角色
* Azure MFA 许可证
* Windows Server 软件
* 库
* Azure AD 已与本地 AD 同步 
* Azure Active Directory GUID ID

### <a name="vpn-infrastructure"></a>VPN 基础结构
本文假定你具有一个使用 Microsoft Windows Server 2016 的工作 VPN 基础结构，并假定目前还没有配置 VPN 服务器来将连接请求转发到 RADIUS 服务器。 你将配置 VPN 基础结构，以使用本指南中的中央 RADIUS 服务器。

如果还没有工作基础结构，则可以通过下面 Microsoft 和第三方网站上大量 VPN 安装教程中所提供的指南来快速创建此基础结构。 

### <a name="network-policy-and-access-services-nps-role"></a>网络策略和访问服务 (NPS) 角色

NPS 角色服务提供 RADIUS 服务器和客户端功能。 本文假定已在环境中的成员服务器或域控制器上安装了 NPS 角色。 你将为本指南中的 VPN 配置来配置 RADIUS。 在服务器上（不是你的 VPN 服务器上）安装 NPS 角色。

有关安装 NPS 角色服务 Windows Server 2012 或更高版本的信息，请参阅[安装 NAP 健康策略服务器](https://technet.microsoft.com/library/dd296890.aspx)。 Windows Server 2016 中已弃用网络访问策略 (NAP)。 有关 NPS 最佳做法的说明，包括在域控制器上安装 NPS 的建议，请参阅 [NPS 最佳做法](https://technet.microsoft.com/library/cc771746)。

### <a name="licenses"></a>许可证

Azure MFA 许可证是必需的，可通过 Azure AD Premium、企业移动性 + 安全性 (EMS) 或 MFA 订阅获得。 有关详细信息，请参阅[如何获取 Azure 多重身份验证](multi-factor-authentication-versions-plans.md)。 出于测试目的，你可以使用一个试用订阅。

### <a name="software"></a>软件

NPS 扩展需要安装了 NPS 角色服务的 Windows Server 2008 R2 SP1 或更高版本。 本指南中的所有步骤均使用 Windows Server 2016 执行。

### <a name="libraries"></a>库

这些库将自动随扩展一同安装。

-   [Visual C++ Redistributable Packages for Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块版本 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

已安装用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块，如果尚未安装，可以通过配置脚本，作为安装过程的一部分运行。 如果尚未安装此模块，则无需提前安装。

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>将 Azure Active Directory 与本地 Active Directory 同步 

要使用 NPS 扩展，本地用户必须与 Azure Active Directory 同步并启用多重身份验证。 本指南假设本地用户使用 AD Connect 与 Azure Active Directory 同步。 下面提供了有关为用户启用 MFA 的说明。
有关 Azure AD 连接的信息，请参阅[将本地目录与 Azure Active Directory 进行集成](../active-directory/connect/active-directory-aadconnect.md)。 

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID ID 
若要安装 NPS，则需要知道 Azure Active Directory 的 GUID。 下一节中提供了有关查找 Azure Active Directory 的 GUID 的说明。

## <a name="configure-radius-for-vpn-connections"></a>为 VPN 连接配置 RADIUS

如果你已在成员服务器上安装了 NPS 服务器角色，则需要配置，以对请求 VPN 连接的 VPN 客户端进行身份验证并授权。 

本部分假设你已安装网络策略服务器角色，但尚未对其进行配置以在基础结构中使用。

>[!NOTE]
>如果你已经有使用集中式 RADIUS 服务器进行身份验证的工作 VPN 服务器，则可以跳过本部分内容。
>

### <a name="register-server-in-active-directory"></a>在 Active Directory 中注册服务器
要在这种情况下正常工作，需要在 Active Directory 中注册 NPS 服务器。

1. 打开服务器管理器。
2. 在“服务器管理器”中，单击“工具”，然后单击“网络策略服务器”。 
3. 在“网络策略服务器”控制台中，右键单击“NPS(本地)”，然后单击“在 Active Directory 中注册服务器”。 单击“确定”两次。

 ![网络策略服务器](./media/nps-extension-vpn/image2.png)

4. 使控制台保持打开状态以进行下一个过程。

### <a name="use-wizard-to-configure-radius-server"></a>使用向导来配置 RADIUS 服务器
你可以使用标准（基于向导的）或高级配置选项来配置 RADIUS 服务器。 本部分假设使用基于向导的标准配置选项。

1. 在“网络策略服务器”控制台中，单击“NPS(本地)”。
2. 在“标准配置”下，选择“用于拨号或 VPN 连接的 RADIUS 服务器”，然后单击“配置 VPN 或拨号”。

 ![配置 VPN](./media/nps-extension-vpn/image3.png)

3. 在“选择拨号或虚拟专用网络连接类型”页上，选择“虚拟专用网络连接”，然后单击“下一步”。

 ![虚拟专用网络](./media/nps-extension-vpn/image4.png)

4. 在“指定拨号或 VPN 服务器”页上，单击“添加”。
5. 在“新建 RADIUS 客户端”对话框中，提供一个友好名称，输入 VPN 服务器的可解析名称或 IP 地址，然后输入一个共享密钥密码。 将此共享密钥密码设置为复杂的长密码。 请记下此密码，因为会在下一部分的步骤中用到它。

 ![新建 RADIUS 客户端](./media/nps-extension-vpn/image5.png)

6. 单击“确定”，然后单击“下一步”。
7. 在“配置身份验证方法”页上，接受默认选择（Microsoft 加密身份验证版本 2 (MS-CHAPv2)，或者选择另一个选项，然后单击“下一步”。

  >[!NOTE]
  >如果你配置可扩展身份验证协议 (EAP)，必须使用 MS CHAPv2 或 PEAP。 不支持任何其他 EAP。
 
8. 在“指定用户组”页上，单击“添加”，然后选择相应的组（如果存在的话）。 否则，将此选项保留为空，以授予所有用户访问权限。

 ![指定用户组](./media/nps-extension-vpn/image7.png)

9. 单击“下一步”。
10. 在“指定 IP 筛选器”页上，单击“下一步”。
11. 在“指定加密设置”页上，接受默认设置，然后单击“下一步”。

 ![指定加密](./media/nps-extension-vpn/image8.png)

12. 在“指定领域名”上，将名称保留为空，接受默认设置，然后单击“下一步”。

 ![指定领域名](./media/nps-extension-vpn/image9.png)

13. 在“完成新的拨号或虚拟专用网络连接和 RADIUS 客户端”页上，单击“完成”。

 ![完成连接](./media/nps-extension-vpn/image10.png)

### <a name="verify-radius-configuration"></a>验证 RADIUS 配置
本部分详细介绍使用向导创建的配置。

1. 在 NPS 服务器的“NPS(本地)”控制台中，展开“RADIUS 客户端”，并选择“RADIUS 客户端”。
2. 在“详细信息”窗格中，右键单击使用向导创建的“ RADIUS 客户端”，然后单击“属性”。 RADIUS 客户端（VPN 服务器）的属性应类似于下面所示的属性。

 ![VPN 属性](./media/nps-extension-vpn/image11.png)

3. 单击“取消”。
4. 在 NPS 服务器的“NPS(本地)”控制台中，展开“策略”，然后选择“连接请求策略”。 你应看到类似于下图所示的 VPN 连接策略。

 ![连接请求](./media/nps-extension-vpn/image12.png)

5. 在“策略”下，选择“网络策略”。 你应看到类似于下图所示的虚拟专用网络 (VPN) 连接策略。

 ![网络属性](./media/nps-extension-vpn/image13.png)

## <a name="configure-vpn-server-to-use-radius-authentication"></a>配置 VPN 服务器以使用 RADIUS 身份验证
在本部分中，你将配置 VPN 服务器以使用 RADIUS 身份验证。 本部分假设你有 VPN 服务器的工作配置，但没有配置 VPN 服务器来使用 RADIUS 身份验证。 在配置 VPN 服务器之后，请确认配置可如期工作。

>[!NOTE]
>如果你已经有使用 RADIUS 身份验证的工作 VPN 服务器配置，则可以跳过本节。
>

### <a name="configure-authentication-provider"></a>配置身份验证提供程序
1. 在 VPN 服务器上，打开“服务器管理器”。
2. 在“服务器管理器”中，单击“工具”，然后单击“路由和远程访问”。
3. 在“路由和远程访问”控制台中，右键单击“\[服务器名称(本地)”**\]**，然后单击“属性”。

 ![路由和远程访问](./media/nps-extension-vpn/image14.png)
 
4. 在“[服务器名称}(本地)属性”对话框中，单击“安全”选项卡。 
5. 在“安全”选项卡的“身份验证”提供程序下，单击“RADIUS 身份验证”，然后单击“配置”。

 ![RADIUS 身份验证](./media/nps-extension-vpn/image15.png)
 
6. 在“RADIUS 身份验证”对话框中，单击“添加”。
7. 在“添加 RADIUS 服务器”的“服务器名称”中，添加上一节中配置的 RADIUS 服务器的名称或 IP 地址。
8. 在“共享密钥”中，单击“更改”并添加前面创建和记录的共享密钥密码。
9. 在“超时(秒)”中，将值更改为介于 30 和 60 之间的值。 需要提供足够的时间来完成二次身份验证因素。
 
 ![添加 RADIUS 服务器](./media/nps-extension-vpn/image16.png)
 
10. 在完成关闭所有对话框之前，单击“确定”。

### <a name="test-vpn-connectivity"></a>测试 VPN 连接
在此部分中，当你尝试连接到 VPN 虚拟端口时，确认 VPN 客户端通过 RADIUS 服务器进行身份验证和授权。 本部分假设你使用 Windows 10 作为 VPN 客户端。 

>[!NOTE]
>如果你已配置 VPN 客户端连接到 VPN 服务器，并已保存设置，则可以跳过与配置和保存 VPN 连接对象相关的步骤。
>

1. 在 VPN 客户端计算机上，单击“启动”，然后单击“设置”（齿轮图标）。
2. 在“窗口设置”中，单击“网络和 Internet”。
3. 单击“VPN”。
4. 单击“添加 VPN 连接”。
5. 在“添加 VPN 连接”中，指定“Windows(内置)”作为 VPN 提供程序，然后根据需要完成其余字段，然后单击“保存”。 

 ![添加 VPN 连接](./media/nps-extension-vpn/image17.png)
 
6. 在控制面板中打开“网络和共享中心”。
7. 单击“更改适配器设置”。

 ![更改适配器设置](./media/nps-extension-vpn/image18.png)

8. 右键单击“VPN 网络连接”，然后单击“属性”。 

 ![VPN 网络属性](./media/nps-extension-vpn/image19.png)

9. 在“VPN 属性”对话框中，单击“安全”选项卡。 
10. 在“安全”选项卡上，确保仅选中“Microsoft CHAP 版本 2 (MS-CHAP v2)”，然后单击“确定”。

 ![允许协议](./media/nps-extension-vpn/image20.png)

11. 右键单击“VPN 连接”，然后单击“连接”。
12. 在“设置”页上，单击“连接”。

如下所示，成功连接作为事件 ID 6272 出现在 RADIUS 服务器的安全日志中。

 ![事件属性](./media/nps-extension-vpn/image21.png)

## <a name="troubleshoot-guide"></a>故障排除指南
假定在你配置 VPN 服务器以使用集中式 RADIUS 服务器进行身份验证和授权之前你的 VPN 配置正在运行。 在这种情况下，该问题很可能是由 RADIUS 服务器配置不正确或使用的用户名或密码无效导致的。 例如，如果你在用户名中使用备用 UPN 后缀，登录尝试可能会失败（应使用相同的帐户名以获得最佳结果）。 

若要解决这些问题，应先检查 RADIUS 服务器上的安全事件日志。 为了节省时间搜索事件，你可以使用事件查看器中基于角色的“网络策略和访问服务器”自定义视图，如下所示。 事件 ID 6273 指示网络策略服务器拒绝访问用户的事件。 

 ![事件查看器](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>配置多重身份验证
本部分将介绍如何为用户启用 MFA，以及如何设置帐户进行双重验证。 

### <a name="enable-multi-factor-authentication"></a>启用多重身份验证
在此部分中，你可以启用进行 MFA 的 Azure AD 帐户。 使用“经典门户”以为用户启用 MFA。 

1. 打开浏览器，导航到 [https://manage.windowsazure.com](https://manage.windowsazure.com)。 
2. 以管理员身份登录。
3. 在门户的左侧导航栏中，单击“ACTIVE DIRECTORY”。

 ![默认目录](./media/nps-extension-vpn/image23.png)

4. 在“名称”列中，单击“默认目录”（或另一个目录，如果适用的话）。
5. 在“快速启动”页上，单击“配置”。

 ![配置默认](./media/nps-extension-vpn/image24.png)

6. 在“配置”页上向下滚动，并在“多重身份验证”部分，单击“管理服务设置”。

 ![管理 MFA 设置](./media/nps-extension-vpn/image25.png)
 
7. 在“多重身份验证”页上，查看默认服务设置，然后单击“用户”。 

 ![MFA 用户](./media/nps-extension-vpn/image26.png)
 
8. 在“用户”页中，选择想要为其启用 MFA 的用户，然后单击“启用”。

 ![属性](./media/nps-extension-vpn/image27.png)
 
9. 出现提示时，单击“启用多重身份验证”。

 ![启用 MFA](./media/nps-extension-vpn/image28.png)
 
10. 单击“关闭”。 
11. 刷新页面。 MFA 状态将变为“已启用”。

有关如何为用户启用多重身份验证的信息，请参阅[云中的 Azure 多重身份验证入门](multi-factor-authentication-get-started-cloud.md)。 

### <a name="configure-accounts-for-two-step-verification"></a>配置帐户进行双重验证
一旦帐户启用了 MFA，在成功配置受信任的设备，以用于已使用双重验证的二次身份验证因素前，用户将无法登录 MFA 策略管理的资源。

在本部分中，你将配置一个受信任的设备以用于双重验证。 有几个选项可供你进行配置，包括：

* **移动应用**。 在 Windows Phone、Android 或 iOS 设备上安装 Microsoft Authenticator 应用。 根据组织的策略，你必须在任一以下两种模式下使用应用：接收验证通知（通知会推送到你的设备上），或使用验证码（将要求你输入每隔 30 秒更新一次的验证码）。 
* **移动电话呼叫或短信**。 你会收到自动电话呼叫或短信。 使用电话呼叫选项，可以接听电话并按 # 号进行身份验证。 使用短信选项，可以回复短信或在登录界面中输入验证码。
* **办公电话呼叫**。 此过程与上面所述的自动电话呼叫相同。

按照这些说明设置设备，以便使用移动应用来接收用于验证的推送通知。

1. 登录到 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) 或任何站点，如 [https://portal.azure.com](https://portal.azure.com)，在其中需要使用启用 MFA 的凭据进行身份验证。 
2. 使用用户名和密码登录时，将出现一个屏幕，提示你设置帐户以进行其他安全性验证。

 ![其他安全性](./media/nps-extension-vpn/image29.png)

3. 单击“立即设置”。
4. 在“其他安全性验证”页中，选择联系人类型（身份验证电话、办公电话或移动应用）。 然后选择国家或地区，并选择一种方法。 方法将根据你所选联系人的类型而异。 例如，如果你选择“移动应用”，则可以选择“接收验证通知”或“使用验证码”。 后续步骤假定你选择“移动应用”作为联系人类型。

 ![电话身份验证](./media/nps-extension-vpn/image30.png)

5. 选择“移动应用”，单击“接收验证通知”，然后单击“设置”。 

 ![移动应用验证](./media/nps-extension-vpn/image31.png)
 
6. 如果你尚未这样做，请在设备上安装验证器移动应用。 
7. 按照移动应用中的说明扫描提供的条码或手动输入信息，然后单击“完成”。

 ![配置移动应用](./media/nps-extension-vpn/image32.png)

8. 在“其他安全性验证”页中，单击“联系我”并回复发送到设备的通知。
9. 在“其他安全性验证”页中，输入一个联系人号码以防你无法访问移动应用，并单击“下一步”。

 ![移动电话号码](./media/nps-extension-vpn/image33.png)
 
10. 在“其他安全性验证”上，单击“完成”。

现已配置设备以提供第二种验证方法。 有关设置帐户进行双重验证的信息，请参阅[设置我的帐户进行双重验证](./end-user/multi-factor-authentication-end-user-first-time.md)。

## <a name="install-and-configure-nps-extension"></a>安装和配置 NPS 扩展

本部分将说明如何配置 VPN，以通过 VPN 服务器使用 Azure MFA 进行客户端身份验证。

一旦安装和配置 NPS 扩展，此服务器处理的所有基于 RADIUS 的客户端身份验证都需要使用 Azure MFA。 如果没有在 Azure MFA 中注册所有 VPN 用户，则可以设置另一个 RADIUS 服务器，以对未配置使用 MFA 的用户进行身份验证。 或者，可以创建一个注册表项，使得被质询的用户可提供二次身份验证因素，这仅限用户在注册 MFA 时。 

创建一个名为 REQUIRE_USER_MATCH in HKLM\SOFTWARE\Microsoft\AzureMfa 的新字符串值，并将值设置为 TRUE 或 FALSE。 

 ![要求用户匹配](./media/nps-extension-vpn/image34.png)
 
如果值设置为 TRUE，或者未设置，则所有身份验证请求都将受到 MFA 的质询。 如果值设置为 FALSE，那么仅在 MFA 中注册的用户会受到 MFA 的质询。 在测试或生产环境中，仅在载入期间使用 FALSE 设置。

### <a name="acquire-azure-active-directory-guid-id"></a>获得 Azure Active Directory GUID ID

作为 NPS 扩展配置的一部分，你需要为 Azure AD 租户提供管理员凭据和 Azure Active Directory ID。 以下步骤演示如何获取租户 ID。

1. 以 Azure 租户的全局管理员身份登录 Azure 门户 [https://portal.azure.com](https://portal.azure.com)。
2. 在左侧导航栏中，单击“Azure Active Directory”图标。
3. 单击“属性”。
4. 若要将你的 Directory ID 复制到剪贴板中，请选择“复制”图标。
 
 ![Directory ID](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>安装 NPS 扩展
NPS 扩展需要安装在安装了网络策略和访问服务 (NPS) 角色并在设计中作为 RADIUS 服务器运行的服务器上。 请勿在远程桌面服务器上安装 NPS 扩展。

1. 从 [https://aka.ms/npsmfa](https://aka.ms/npsmfa) 下载 NPS 扩展。 
2. 将安装程序可执行文件 (NpsExtnForAzureMfaInstaller.exe) 复制到 NPS 服务器。
3. 在 NPS 服务器上，双击“NpsExtnForAzureMfaInstaller.exe”。 系统提示后，单击“运行”。
4. 在“适用于 Azure MFA 的 NPS 扩展”对话框中，查看软件许可条款，勾选“我同意许可条款和条件”，然后单击“安装”。

 ![NPS 扩展](./media/nps-extension-vpn/image36.png)
 
5. 在“适用于 Azure MFA 的 NPS 扩展”对话框中，单击“关闭”。  

 ![安装成功](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>使用 PowerShell 脚本配置用于 NPS 扩展的证书
为确保安全通信并提供保证，你需要配置供 NPS 扩展使用的证书。 NPS 组件包括配置自签名证书以用于 NPS 的 Windows PowerShell 脚本。 

此脚本可执行以下操作：

* 创建自签名证书
* 将证书的公钥关联到 Azure AD 上的服务主体
* 将证书存储在本地计算机存储中
* 向网络用户授予对证书私钥的访问权限
* 重新启动网络策略服务器服务

如果要使用自己的证书，则需要将证书的公钥关联到 Azure AD 上的服务主体等等。
要使用该脚本，请提供 Azure Active Directory 管理员凭据和之前复制的 Azure Active Directory 租户 ID 的扩展。 在安装 NPS 扩展的每个 NPS 服务器上运行脚本。

1. 打开管理 Windows PowerShell 提示符。
2. 在 PowerShell 提示符处，键入 cd ‘c:\Program Files\Microsoft\AzureMfa\Config’，然后按 ENTER。
3. 键入 .\AzureMfsNpsExtnConfigSetup.ps1，然后按 ENTER。 
 * 该脚本将检查是否已安装 Azure Active Directory PowerShell 模块。 如果未安装，该脚本将为你安装此模块。
 
 ![PowerShell](./media/nps-extension-vpn/image38.png)
 
4. 脚本验证 PowerShell 模块的安装情况后，它将显示 Azure Active Directory PowerShell 模块对话框。 在对话框中，输入 Azure AD 管理员凭据和密码，然后单击“登录”。 
 
 ![PowerShell 登录](./media/nps-extension-vpn/image39.png)
 
5. 出现提示时，粘贴之前复制到剪贴板的租户 ID，然后按 ENTER。 

 ![租户 ID](./media/nps-extension-vpn/image40.png)

6. 此脚本创建一个自签名证书并执行其他配置更改。 输出如下图所示。

 ![自签名证书](./media/nps-extension-vpn/image41.png)

7. 重启服务器。
 
### <a name="verify-configuration"></a>验证配置
若要验证配置，你需要与 VPN 服务器建立新的 VPN 连接。 在成功输入凭据进行主要身份验证后，VPN 连接要等待辅助身份验证成功后才能建立连接，如下所示。 

 ![验证配置](./media/nps-extension-vpn/image42.png)

如果使用先前在 Azure MFA 中配置的辅助验证方法成功进行身份验证，则将连接到此资源。 但是，如果辅助身份验证不成功，你将无法访问资源。 

在下面的示例中，Windows 手机上的 Authenticator 应用用于提供辅助身份验证。

 ![验证帐户](./media/nps-extension-vpn/image43.png)

一旦你使用辅助方法成功通过身份验证，将有权访问 VPN 服务器上的虚拟端口。 但是，由于你需要在受信任的设备上通过移动应用使用辅助身份验证方法，因此，登录过程比仅使用用户名/密码组合更安全。

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>查看事件查看器日志以了解成功登录事件
要在 Windows 事件查看器日志中查看成功登录事件，可以发出以下 Windows PowerShell 命令来查询 NPS 服务器上的 Windows 安全日志。

要在安全事件查看器日志中查询成功登录事件，请使用以下命令，
* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

 ![安全事件查看器](./media/nps-extension-vpn/image44.png)
 
此外，你还可以查看“安全日志”或“网络策略和访问服务”自定义视图，如下所示：

 ![网络策略访问](./media/nps-extension-vpn/image45.png)

在安装了适用于 Azure MFA 的 NPS 扩展的服务器上，可以在 Application and Services Logs\Microsoft\AzureMfa 位置找到特定于此扩展的事件查看器应用程序日志。 

* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

 ![事件数](./media/nps-extension-vpn/image46.png)

## <a name="troubleshoot-guide"></a>故障排除指南
如果配置无法正常工作，首先要排查的问题是验证是否已配置用户使用 Azure MFA。 让用户连接到 [https://portal.azure.com](https://portal.azure.com)。如果提示用户进行辅助身份验证并且身份验证成功，则可以消除 Azure MFA 的不正确配置。

如果用户的 Azure MFA 正常工作，则应查看相关的事件日志。 其中包括上一节中讨论的安全事件、网关操作和 Azure MFA 日志。 

以下是安全日志的输出示例，其中显示了失败的登录事件（事件 ID 6273）：

 ![安全日志](./media/nps-extension-vpn/image47.png)

下面是 AzureMFA 日志中的相关事件：

 ![Azure MFA 日志](./media/nps-extension-vpn/image48.png)

要执行高级故障排除选项，请参阅安装了 NPS 服务的 NPS 数据库格式日志文件。 这些日志文件以逗号分隔的文本文件形式在 %SystemRoot%\System32\Logs 文件夹中创建。 有关这些日志文件的说明，请参阅[解释 NPS 数据库格式日志文件](https://technet.microsoft.com/library/cc771748.aspx)。 

如果不将这些日志文件中的条目导入电子表格或数据库，可能难以解释这些条目。 你可以在线查找几个 IAS 分析程序，以帮助你解释日志文件。 下面是一个可下载的[共享性应用程序](http://www.deepsoftware.com/iasviewer)的输出。 

 ![共享性应用程序](./media/nps-extension-vpn/image49.png)

最后，对于其他故障排除选项，你可以使用协议分析器，例如 Wireshark 或 [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)。 下图来自 Wireshark，显示了 VPN 服务器与 NPS 服务器之间的 RADIUS 消息。

 ![Microsoft Message Analyzer](./media/nps-extension-vpn/image50.png)

有关详细信息，请参阅[将现有 NPS 基础结构与 Azure 多重身份验证进行集成](multi-factor-authentication-nps-extension.md)。  

## <a name="next-steps"></a>后续步骤
[如何获取 Azure 多重身份验证](multi-factor-authentication-versions-plans.md)

[使用 RADIUS 的远程桌面网关和 Azure 多重身份验证服务器](multi-factor-authentication-get-started-server-rdg.md)

[将本地目录与 Azure Active Directory 进行集成](../active-directory/connect/active-directory-aadconnect.md)

