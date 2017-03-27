
---
title: "应用程序代理故障排除 | Microsoft 文档"
description: "介绍如何对 Azure AD 应用程序代理中的错误进行故障排除。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 970caafb-40b8-483c-bb46-c8b032a4fb74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 0051bbeaac458e8df07e5c7aa186fbefb526f50e
ms.lasthandoff: 03/15/2017

---


# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>应用程序代理问题和错误消息故障排除
如果在访问已发布应用程序或发布应用程序时出现错误，请检查以下选项，查看 Microsoft Azure AD 应用程序代理是否正确工作：

* 打开 Windows 服务控制台并验证 **Microsoft AAD 应用程序代理连接器**服务已启用并且正在运行。 另请查看应用程序代理服务属性页，如下图所示：  
  ![Microsoft AAD 应用程序代理连接器属性窗口屏幕截图](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)
* 打开事件查看器，然后在**应用程序和服务日志** > **Microsoft** > **AadApplicationProxy** > **Connector** > **Admin** 中查找应用程序代理连接器事件。
* 如果需要，可获取更详细的日志，方法是打开分析和调试日志，然后打开应用程序代理连接器会话日志。

有关 Azure AD 故障排除工具的详细信息，请参阅[用于验证连接器网络先决条件的故障排除工具](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/03/troubleshooting-tool-to-validate-connector-networking-prerequisites)。

## <a name="the-page-is-not-rendered-correctly"></a>页面未正确呈现
即使没有收到特定的错误消息，应用程序也可能存在绘制或运行不正常的问题。 如果发布了文章路径，但应用程序需要存在于该路径外的内容时，可能发生此情况。

例如，如果发布路径 https://yourapp/app，但应用程序调用 https://yourapp/media 中的图像，则这些图像无法呈现。 确保使用包含所有相关内容所需的最高级路径发布应用程序。 在本示例中为 http://yourapp/。

如果更改路径以包含引用内容，但仍然需要用户登陆路径中更深的链接，请参阅博客文章 [Setting the right link for Application Proxy applications in the Azure AD access panel and Office 365 app launcher](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/)（在 Azure AD 访问面板和 Office 365 应用启动器中为应用程序代理应用程序设置正确的链接）。

## <a name="connector-errors"></a>连接器错误

使用 [Azure AD 应用程序代理连接器端口测试工具](https://aadap-portcheck.connectorporttest.msappproxy.net/)验证连接器是否能够访问应用程序代理服务。 请至少确保美国中部区域和离你最近的区域有全部绿色复选标记。 绿色复选标记越多表示复原能力越强。 

如果注册在连接器向导安装期间失败，有两种方法可查看失败原因。 查看**应用程序和服务日志\Microsoft\AadApplicationProxy\Connector\Admin** 下的事件日志，或运行以下 Windows PowerShell 命令。

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

从事件日志找到连接器错误后，使用此常见错误列表解决问题：

- **连接器注册已失败：确保已在 Azure 管理门户中启用应用程序代理，并且已正确输入 Active Directory 用户名和密码。错误：“发生了一个或多个错误。”**

  如果关闭了注册窗口但没有登录到 Azure AD，请再次运行连接器向导并注册连接器。

  如果注册窗口打开后立即关闭，无法进行登录，则可能看到此错误。 当系统上存在网络错误时，可能出现此错误。 确保可从浏览器连接到公共网站，并且端口以[应用程序代理先决条件](active-directory-application-proxy-enable.md)中所指定的方式打开。

- **连接器注册已失败：确保计算机连接到 Internet。错误：“没有可接受消息的终结点在 https://connector.msappproxy.net:9090/register/RegisterConnector 处侦听。这通常由错误地址或 SOAP 操作导致。有关更多详细信息，请参阅 InnerException（如果存在）。”**

  如果使用 Azure AD 用户名和密码登录，但随后收到此错误，则可能是因为 8081 以上的所有端口都已被阻止。 确保所需的端口已打开。 有关详细信息，请参阅[应用程序代理先决条件](active-directory-application-proxy-enable.md)。

- **注册窗口中出现明确错误。无法继续**

  如果看到此错误，然后窗口关闭，这意味着输入的用户名或密码错误。 重试。 

- **连接器注册已失败：确保已在 Azure 管理门户中启用应用程序代理，并且已正确输入 Active Directory 用户名和密码。错误：AADSTS50059: 未在请求中找到或所提供的任何凭据均未暗示任何租户识别信息，并且服务主体 URI 的搜索已失败。** 

  正尝试使用 Microsoft 帐户登录，而不是使用作为正尝试登录的目录组织 ID 一部分的域进行登录。 确保管理员是租户域的相同域名的一部分，例如，如果 Azure AD 域为 contoso.com，则管理员应为 admin@contoso.com。 

- **未能检索运行 PowerShell 脚本的当前执行策略。** 

  如果连接器安装已失败，请检查确保 PowerShell 执行策略未禁用。 

  1. 打开组策略编辑器。 
  2. 依次转到“计算机配置” > “管理模板” > “Windows 组件” > **“Windows PowerShell”** ，然后双击“打开脚本执行”。 
  3. 可将执行策略设置为“未配置”或“已启用”。 如果设置为“已启用”，请确保在“选项”下将“执行策略”设置为“允许本地脚本和远程签名脚本”或“允许所有脚本”。 

- **连接器未能下载配置。** 

  用于身份验证的连接器客户端证书已过期。 如果将连接器安装在代理后，则可能发生此情况。 在此情况下，连接器无法访问 Internet，并且将无法向远程用户提供应用程序。 在 Windows PowerShell 中使用 `Register-AppProxyConnector` cmdlet 手动续订信任。 如果连接器在代理后，则必须向连接器帐户“网络服务”和“本地系统”授予 Internet 访问权限。 可通过向它们授予代理访问权限或将它们设置为绕过代理来实现此目的。 

- **连接器注册已失败：若要注册连接器，请确保你是 Active Directory 的全局管理员。错误：“注册请求被拒绝。”** 

  尝试登录时所使用的别名不是此域上的管理员。 始终为拥有用户域的目录安装连接器。 确保尝试登录时所使用的管理员帐户具有 Azure AD 租户的全局权限。 

## <a name="kerberos-errors"></a>Kerberos 错误

此列表包含 Kerberos 设置和配置中较常见的错误以及相关解决建议。

- **未能检索运行 PowerShell 脚本的当前执行策略。** 

  如果连接器安装已失败，请检查确保 PowerShell 执行策略未禁用。 

  1. 打开组策略编辑器。 
  2. 依次转到“计算机配置” > “管理模板” > “Windows 组件” > **“Windows PowerShell”** ，然后双击“打开脚本执行”。 
  3. 可将执行策略设置为“未配置”或“已启用”。 如果设置为“已启用”，请确保在“选项”下将“执行策略”设置为“允许本地脚本和远程签名脚本”或“允许所有脚本”。 

- **12008 - Azure AD 超出了对后端服务器的 Kerberos 身份验证尝试的最大允许次数。** 

  此错误可能表示 Azure AD 和后端应用程序服务器之间的配置不正确，或者两台计算机上的时间和日期配置存在问题。 后端服务器拒绝了由 Azure AD 创建的 Kerberos 票证。 验证 Azure AD 和后端应用程序服务器是否正确配置。 确保 Azure AD 和后端应用程序服务器上的时间和日期配置已同步。 

- **13016 - Azure AD 无法代表用户检索 Kerberos 票证，因为边缘令牌或访问 cookie 中没有 UPN。** 

  STS 配置出现问题。 在 STS 中修复 UPN 声明配置。 

- **13019 - Azure AD 由于以下常规 API 错误而无法代表用户检索 Kerberos 票证。** 

  此事件可能表示 Azure AD 和域控制器服务器之间的配置不正确，或者两台计算机上的时间和日期配置存在问题。 域控制器拒绝了由 Azure AD 创建的 Kerberos 票证。 验证 Azure AD 和后端应用程序服务器是否正确配置，尤其是 SPN 配置。 确保 Azure AD 加入与域控制器相同的域，以确保域控制器与 Azure AD 建立信任。 确保 Azure AD 和域控制器上的时间和日期配置已同步。 

- **13020 - Azure AD 无法代表用户检索 Kerberos 票证，因为后端服务器 SPN 未定义。** 

  此事件可能表示 Azure AD 和域控制器服务器之间的配置不正确，或者两台计算机上的时间和日期配置存在问题。 域控制器拒绝了由 Azure AD 创建的 Kerberos 票证。 验证 Azure AD 和后端应用程序服务器是否正确配置，尤其是 SPN 配置。 确保 Azure AD 加入与域控制器相同的域，以确保域控制器与 Azure AD 建立信任。 确保 Azure AD 和域控制器上的时间和日期配置已同步。 

- **13022 - Azure AD 无法对用户进行身份验证，因为后端服务器以 HTTP 401 错误响应 Kerberos 身份验证尝试。** 

  此事件可能表示 Azure AD 和后端应用程序服务器之间的配置不正确，或者两台计算机上的时间和日期配置存在问题。 后端服务器拒绝了由 Azure AD 创建的 Kerberos 票证。 验证 Azure AD 和后端应用程序服务器是否正确配置。 确保 Azure AD 和后端应用程序服务器上的时间和日期配置已同步。 

## <a name="end-user-errors"></a>最终用户遇到的错误

此列表包括最终用户尝试访问应用失败时可能遇到的错误。 

- **网站无法显示页面。** 

  如果应用程序是 IWA 应用程序，用户可能在尝试访问已发布的应用时得到此错误。 为此应用程序定义的 SPN 可能不正确。 对于 IWA 应用，确保为此应用程序配置的 SPN 正确。 

- **网站无法显示页面。** 

  如果应用程序是 OWA 应用程序，用户可能在尝试访问已发布的应用时得到此错误。 这可能由以下原因之一导致： 
  - 为此应用程序定义的 SPN 不正确。 确保针对此应用程序配置的 SPN 正确。
  - 尝试访问应用程序的用户正在使用 Microsoft 帐户（而不是正确的公司帐户）登录，或者该用户是来宾用户。 确保用户使用与已发布应用程序的域匹配的公司帐户登录。 Microsoft 帐户用户和来宾无法访问 IWA 应用程序。
  - 尝试访问应用程序的用户未针对本地端的此应用程序正确定义。 确保此用户在本地计算机上具有针对此后端应用程序定义的正确权限。

- **无法访问此公司应用。无权访问此应用程序。授权失败。确保向用户分配此应用程序的访问权限。** 

  如果用户使用 Microsoft 帐户而不是公司帐户登录，则用户可能在尝试访问已发布的应用时得到此错误。 来宾用户也可能得到此错误。 Microsoft 帐户用户和来宾无法访问 IWA 应用程序。 确保用户使用与已发布应用程序的域匹配的公司帐户登录。 

  可能未针对此应用程序分配用户。 转到“应用程序”选项卡，然后在“用户和组”下将此用户或用户组分配到此应用程序。

- **当前无法访问此公司应用。请稍后重试…连接器已超时。** 

  如果用户未在本地端针对此应用程序正确定义，则用户可能在尝试访问已发布的应用时得到此错误。 确保用户在本地计算机上具有针对此后端应用程序定义的正确权限。 

- **无法访问此公司应用。无权访问此应用程序。授权失败。确保用户具有 Azure Active Directory Premium 或 Basic 的许可证。**

  如果订阅方管理员未向用户显式分配 Premium/Basic 许可证，用户可能在尝试访问你发布的应用时遇到此错误。 转到订阅方的 Active Directory“许可证”选项卡并确保向此用户或用户组分配 Premium 或 Basic 许可证。

## <a name="my-error-wasnt-listed-here"></a>此处未列出我遇到的错误

如果你遇到的 Azure AD 应用程序代理的错误或问题未在此故障排除指南中列出，请告知我们。 请将遇到的错误的详细信息通过电子邮件发送到我们的[反馈团队](mailto:aadapfeedback@microsoft.com)。

## <a name="see-also"></a>另请参阅
* [启用 Azure Active Directory 的应用程序代理](active-directory-application-proxy-enable.md)
* [使用应用程序代理发布应用程序](active-directory-application-proxy-publish.md)
* [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)
* [启用条件性访问](active-directory-application-proxy-conditional-access.md)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png

