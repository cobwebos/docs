# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>从 JavaScript 单页面应用程序 (SPA) 调用 Microsoft Graph API

本指南演示 JavaScript 单页面应用程序 (SPA) 如何从 Azure Active Directory v2 终结点登录个人、工作和学校帐户，获取访问令牌以及调用 Microsoft Graph API 或其他需要访问令牌的 API。

### <a name="how-this-guide-works"></a>本指南的工作原理

![本指南生成的示例应用的工作原理](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>更多信息

本指南创建的示例应用程序允许 JavaScript SPA 查询接受 Azure Active Directory v2 终结点令牌的 Microsoft Graph API 或 Web API。 此方案中，用户登录后请求了访问令牌，并通过授权标头将其添加到 HTTP 请求。 由 Microsoft 身份验证库 (MSAL) 获取和更新令牌。

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>库

本指南使用以下库：

|库|说明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|适用于 JavaScript 的 Microsoft 身份验证库预览|

> [!NOTE]
> msal.js 面向 Azure Active Directory v2 终结点，这使得个人、学校和工作帐户能够登录和获取令牌。 Azure Active Directory v2 终结点具有[某些限制](..\articles\active-directory\develop\active-directory-v2-limitations.md)。 如果只关注学校和工作帐户，请使用 adal.js 和 V1 终结点。 要了解 v1 和 v2 终结点之间的差异，请参阅 [v1-v2 比较](..\articles\active-directory\develop\active-directory-v2-compare.md)。

<!--end-collapse-->
