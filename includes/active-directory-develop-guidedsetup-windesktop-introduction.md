# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>从 Windows 桌面应用调用 Microsoft Graph API

本指南演示本机 Windows 桌面 .NET (XAML) 应用程序如何从 Azure Active Directory v2 终结点获取访问令牌并调用 Microsoft Graph API 或需要访问令牌的其他 API。

在本指南结束时，应用程序将能使用个人帐户（包括 outlook.com、live.com 和其他帐户）以及具有 Azure Active Directory 的任何公司或组织的工作和学校帐户调用受保护的 API。  

> 本指南需要 Visual Studio 2015 Update 3 或 Visual Studio 2017。  尚未安装？ [免费下载 Visual Studio 2017](https://www.visualstudio.com/downloads/)

### <a name="how-this-guide-works"></a>本指南的工作原理

![本指南的工作原理](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

本指南创建的示例应用程序使 Windows 桌面应用程序能够查询接受 Azure Active Directory v2 终结点令牌的 Microsoft Graph API 或 Web API。 在此方案中，通过 Authorization 标头向 HTTP 请求添加了令牌。 由 Microsoft 身份验证库 (MSAL) 获取和更新令牌。


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>负责获得用于访问受保护 Web API 的令牌

用户进行身份验证后，示例应用程序将收到一个令牌，可用于查询受 Microsoft Azure Active Directory v2 保护的 Microsoft Graph API 或 Web API。

Microsoft Graph 等 API 需要访问令牌才能允许访问特定资源 - 例如读取用户的个人资料、访问用户的日历或发送电子邮件。 应用程序可通过指定 API 作用域来请求使用 MSAL 的访问令牌，从而访问这些资源。 此访问令牌随后将添加到 HTTP Authorization 标头，在每次调用受保护资源时使用。 

MSAL 负责管理缓存和刷新访问令牌，因此应用程序无需执行这些任务。


### <a name="nuget-packages"></a>NuGet 包

本指南使用以下 NuGet 包：

|库|说明|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft 身份验证库 (MSAL)|

