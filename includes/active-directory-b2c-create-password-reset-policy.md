若要在应用程序上启用细化密码重置，请使用**密码重置**策略。 请注意，[此处](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md)指定租户范围的密码重置选项。 此策略描述了客户在密码重置过程中将经历的体验以及应用程序在成功完成时会接收到的令牌内容。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

在设置的策略部分，选择“密码重置策略”，然后单击“+ 添加”。

![选择注册或登录策略，然后单击“添加”按钮](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-policy.png)

输入供应用程序引用的策略名称。 例如，输入 `SSPR`。

选择“标识提供者”，然后单击“使用电子邮件地址重置密码”。 单击“确定”。

![以标识提供者身份选择“使用电子邮件地址重置密码”，然后单击“确定”按钮](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

选择“应用程序声明”。 选择需要在授权令牌中返回的声明，该令牌在你成功获得密码重置体验后会发回到应用程序。 例如，选择“用户的对象 ID”。

![选择一些应用程序声明，然后单击“确定”按钮](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

单击“创建”添加策略。 策略被列为 B2C_1_SSPR。 B2C_1_ 前缀追加到名称。

通过选择“B2C_1_SSPR”打开策略。 验证表中指定的设置，然后单击“现在运行”。

![选择并运行策略](media/active-directory-b2c-create-password-reset-policy/run-b2c-password-reset-policy.png)

| 设置      | 值  |
| ------------ | ------ |
| **应用程序** | Contoso B2C 应用 |
| 选择回复 url | `https://localhost:44316/` |

打开新的浏览器选项卡后，即可在应用程序中验证密码重置使用者体验。

> [!NOTE]
> 策略创建和更新最多需要一分钟才能生效。
>
