---
title: 在 Azure Active Directory 中添加本地应用 - 应用程序代理 | Microsoft Docs
description: Azure Active Directory (Azure AD) 具有可让用户使用其 Azure AD 帐户登录来访问本地应用程序的应用程序代理服务。 本教程介绍如何准备与应用程序代理配合使用的环境，然后使用 Azure 门户将一个本地应用程序添加到 Azure AD 租户。
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 2847cf4abdfdea7eb38bd965cb54a223f014fb68
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176907"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>教程：在 Azure Active Directory 中添加一个本地应用程序以通过应用程序代理进行远程访问

Azure Active Directory (Azure AD) 具有可让用户使用其 Azure AD 帐户登录来访问本地应用程序的应用程序代理服务。 本教程将准备与应用程序代理配合使用的环境。 准备好环境后，使用 Azure 门户将一个本地应用程序添加到 Azure AD 租户。

本教程：

> [!div class="checklist"]
> * 打开出站流量的端口并允许访问特定的 URL。
> * 在 Windows 服务器上安装连接器并将其注册到应用程序代理。
> * 验证是否正确安装并注册了连接器。
> * 将本地应用程序添加到 Azure AD 租户。
> * 验证测试用户是否可以使用 Azure AD 帐户登录到该应用程序。

## <a name="before-you-begin"></a>开始之前

若要向租户添加应用程序，需具备：

* 一个 [Microsoft Azure AD Basic 或 Premium 订阅](https://azure.microsoft.com/pricing/details/active-directory)。 
* 一个应用程序管理员帐户。

### <a name="windows-server"></a>Windows Server
若要使用应用程序代理，需要一台运行 Windows Server 2012 R2 或更高版本的 Windows 服务器。 在该服务器上安装应用程序代理连接器。 此连接器服务器需要连接到 Azure 中的应用程序代理服务以及要发布的本地应用程序。

为了在生产环境中实现高可用性，我们建议提供多个 Windows 服务器。 对于本教程，使用一个 Windows 服务器便已足够。

**有关连接器服务器的建议**

1. 将连接器服务器实物定位在靠近应用程序服务器的位置，以优化连接器与应用程序之间的性能。 有关详细信息，请参阅[网络拓扑注意事项](application-proxy-network-topology.md)。

2. 连接器服务器和 Web 应用程序服务器应属于同一个 Active Directory 域。 若要配合 Windows 集成身份验证 (IWA) 和 Kerberos 约束委派 (KCD) 使用单一登录 (SSO)，这些服务器必须位于同一个域。 如果连接器服务器和 Web 应用程序服务器处于不同的 Active Directory 域，则你需要使用基于资源的委派进行单一登录。 有关详细信息，请参阅[用于使用应用程序代理进行单一登录的 KCD](application-proxy-configure-single-sign-on-with-kcd.md)。

**软件要求**

在安装应用程序代理连接器之前，需要为 Windows 连接器服务器启用 TLS 1.2。 版本低于 1.5.612.0 的现有连接器继续使用早期版本的 TLS，直到另行通知。 

若要启用 TLS 1.2，请执行以下操作：

1. 设置以下注册表项：
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. 重启服务器

  
## <a name="prepare-your-on-premises-environment"></a>准备本地环境
若要为 Azure AD 应用程序代理准备你的环境，首先需要实现与 Azure 数据中心的通信。 如果路径中有防火墙，请确保防火墙已打开，使连接器能够向应用程序代理发出 HTTPS (TCP) 请求。

### <a name="open-ports"></a>打开端口

为**出站**流量打开以下端口： 

   | 端口号 | 用途 |
   | --- | --- |
   | 80 | 下载证书吊销列表 (Crl) 的同时验证 SSL 证书 |
   | 443 | 与应用程序代理服务进行所有出站通信 |

如果防火墙根据发起方用户实施流量控制，另请针对来自作为网络服务运行的 Windows 服务的流量打开端口 80 和 443。

如果你已在使用应用程序代理，则可能安装了旧版连接器。  请遵循本教程安装最新版本的连接器。 低于 1.5.132.0 的版本还需要打开以下端口：5671、8080、9090-9091、9350、9352、10100–10120。 

### <a name="allow-access-to-urls"></a>允许访问 URL

允许访问以下 URL：

| 代码 | 用途 |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | 连接器与应用程序代理云服务之间的通信 |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure 使用以下 URL 来验证证书 |
| login.windows.net<br>login.microsoftonline.com | 在注册过程中，连接器将使用这些 URL。 |

如果防火墙或代理允许执行 DNS 允许列表，可将与 \*.msappproxy.net 和 \*.servicebus.windows.net 的连接加入允许列表。 否则，需要允许访问 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 IP 范围每周更新。

## <a name="install-and-register-a-connector"></a>安装并注册连接器
若要使用应用程序代理，需在选择与应用程序代理服务配合使用的每个 Windows 服务器上安装一个连接器。 连接器是一个代理，可以管理从本地应用程序服务器到 Azure AD 中应用程序代理的出站连接。 可以在同时安装了其他身份验证代理（例如 Azure AD Connect）的服务器上安装连接器。

安装连接器：

1. 以使用应用程序代理的目录的应用程序管理员身份登录到 [Azure 门户](https://portal.azure.com/)。 例如，如果租户域为 contoso.com，则管理员应为 admin@contoso.com 或该域上的其他任何域别名。
2. 当前目录显示在右上角的用户名下。 验证是否已登录到使用应用程序代理的目录。 如果需要更改目录，则选择该图标。
3. 在左侧边栏选项卡中，依次单击“Azure Active Directory”、“应用程序代理”。
4. 单击“下载连接器服务”。
5. 阅读“服务条款”。  准备就绪后，单击“接受条款并下载”。
6. 窗口底部会显示有关下载 **AADApplicationProxyConnectorInstaller.exe** 的提示。 单击“运行”以安装该连接器。 此时会打开安装向导。 
7. 按照向导中的说明进行安装。 当系统提示将连接器注册到 Azure AD 租户使用的应用程序代理时，请提供应用程序管理员凭据。
    - 在 Internet Explorer (IE) 中，如果“IE 增强的安全配置”设置为“打开”，则可能不会显示注册屏幕。 若要访问，请按照错误消息中的说明进行操作。 确保“Internet Explorer 增强的安全性”设置为“关闭”。

### <a name="general-remarks"></a>一般备注

如果以前安装了连接器，请重新安装以获取最新版本。

如果选择对本地应用程序使用多个 Windows 服务器，则需要在每个服务器上安装并注册连接器。 可将连接器组织成连接器组。 有关详细信息，请参阅[连接器组](application-proxy-connector-groups.md)。 

如果你的组织使用代理服务器连接到 Internet，则需要为应用程序代理配置代理服务器。  有关详细信息，请参阅[使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)。 

有关连接器、容量规划以及它们如何保持最新状态的信息，请参阅[了解 Azure AD 应用程序代理连接器](application-proxy-connectors.md)。 

如果使用 Qlik Sense 应用程序，请始终安装最新的连接器。 Qlik Sense 使用 WebSocket，而只有 1.5.612.0 或更高版本的连接器才支持 WebSocket。


## <a name="verify-the-connector-installed-and-registered-correctly"></a>验证是否正确安装并注册了连接器

可以使用 Azure 门户或 Windows 服务器来确认是否正确安装了新连接器。

### <a name="verify---azure-portal"></a>验证 - Azure 门户
确认是否正确安装并注册了连接器：

1. 在 [Azure 门户](https://portal.azure.com)中登录到租户目录。
2. 依次单击“Azure Active Directory”、“应用程序代理”。 你的所有连接器和连接器组都显示在此页面上。 
3. 选择一个连接器以检查其详细信息。 活动的绿色标签表示相应的连接器可以连接到服务。 但是，即使标签是绿色的，网络问题也仍可能会阻止该连接器接收消息。 

    ![AzureAD 应用程序代理连接器](./media/application-proxy-connectors/app-proxy-connectors.png)

有关安装连接器的更多帮助，请参阅[安装应用程序代理连接器时出现问题](application-proxy-connector-installation-problem.md)。

### <a name="verify---windows-server"></a>验证 - Windows 服务器
确认是否正确安装并注册了连接器：

1. 单击“Windows”键并输入 *services.msc*，打开 Windows 服务管理器。
2. 检查以下两个服务的状态是否为“正在运行”。
   - **Microsoft AAD 应用程序代理连接器** 将启用连接
   - **Microsoft AAD 应用程序代理连接器更新程序**是一个自动的更新服务。 该更新程序会检查连接器的新版本并根据需要更新连接器。

    ![应用代理连接器服务 - 屏幕截图](./media/application-proxy-enable/app_proxy_services.png)

3. 如果这些服务的状态不是“正在运行”，请右键单击每个服务，然后选择“启动”。 

## <a name="add-an-on-premises-app-to-azure-ad"></a>将本地应用添加到 Azure AD

准备好环境并安装连接器后，可将本地应用程序添加到 Azure AD。  

1. 在 [Azure 门户](https://portal.azure.com/)中，以管理员身份登录。
2. 选择“Azure Active Directory” > “企业应用程序” > “新建应用程序”。

    ![添加企业应用程序](./media/application-proxy-publish-azure-portal/add-app.png)

3. 选择“全部”，然后选择“本地应用程序”。  

    ![添加自己的应用程序](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. 在“添加自己的本地应用程序”边栏选项卡中，提供有关应用程序的以下信息：

    ![配置应用程序](./media/application-proxy-publish-azure-portal/configure-app.png)

    | 字段 | 说明 |
    | :---- | :---------- |
    | **名称** | 显示在访问面板和 Azure 门户中的应用程序名称。 |
    | **内部 URL** | 用于从专用网络内部访问应用程序的 URL。 可以提供后端服务器上要发布的特定路径，而服务器的其余部分则不发布。 通过这种方式，可以在同一服务器上将不同站点发布为不同应用，并为每个站点提供其自己的名称和访问规则。<br><br>如果发布路径，请确保它包含应用程序的所有必要映像、脚本和样式表。 例如，如果你的应用位于 https://yourapp/app，并使用位于 https://yourapp/media 的映像，则应发布 https://yourapp/ 作为路径。 此内部 URL 不一定是用户看到的登陆页面。 有关详细信息，请参阅[为发布的应用设置自定义主页](application-proxy-configure-custom-home-page.md)。 |
    | **外部 URL** | 用户从网络外部访问应用时使用的地址。 如果不想使用默认的应用程序代理域，请参阅 [Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)。|
    | **预身份验证** | 应用程序代理在向用户授予应用程序访问权限之前如何验证用户。<br><br>**Azure Active Directory** - 应用程序代理重定向用户，让其使用 Azure AD 登录；这会验证他们对目录和应用程序的权限。 建议将此选项保留为默认值，以便可以利用条件性访问和多重身份验证等 Azure AD 安全功能。 必须在 **Azure Active Directory** 中使用 Microsoft 云应用程序安全性来监视应用程序。<br><br>**直通** - 用户无需对 Azure Active Directory 进行身份验证即可访问应用程序。 仍可在后端设置身份验证要求。 |
    | **连接器组** | 连接器处理对应用程序的远程访问，借助连接器组可按区域、网络或用途组织连接器和应用。 如果尚未创建任何连接器组，应用将分配到“默认”。<br><br>如果应用程序使用 WebSocket 进行连接，组中的所有连接器必须为 1.5.612.0 或更高版本。|

5. 根据需要配置**其他设置**。 对于大多数应用程序，应保留这些设置的默认状态。 

    | 字段 | 说明 |
    | :---- | :---------- |
    | **后端应用程序超时** | 仅当应用程序身份验证和连接速度较慢时，才将此值设置为“长”。 |
    | **使用仅限 HTTP 的 Cookie** | 将此值设置为“是”，使应用程序代理 Cookie 在 HTTP 响应标头中包含 HTTPOnly 标志。 如果使用远程桌面服务，请将此值设置为“否”。|
    | **使用安全 Cookie**| 将此值设置为“是”可通过安全通道（例如加密的 HTTPS 请求）传输 Cookie。
    | **使用永久性 Cookie**| 始终将此值设置为“否”。 此设置只应该用于不能在进程之间共享 Cookie 的应用程序。 有关 Cookie 设置的详细信息，请参阅 [Cookie settings for accessing on-premises applications in Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)（用于在 Azure Active Directory 中访问本地应用程序的 Cookie 设置）
    | **转换标头中的 URL** | 除非应用程序要求在身份验证请求中包含原始主机标头，否则请将此值保留为“是”。 |
    | **转换应用程序主体中的 URL** | 除非具有指向其他本地应用程序的硬编码 HTML 链接且不使用自定义域，否则请将此值保留为“否”。 有关详细信息，请参阅[使用应用程序代理进行链接转换](application-proxy-configure-hard-coded-link-translation.md)。<br><br>如果你打算使用 Microsoft 云应用安全性 (MCAS) 监视此应用程序，请将此值设置为“是”。 有关详细信息，请参阅[使用 Microsoft 云应用安全性和 Azure Active Directory 配置实时应用程序访问监视](application-proxy-integrate-with-microsoft-cloud-application-security.md) |
   


6. 选择 **添加** 。

## <a name="test-the-application"></a>测试应用程序

现在，可以测试是否正确添加了应用程序。 在以下步骤中，你要将一个用户帐户添加到应用程序，然后尝试登录。

### <a name="add-a-user-for-testing"></a>添加用于测试的用户
将用户添加到应用程序之前，请验证该用户帐户是否有权从企业网络内部访问应用程序。

添加测试用户：

1. 返回“快速启动”边栏选项卡，选择“分配用于测试的用户”。

    ![分配用于测试的用户](./media/application-proxy-publish-azure-portal/assign-user.png)

2. 在“用户和组”边栏选项卡中，选择“添加用户”。

    ![添加用户或组](./media/application-proxy-publish-azure-portal/add-user.png)

3. 在“添加分配”边栏选项卡中选择“用户和组”，然后选择要添加的帐户。 
4. 选择“分配”。

### <a name="test-the-sign-on"></a>测试登录

测试应用程序登录：

1. 在浏览器中，导航到在发布步骤中配置的外部 URL。 
2. 应会看到启动屏幕。
3. 尝试使用在上一部分创建的用户身份登录。

    ![测试已发布的应用](./media/application-proxy-publish-azure-portal/test-app.png)

有关故障排除信息，请参阅[根据错误消息排查应用程序代理问题](application-proxy-troubleshoot.md)。

## <a name="next-steps"></a>后续步骤
在本教程中，你已准备了要与应用程序代理配合使用的本地环境，然后安装并注册了应用程序代理连接器。 接下来，你已将一个应用程序添加到 Azure AD 租户。 你已验证某个用户是否可以使用 Azure AD 帐户登录到该应用程序。

完成了以下操作：
> [!div class="checklist"]
> * 打开出站流量的端口并允许访问特定的 URL
> * 在 Windows 服务器上安装连接器并将其注册到应用程序代理
> * 验证是否正确安装并注册了连接器
> * 将本地应用程序添加到 Azure AD 租户
> * 验证测试用户是否可以使用 Azure AD 帐户登录到该应用程序。

现在可以配置应用程序的单一登录。 使用以下链接选择单一登录方法，并查找单一登录教程。 

> [!div class="nextstepaction"]
>[配置单一登录](what-is-single-sign-on.md#choosing-a-single-sign-on-method)





