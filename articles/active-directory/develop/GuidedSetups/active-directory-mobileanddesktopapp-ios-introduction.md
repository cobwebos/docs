
# <a name="call-the-microsoft-graph-api-from-an-ios-app"></a>从 iOS 应用调用 Microsoft Graph API

本指南演示本机 iOS 应用程序 (Swift) 如何从 Azure Active Directory v2 终结点获取访问令牌并调用 Microsoft Graph API 或其他需要访问令牌的 API。

在本指南结束时，应用程序将能使用个人帐户（包括 outlook.com、live.com 和其他帐户）以及具有 Azure Active Directory 的任何公司或组织的工作和学校帐户调用受保护的 API。

> ### <a name="pre-requisites"></a>先决条件
> - 本指南要求使用 XCode 8.x。 可从[此处](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 下载 URL")下载 XCode。
> - 用于包管理的 [Carthage](https://github.com/Carthage/Carthage)

### <a name="how-this-guide-works"></a>本指南的工作原理

![本指南的工作原理](media/active-directory-mobileanddesktopapp-ios-introduction/iosintro.png)

本指南创建的示例应用程序使 iOS 桌面应用程序能够查询接受 Azure Active Directory v2 终结点令牌的 Microsoft Graph API 或 Web API。 在此方案中，通过 Authorization 标头向 HTTP 请求添加了令牌。 由 Microsoft 身份验证库 (MSAL) 获取和更新令牌。


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>负责获得用于访问受保护 Web API 的令牌

用户进行身份验证后，示例应用程序将收到一个令牌，可用于查询受 Microsoft Azure Active Directory v2 保护的 Microsoft Graph API 或 Web API。

Microsoft Graph 等 API 需要访问令牌才能允许访问特定资源 - 例如读取用户的个人资料、访问用户的日历或发送电子邮件。 应用程序可通过指定 API 范围使用 MSAL 来请求访问令牌。 此访问令牌随后将添加到 HTTP Authorization 标头，在每次调用受保护资源时使用。

MSAL 负责管理缓存和刷新访问令牌，因此应用程序无需执行这些任务。


### <a name="nuget-packages"></a>NuGet 包

本指南使用以下 NuGet 包：

|库|说明|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|适用于 iOS 的 Microsoft 身份验证库预览|

