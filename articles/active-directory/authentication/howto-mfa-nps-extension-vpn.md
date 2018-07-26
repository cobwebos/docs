---
title: 通过网络策略服务器扩展集成 VPN 与 Azure MFA | Microsoft Docs
description: 使用 Microsoft Azure 的网络策略服务器扩展将 VPN 基础结构与Azure MFA 进行集成。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: c1247dfca6dea638da2113fef940b97ad3348b9a
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160274"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>使用 Azure 网络策略服务器扩展集成 VPN 基础结构与 Azure MFA

## <a name="overview"></a>概述

通过 Azure 的网络策略服务器 (NPS) 扩展，可使组织使用提供双重验证的基于云的 [Azure 多重身份验证 (MFA)](howto-mfaserver-nps-rdg.md) 来保护远程身份验证拨入用户服务 (RADIUS) 客户端身份验证。

本文将说明如何使用 Azure 的 NPS 扩展集成 NPS 基础结构与 MFA。 此进程为尝试使用 VPN 连接到网络的用户启用安全双重验证。 

网络策略和访问服务让组织能够：

* 分配一个用于网络请求管理和控制的集中位置以指定：

    * 谁可以连接 
    
    * 允许什么时间进行连接 
    
    * 连接持续时间
    
    * 客户端连接时须使用的安全级别

    不是在每个 VPN 或远程桌面网关服务器上指定策略，而是在位于中心位置后指定。 RADIUS 协议用于提供集中身份验证、授权和计帐 (AAA)。 

* 建立和强制执行网络访问保护 (NAP) 客户端健康策略，可确定是授予设备对网络资源的无限制还是受限制的访问权限。

* 提供一种强制进行身份验证和授权，以访问支持 802.1x 无线访问点和以太网交换机的方式。   
有关详细信息，请参阅[网络策略服务器](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)。 

若要增强安全性，并提供高级别的符合性，组织可以将 NPS 与 Azure 多重身份验证集成，以确保用户使用双重验证连接到 VPN 服务器上的虚拟端口。 已授予访问权限的用户，他们必须提供其用户名和密码组合与他们控制的其他信息。 此信息必须受信任且不易复制。 可以包括手机号码、座机号码或者移动设备上的应用程序。

在为 Azure 提供 NPS 扩展之前，希望对集成的 NPS 和 Azure MFA 环境实施双重验证的客户，必须在本地环境中配置和维护单独的 MFA 服务器。 此类型的身份验证由使用 RADIUS 的远程桌面网关和 Azure 多重身份验证服务器提供。

通过用于 Azure 的 NPS 扩展，组织可以通过部署基于本地的 MFA 解决方案或基于云的 MFA 解决方案，保护 RADIUS 客户端身份验证。
 
## <a name="authentication-flow"></a>身份验证流
用户在连接到 VPN 服务器上的虚拟端口时，必须先使用多种协议进行身份验证。 协议允许使用用户名和密码的组合和基于证书的身份验证方法。 

除了进行身份验证和验证标识外，用户还必须具有适当的拨入权限。 在简单实现中，可直接在 Active Directory 用户对象上设置允许访问的拨入权限。 

![用户属性](./media/howto-mfa-nps-extension-vpn/image1.png)

在简单实现中，每个 VPN 服务器将根据每个本地 VPN 服务器上定义的策略授予或拒绝访问。

在更大和更具缩放性的实现中，授予或拒绝 VPN 访问的策略集中在 RADIUS 服务器上。 在这些情况下，VPN 服务器充当向 RADIUS 服务器转发连接请求和帐户消息的访问服务器（RADIUS 客户端）。 若要连接到 VPN 服务器上的虚拟端口，用户必须进行身份验证，并满足 RADIUS 服务器上集中定义的条件。 

当 Azure 的 NPS 扩展与 NPS 集成时，成功的身份验证流结果如下：

1. VPN 服务器从包含用户名和密码的 VPN 用户接收身份验证请求，用于连接到资源，如远程桌面会话。 

2. 作为 RADIUS 客户端，VPN 服务器将请求转换为 RADIUS“访问请求”消息，并将其（密码已加密）发送到安装了 NPS 扩展的 RADIUS 服务器。 

3. 用户名和密码组合在 Active Directory 中进行验证。 如果用户名或密码不正确，RADIUS 服务器将发送“访问被拒”消息。 

4. 如果满足 NPS 连接请求和网络策略中指定的所有条件（例如，时间或组成员资格限制），NPS 扩展将触发对 Azure 多重身份验证进行辅助身份验证的请求。 

5. Azure 多重身份验证与 Azure Active Directory 进行通信、检索用户详细信息并使用用户配置的方法（手机呼叫、短信或移动应用）执行辅助身份验证。 

6. 当 MFA 质询成功时，Azure 多重身份验证将结果传递给 NPS 扩展。

7. 对连接尝试进行身份验证和授权之后，在其上安装了扩展的 NPS 会将 RADIUS“访问接受”消息发送到 VPN 服务器（RADIUS 客户端）。

8. 用户被授予对 VPN 服务器上虚拟端口的访问权限，并建立加密的 VPN 隧道。

## <a name="prerequisites"></a>先决条件
此部分详细介绍集成 MFA 与远程桌面网关之前必须满足的先决条件。 开始集成之前，必须具备以下先决条件：

* VPN 基础结构
* 网络策略和访问服务角色
* Azure 多重身份验证许可
* Windows Server 软件
* 库
* 将 Azure Active Directory (Azure AD) 与本地 Active Directory 同步 
* Azure Active Directory GUID ID

### <a name="vpn-infrastructure"></a>VPN 基础结构
本文假定你具有一个使用 Microsoft Windows Server 2016 的工作 VPN 基础结构，并假定目前还没有配置 VPN 服务器来将连接请求转发到 RADIUS 服务器。 在本文中，要配置 VPN 基础结构，以使用中央 RADIUS 服务器。

如果还没有工作 VPN 基础结构，则可以通过下面 Microsoft 和第三方网站上大量 VPN 安装教程中的指南来快速创建一个。 
            
### <a name="the-network-policy-and-access-services-role"></a>网络策略和访问服务角色

网络策略和访问服务提供 RADIUS 服务器和客户端功能。 本文假设已在环境中的成员服务器或域控制器上安装网络策略和访问服务角色。 在本指南中，要配置 RADIUS 以实现 VPN 配置。 在服务器上而非 VPN 服务器上，安装网络策略和访问服务角色。

有关安装网络策略和访问服务角色服务 Windows Server 2012 或更高版本的详细信息，请参阅[安装 NAP 健康策略服务器](https://technet.microsoft.com/library/dd296890.aspx)。 Windows Server 2016 已弃用 NAP。 有关 NPS 最佳做法的说明，包括在域控制器上安装 NPS 的建议，请参阅 [NPS 最佳做法](https://technet.microsoft.com/library/cc771746)。

### <a name="azure-mfa-license"></a>Azure MFA 许可证

Azure 多重身份验证需要许可证，可通过 Azure AD Premium、企业移动性 + 安全性或多重身份验证独立许可证获取。 Azure MFA 的基于使用量的许可证（例如，按用户或按身份验证许可证）与 NPS 扩展不兼容。 有关详细信息，请参阅[如何获取 Azure 多重身份验证](concept-mfa-licensing.md)。 出于测试目的，你可以使用一个试用订阅。

### <a name="windows-server-software"></a>Windows Server 软件

NPS 扩展要求使用 Windows Server 2008 R2 SP1 或更高版本，且需安装网络策略和访问服务角色。 本指南中的所有步骤均使用 Windows Server 2016 执行。

### <a name="libraries"></a>库

以下库会自动随 NPS 扩展一同安装：

-   [Visual C++ Redistributable Packages for Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块版本 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

如果 Microsoft Azure Active Directory PowerShell 模块尚未安装，可以通过作为安装过程一部分运行的配置脚本安装。 如果尚未安装此模块，则无需提前安装。

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>将 Azure Active Directory 与本地 Active Directory 同步 

要使用 NPS 扩展，本地用户必须与 Azure Active Directory 同步并启用 MFA。 本指南假设本地用户通过 Azure AD Connect 与 Azure Active Directory 同步。 下面提供了有关为用户启用 MFA 的说明。

有关 Azure AD 连接的信息，请参阅[将本地目录与 Azure Active Directory 进行集成](../connect/active-directory-aadconnect.md)。 

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID ID 

若要安装 NPS 扩展，则需要知道 Azure Active Directory 的 GUID。 下一节中提供了有关查找 Azure Active Directory 的 GUID 的说明。

## <a name="configure-radius-for-vpn-connections"></a>为 VPN 连接配置 RADIUS

如果你已在成员服务器上安装了 NPS 角色，则需要配置它，以对请求 VPN 连接的 VPN 客户端进行身份验证并授权。 

本部分假设你已安装网络策略和访问服务角色，但尚未对其进行配置以在基础结构中使用。

> [!NOTE]
> 如果你已经有使用集中式 RADIUS 服务器进行身份验证的工作 VPN 服务器，则可以跳过本部分内容。
>

### <a name="register-server-in-active-directory"></a>在 Active Directory 中注册服务器
要在这种情况下正常工作，需要在 Active Directory 中注册 NPS 服务器。

1. 打开服务器管理器。

2. 在“服务器管理器”中，依次选择“工具”和“网络策略服务器”。 

3. 在“网络策略服务器”控制台中，右键单击“NPS(本地)”，然后选择“在 Active Directory 中注册服务器”。 选择两次“确定”。

    ![网络策略服务器](./media/howto-mfa-nps-extension-vpn/image2.png)

4. 使控制台保持打开状态以进行下一个过程。

### <a name="use-wizard-to-configure-the-radius-server"></a>使用向导来配置 RADIUS 服务器
你可以使用标准（基于向导的）或高级配置选项来配置 RADIUS 服务器。 本部分假设正在使用基于向导的标准配置选项。

1. 在“网络策略服务器”控制台中，选择“NPS(本地)”。

2. 在“标准配置”下，选择“用于拨号或 VPN 连接的 RADIUS 服务器”，然后选择“配置 VPN 或拨号”。

    ![配置 VPN](./media/howto-mfa-nps-extension-vpn/image3.png)

3. 在“选择拨号或虚拟专用网络连接类型”窗口中，选择“虚拟专用网络连接”，然后选择“下一步”。

    ![虚拟专用网络](./media/howto-mfa-nps-extension-vpn/image4.png)

4. 在“指定拨号或 VPN 服务器”窗口中，选择“添加”。

5. 在“新建 RADIUS 客户端”窗口中，提供一个友好名称，输入 VPN 服务器的可解析名称或 IP 地址，然后输入一个共享密钥密码。 将此共享密钥密码设置为复杂的长密码。 请将其记录下来，因为下一个部分中会用到。

    ![新建 RADIUS 客户端](./media/howto-mfa-nps-extension-vpn/image5.png)

6. 选择“确定”，然后选择“下一步”。

7. 在“配置身份验证方法”窗口中，接受默认选择（“Microsoft 加密身份验证版本 2 [MS-CHAPv2]”），或者选择另一个选项，然后选择“下一步”。

    > [!NOTE]
    > 如果配置可扩展的身份验证协议 (EAP)，则必须使用质询握手身份验证协议 (CHAPv2) 或受保护的可扩展身份验证协议 (PEAP)。 不支持任何其他 EAP。
 
8. 在“指定用户组”窗口中，选择“添加”，然后选择相应的组。 如果没有组，将此选项保留为空，以授予所有用户访问权限。

    ![“指定用户组”窗口](./media/howto-mfa-nps-extension-vpn/image7.png)

9. 选择“**下一步**”。

10. 在“指定 IP 筛选器”窗口中，选择“下一步”。

11. 在“指定加密设置”窗口中，接受默认设置，然后选择“下一步”。

    ![“指定加密设置”窗口](./media/howto-mfa-nps-extension-vpn/image8.png)

12. 在“指定领域名”窗口中，为领域名留空，接受默认设置，然后选择“下一步”。

    ![“指定领域名”窗口](./media/howto-mfa-nps-extension-vpn/image9.png)

13. 在“完成新的拨号或虚拟专用网络连接和 RADIUS 客户端”窗口中，选择“完成”。

    ![“完成新的拨号或虚拟专用网络连接和 RADIUS 客户端”窗口](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>验证 RADIUS 配置
本部分详细介绍使用向导创建的配置。

1. 在网络策略服务器的“NPS(本地)”控制台中，展开“RADIUS 客户端”，然后选择“RADIUS 客户端”。

2. 在详细信息窗格中，右键单击创建的 RADIUS 客户端，然后选择“属性”。 RADIUS 客户端（VPN 服务器）的属性应类似于：

    ![VPN 属性](./media/howto-mfa-nps-extension-vpn/image11.png)

3. 选择“取消”。

4. 在网络策略服务器的“NPS(本地)”控制台中，展开“策略”，然后选择“连接请求策略”。 VPN 连接策略如下图所示：

    ![连接请求](./media/howto-mfa-nps-extension-vpn/image12.png)

5. 在“策略”下，选择“网络策略”。 应会看见类似下图所示策略的虚拟专用网络 (VPN) 连接策略：

    ![网络策略](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>配置 VPN 服务器以使用 RADIUS 身份验证
在本部分中，你将配置 VPN 服务器以使用 RADIUS 身份验证。 本说明假设你有 VPN 服务器的工作配置，但没有配置它来使用 RADIUS 身份验证。 在配置 VPN 服务器之后，请确认配置可如期工作。

> [!NOTE]
> 如果你已经有使用 RADIUS 身份验证的工作 VPN 服务器配置，则可以跳过本节。
>

### <a name="configure-authentication-provider"></a>配置身份验证提供程序
1. 在 VPN 服务器上，打开“服务器管理器”。

2. 在“服务器管理器”中，选择“工具”，然后选择“路由和远程访问”。

3. 在“路由和远程访问”窗口中，右键单击“\<服务器名称> (本地)”，然后选择“属性”。

    ![路由和远程访问窗口](./media/howto-mfa-nps-extension-vpn/image14.png)
 
4. 在“\<服务器名称> (本地) 属性”窗口中，选择“安全”选项卡。 

5. 在“安全”选项卡的“身份验证提供程序”下，选择“RADIUS 身份验证”，然后选择“配置”。

    ![RADIUS 身份验证](./media/howto-mfa-nps-extension-vpn/image15.png)
 
6. 在“RADIUS 身份验证”窗口中，选择“添加”。

7. 在“添加 RADIUS 服务器”窗口中，进行以下操作：

    a. 在“服务器名称”框中，输入上一部分中配置的 RADIUS 服务器的名称或 IP 地址。

    b. 对于“共享密钥”，选择“更改”，然后输入之前创建和记录的共享密钥密码。

    c. 在“超时(秒)”框中，选择一个介于 30 到 60 之间的值。  
    需要超时值以提供足够的时间来完成二次身份验证因素。
 
    ![“添加 RADIUS 服务器”窗口](./media/howto-mfa-nps-extension-vpn/image16.png)
 
8. 选择“确定”。

### <a name="test-vpn-connectivity"></a>测试 VPN 连接
在此部分中，当你尝试连接到 VPN 虚拟端口时，确认 VPN 客户端通过 RADIUS 服务器进行身份验证和授权。 该说明假设读者使用 Windows 10 作为 VPN 客户端。 

> [!NOTE]
> 如果你已配置 VPN 客户端连接到 VPN 服务器，并已保存设置，则可以跳过与配置和保存 VPN 连接对象相关的步骤。
>

1. 在 VPN 客户端计算机上，选择“开始”按钮，然后选择“设置”按钮。

2. 在“Windows 设置”窗口中，选择“网络和 Internet”。

3. 选择“VPN”。

4. 选择“添加 VPN 连接”。

5. 在“添加 VPN 连接”窗口中的“VPN 提供程序”框中，选择“Windows(内置)”，根据需要完成剩余字段，然后选择“保存”。 

    ![“添加 VPN 连接”窗口](./media/howto-mfa-nps-extension-vpn/image17.png)
 
6. 转到“控制面板”，然后选择“网络和共享中心”。

7. 选择“更改适配器设置”。

    ![更改适配器设置](./media/howto-mfa-nps-extension-vpn/image18.png)

8. 右键单击“VPN 网络连接”，然后选择“属性”。 

    ![VPN 网络属性](./media/howto-mfa-nps-extension-vpn/image19.png)

9. 在 VPN 属性窗口中，选择“安全”选项卡。 

10. 在“安全”选项卡上，确保仅选中“Microsoft CHAP 版本 2 (MS-CHAP v2)”，然后选择“确定”。

    ![“允许这些协议”选项](./media/howto-mfa-nps-extension-vpn/image20.png)

11. 右键单击“VPN 连接”，然后选择“连接”。

12. 在“设置”窗口中，选择“连接”。  
    如下所示，成功连接作为事件 ID 6272 出现在 RADIUS 服务器的安全日志中：

    ![“事件属性”窗口](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>RADIUS 故障排除

假定在你配置 VPN 服务器以使用集中式 RADIUS 服务器进行身份验证和授权之前你的 VPN 配置正在运行。 如果配置正常运行，则该问题很可能是由 RADIUS 服务器配置不正确或使用的用户名或密码无效导致的。 例如，如果在用户名中使用备用 UPN 后缀，则登录尝试可能失败。 为获得最佳结果，请使用相同的帐户名称。 

若要解决这些问题，应先检查 RADIUS 服务器上的安全事件日志。 为了节省时间搜索事件，你可以使用事件查看器中基于角色的“网络策略和访问服务器”自定义视图，如下所示。 “事件 ID 6273”指示某类事件，在这些事件中，NPS 拒绝访问用户的操作。 

![事件查看器](./media/howto-mfa-nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>配置多重身份验证

如需针对多重身份验证配置用户的帮助，请参阅文章[如何对用户或组要求双重验证](howto-mfa-userstates.md)和[为双重验证设置帐户](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>安装和配置 NPS 扩展

本部分将说明如何配置 VPN，以通过 VPN 服务器使用 MFA 进行客户端身份验证。

安装和配置 NPS 扩展后，此服务器处理的所有基于 RADIUS 的客户端身份验证都需要使用 MFA。 如果所有 VPN 用户都未在 Azure 多重身份验证中注册，可以执行以下任一操作：

* 设置另一个 RADIUS 服务器对未配置为使用 MFA 的用户进行身份验证。 

* 如果用户在 Azure 多重身份验证中注册了，则创建一个注册表项，使得被质询的用户可提供二次身份验证因素。 

创建一个名为 REQUIRE_USER_MATCH in HKLM\SOFTWARE\Microsoft\AzureMfa 的新字符串值，并将值设置为 True 或 False。 

![“要求用户匹配”设置](./media/howto-mfa-nps-extension-vpn/image34.png)
 
如果值设置为 True，或者空白，则所有身份验证请求都将受到 MFA 的质询。 如果值设为 False，则仅向在 Azure 多重身份验证中注册的用户发出 MFA 质询。 仅在在测试或生产环境中，载入期间使用 False 设置。

### <a name="obtain-the-azure-active-directory-guid-id"></a>获取 Azure Active Directory GUID ID

作为 NPS 扩展配置的一部分，必须提供管理员凭据和 Azure AD 租户 ID。 通过以下方式获取 ID：

1. 以 Azure 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com)。

2. 在左窗格中，选择“Azure Active Directory”按钮。

3. 选择“属性”。

4. 若要复制 Azure AD ID，请选择“复制”按钮。
 
    ![Azure AD ID](./media/howto-mfa-nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>安装 NPS 扩展
NPS 扩展需要安装在安装了网络策略和访问服务角色并在设计中作为 RADIUS 服务器运行的服务器上。 请勿在远程桌面服务器上安装 NPS 扩展。

1. 从 Microsoft 下载中心[下载 NPS 扩展](https://aka.ms/npsmfa)。 

2. 将安装程序可执行文件 (NpsExtnForAzureMfaInstaller.exe) 复制到 NPS 服务器。

3. 在 NPS 服务器上，双击“NpsExtnForAzureMfaInstaller.exe”，如果收到提示，则选择“运行”。

4. 在“适用于 Azure MFA 设置的 NPS 扩展”窗口中，查看软件许可条款、选择“我同意许可条款和条件”复选框，然后选择“安装”。

    ![“适用于 Azure MFA 设置的 NPS 扩展”窗口](./media/howto-mfa-nps-extension-vpn/image36.png)
 
5. 在“适用于 Azure MFA 设置的 NPS 扩展”窗口中，选择“关闭”。  

    ![“设置成功”确认窗口](./media/howto-mfa-nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>使用 PowerShell 脚本配置用于 NPS 扩展的证书
为确保安全通信并提供保证，配置供 NPS 扩展使用的证书。 NPS 组件包括配置自签名证书以用于 NPS 的 Windows PowerShell 脚本。 

此脚本可执行以下操作：

* 创建自签名证书。
* 将证书的公钥关联到 Azure AD 上的服务主体。
* 将证书存储在本地计算机存储中。
* 向网络用户授予对证书私钥的访问权限。
* 重新启动 NPS 服务。

如果要使用自己的证书，则需要将证书的公钥关联到 Azure AD 上的服务主体等等。

要使用该脚本，请提供 Azure Active Directory 管理员凭据和之前复制的 Azure Active Directory 租户 ID 的扩展。 在安装 NPS 扩展的每个 NPS 服务器上运行脚本。

1. 以管理员身份运行 Windows PowerShell。

2. 在 PowerShell 命令提示符处，输入“cd c:\Program Files\Microsoft\AzureMfa\Config”，然后按 Enter。

3. 在下一个命令提示符处，输入“.\AzureMfsNpsExtnConfigSetup.ps1”，然后按 Enter。 该脚本检查是否安装了 Azure AD PowerShell 模块。 如果未安装，该脚本将为你安装此模块。
 
    ![PowerShell](./media/howto-mfa-nps-extension-vpn/image38.png)
 
    脚本验证 PowerShell 模块的安装情况后，它将显示 Azure Active Directory PowerShell 模块登录窗口。 

4. 输入 Azure AD 管理员凭据和密码，然后选择“登录”。 
 
    ![“PowerShell 登录”窗口](./media/howto-mfa-nps-extension-vpn/image39.png)
 
5. 在命令提示符处，粘贴之前复制的租户 ID，然后按 Enter。 

    ![租户 ID](./media/howto-mfa-nps-extension-vpn/image40.png)

    此脚本创建一个自签名证书并执行其他配置更改。 其输出类似下图所示：

    ![自签名证书](./media/howto-mfa-nps-extension-vpn/image41.png)

6. 重启服务器。

### <a name="verify-the-configuration"></a>验证配置
若要验证配置，需要与 VPN 服务器建立新的 VPN 连接。 在成功输入凭据进行主要身份验证后，VPN 连接要等待辅助身份验证成功后才能建立连接，如下所示。 

![“Windows 设置 VPN”窗口](./media/howto-mfa-nps-extension-vpn/image42.png)

如果使用先前在 Azure MFA 中配置的辅助验证方法成功进行身份验证，则将连接到此资源。 但是，如果辅助身份验证不成功，你将无法访问资源。 

在以下示例中，Windows Phone 上的 Microsoft Authenticator 应用提供辅助身份验证：

![验证帐户](./media/howto-mfa-nps-extension-vpn/image43.png)

在你使用辅助方法成功通过身份验证后，将有权访问 VPN 服务器上的虚拟端口。一旦你使用辅助方法成功通过身份验证，将有权访问 VPN 服务器上的虚拟端口。 由于你需要在受信任的设备上通过移动应用使用辅助身份验证方法，因此，登录过程比仅使用用户名和密码组合更安全。

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>查看事件查看器日志以了解成功登录事件
若要查看 Windows 事件查看器日志中的成功登录事件，请通过输入以下 PowerShell 命令查询 NPS 服务器上的 Windows 安全日志：

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

![PowerShell 安全事件查看器](./media/howto-mfa-nps-extension-vpn/image44.png)
 
此外，你还可以查看“安全日志”或“网络策略和访问服务”自定义视图，如下所示：

![“网络策略服务器”日志](./media/howto-mfa-nps-extension-vpn/image45.png)

在安装了适用于 Azure 多重身份验证的 NPS 扩展的服务器上，可以在 Application and Services Logs\Microsoft\AzureMfa 位置找到特定于此扩展的事件查看器应用程序日志。 

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

![事件查看器“事件数”窗格](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>故障排除指南
如果配置未按照预期运行，请开始进行故障排除并首先验证用户是否配置为使用 MFA。 将用户连接到 [Azure 门户](https://portal.azure.com)。 如果提示用户进行辅助身份验证并且身份验证成功，则可以消除作为问题的 MFA 的不正确配置。

如果 MFA 对用户起作用，则查看相关的事件查看器日志。 日志包括上一节中讨论的安全事件、网关操作和 Azure 多重身份验证日志。 

以下是一个显示失败登录事件（事件 ID 6273）的安全日志示例：

![显示失败登录事件的安全日志](./media/howto-mfa-nps-extension-vpn/image47.png)

以下是 Azure 多重身份验证日志中的一个相关事件：

![“Azure 多重身份验证”日志](./media/howto-mfa-nps-extension-vpn/image48.png)

要执行高级故障排除，请参阅安装了 NPS 服务的 NPS 数据库格式日志文件。 这些日志文件以逗号分隔的文本文件形式在 %SystemRoot%\System32\Logs 文件夹中创建。 有关这些日志文件的说明，请参阅[解释 NPS 数据库格式日志文件](https://technet.microsoft.com/library/cc771748.aspx)。 

如果不将这些日志文件中的条目导入电子表格或数据库，可能难以解释这些条目。 可以在线找到多个 Internet 身份验证服务 (IAS) 分析工具，可帮助解释日志文件。 其中一种可下载的[共享性应用程序](http://www.deepsoftware.com/iasviewer)的输出如下所示： 

![共享性应用程序](./media/howto-mfa-nps-extension-vpn/image49.png)

若要执行其他故障排除，你可以使用协议分析器，例如 Wireshark 或 [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)。 下图来自 Wireshark，显示了 VPN 服务器与 NPS 之间的 RADIUS 消息。

![Microsoft Message Analyzer](./media/howto-mfa-nps-extension-vpn/image50.png)

有关详细信息，请参阅[将现有 NPS 基础结构与 Azure 多重身份验证进行集成](howto-mfa-nps-extension.md)。 

## <a name="next-steps"></a>后续步骤
[获取 Azure 多重身份验证](concept-mfa-licensing.md)

[使用 RADIUS 的远程桌面网关和 Azure 多重身份验证服务器](howto-mfaserver-nps-rdg.md)

[将本地目录与 Azure Active Directory 进行集成](../connect/active-directory-aadconnect.md)

