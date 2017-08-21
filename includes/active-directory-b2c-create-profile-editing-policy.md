要在应用程序上启用配置文件编辑，需要创建配置文件编辑策略。 此策略描述了使用者在配置文件编辑过程中将要体验的内容以及应用程序在成功完成时会接收到的令牌内容。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

在设置的策略部分，选择“配置文件编辑策略”，然后单击“+ 添加”。

![选择配置文件编辑策略，然后单击“添加”按钮](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-policy.png)

输入供应用程序引用的策略名称。 例如，输入 `SiPe`。

选择“标识提供者”，然后选中“本地帐户登录”。 或者，也可以选择社交标识提供者（如果已配置）。 单击 **“确定”**。

![以标识提供者身份选择“本地帐户登录”，然后单击“确定”按钮](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-identity-providers.png)

选择“配置文件属性”。 选择使用者可以在其配置文件中查看和编辑的属性。 例如，选中“国家/地区”、“显示名称”和“邮政编码”。 单击 **“确定”**。

![选择某些属性，然后单击“确定”按钮](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-attributes.png)

选择“应用程序声明”。 选择需要在授权令牌中返回的声明，该令牌在你成功获得配置文件编辑体验后会发回到应用程序。 例如，选择“显示名称”、“邮政编码”。

![选择一些应用程序声明，然后单击“确定”按钮](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-application-claims.png)

单击“创建”添加策略。 策略被列为 B2C_1_SiPe。 B2C_1_ 前缀追加到名称。

通过选择“B2C_1_SiPe”打开策略。 验证表中指定的设置，然后单击“现在运行”。

![选择并运行策略](media/active-directory-b2c-create-profile-editing-policy/run-b2c-editing-policy.png)

| 设置      | 值  |
| ------------ | ------ |
| **应用程序** | Contoso B2C 应用 |
| 选择回复 url | `https://localhost:44316/` |

此时会打开新的浏览器选项卡，可以根据配置验证“配置文件编辑”用户体验。

> [!NOTE]
> 策略创建和更新最多需要一分钟才能生效。
>