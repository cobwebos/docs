[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

若要注册 Web API，请使用表中指定的设置。

![新 Web API 的注册设置示例](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| 设置      | 示例值  | 说明                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Name** | Contoso B2C API | 输入应用程序的“名称”，用于向使用者描述 API。 | 
| 包括 Web 应用/Web API | 是 | 对于 Web API，请选择“是”。 |
| 允许隐式流 | 是 | 如应用程序使用 [OpenID Connect 登录](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md)，请选择“是” |
| 回复 URL | `https://localhost:44316/` | 回复 URL 是 Azure AD B2C 在其中返回应用程序请求的任何令牌的终结点。 输入[合适](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url)的回复 URL。 在此示例中，Web API 为本地应用，在端口 44316 上侦听。 |
| 应用 ID URI | api | App ID URI 是用于 Web API 的标识符。 包括域在内的完整标识符 URI 是为你生成的。 |

单击“ **创建** ”以注册应用程序。

新注册的应用程序显示在 B2C 租户的应用程序列表中。 从列表中选择 Web API。 此时会显示 API 的属性窗格。

![Web API 属性](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

请记下全局唯一的应用程序客户端 ID。 该 ID 用在应用程序代码中。