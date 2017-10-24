---
title: "Azure Active Directory B2C：用户迁移方法"
description: "介绍有关使用图形 API和选择性地使用 Azure AD B2C 自定义策略执行用户迁移的高级核心概念。"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/04/2017
ms.author: yoelh
ms.openlocfilehash: b102edd997e69fb3568780a42dfe93fc9a90e332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C：用户迁移
计划将标识提供者迁移到 Azure AD B2C 时，可能还需要迁移用户帐户。 本文介绍如何将现有用户帐户从任何标识提供者迁移到 Azure AD B2C。 本文并未阐述约定俗成的内容，而只是介绍了多种不同方法中的两种。  开发人员负责判断内容是否适用。

## <a name="user-migration-flows"></a>用户迁移流
Azure AD B2C 允许通过[图形 API](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) 迁移用户。 用户迁移过程划分为两个流：

* **预迁移** - 如果有权访问明文格式的用户凭据（用户名和密码），则适合使用此流。 或者适合凭据已加密，但可以将其解密的情况。 该过程涉及到从旧的标识提供者读取用户，并在 Azure AD B2C 目录中创建新帐户。

* **预迁移和密码重置** - 如果无法访问用户的密码，则适合使用此流。 例如：
    * 密码以 HASH 格式存储
    * 密码存储在你无权访问的标识提供者中。 旧标识提供者通过调用 Web 服务来验证用户凭据

在这两个流中，首先需要运行__预迁移__过程，从旧标识提供者读取用户，并在 Azure AD B2C 目录中创建新帐户。 如果没有密码，可以使用生成的随机密码创建帐户。 要求用户更改其密码。 或者，当用户首次登录时， B2C 要求用户重置密码。

## <a name="password-policy"></a>密码策略
本地帐户的 Azure AD B2C 密码策略基于 Azure AD 策略。 Azure AD B2C 的注册或登录和密码重置策略使用“强”密码强度，并且不会让任何密码过期。 有关详细信息，请阅读 [Azure AD 密码策略](https://msdn.microsoft.com/library/azure/jj943764.aspx)。

如果要迁移的帐户使用的密码强度低于 [Azure AD B2C 强制实施的强密码强度](https://msdn.microsoft.com/library/azure/jj943764.aspx)，可以禁用强密码要求。 若要更改默认密码策略，请将 `passwordPolicies` 属性设置为 `DisableStrongPassword`。 例如，可按如下所示修改创建用户请求： 

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-using-graph-api-to-migrate-users"></a>步骤 1：使用图形 API 迁移用户
通过图形 API 创建 Azure AD B2C 用户帐户（使用密码或随机密码）。 本部分介绍使用图形 API 在 Azure AD B2C 目录中创建用户帐户的过程。

### <a name="step-11-register-your-application-in-your-tenant"></a>步骤 1.1：在租户中注册应用程序
若要与图形 API 通信，首先需要创建具有管理特权的服务帐户。 在 Azure AD 中，可以注册应用程序并向 Azure AD 进行身份验证。 应用程序凭据为**应用程序 ID** 和**应用程序机密**。 应用程序代表自身而不是用户来调用图形 API。

首先，在 Azure AD 中注册迁移应用程序。 然后，创建应用程序密钥（应用程序机密）并使用适当的特权设置应用程序。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在页面右上角选择自己的帐户，以选择 Azure AD **B2C** 租户。
3. 在左侧面板中，单击“Azure Active Directory”（不是“Azure AD B2C”）。 可能需要选择“更多服务”才能找到它。
4. 选择“应用注册”。
5. 单击“新建应用程序注册”。

    ![新建应用程序注册](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
6. 遵照提示创建新的应用程序
    * 对于“名称”，请使用 **B2CUserMigratioin** 或喜好的其他任何名称。
    * 对于“应用程序类型”，使用“Web 应用/API”。
    * 对于“登录 URL”，请使用 https://localhost（因为它与此应用程序无关）。
    * 单击“创建” 。
7. 创建应用程序后，请从应用程序列表中选择新建的应用程序 **B2CUserMigratioin**。
选择“属性”，复制“应用程序 ID”并保存以备后用。

### <a name="step-12-create-application-secret"></a>步骤 1.2：创建应用程序机密
8. 在 Azure 门户的“已注册应用”中继续操作。 单击“密钥”并添加新密钥（也称为客户端机密）。 同样，请复制密钥以备后用。

    ![应用程序 ID 和密钥](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>步骤 1.3：向应用程序授予管理权限
9. 在 Azure 门户的“已注册应用”中继续操作
10. 单击“所需权限”。
11. 单击“Windows Azure Active Directory”。
12. 在“启用访问权限”中的“应用程序权限”下面，选择“读取和写入目录数据”权限，单击“保存”。
13. 最后，返回“所需权限”，单击“授予权限”按钮。

    ![应用程序权限](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

现已创建一个有权从 B2C 租户创建、读取和更新用户的应用程序。

### <a name="step-14-optional-environment-cleanup"></a>步骤 1.4：[可选] 清理环境
“读取和写入目录数据”权限不提供删除用户的能力。 如果想要为应用程序提供删除用户（以清理环境）的能力，则需要执行额外的步骤。 该步骤涉及到运行 PowerShell 来设置“用户帐户管理员”权限；否则，可以跳到下一部分。


> [!IMPORTANT]
> 需要使用 B2C 租户**本地**的 B2C 租户管理员帐户。 这些帐户如下所示：admin@contosob2c.onmicrosoft.com。

>[!NOTE]
> 以下 PowerShell 脚本需要 [Azure Active Directory PowerShell **版本 2**](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)。

在以下 PowerShell 脚本中：
* 连接到联机服务。 为此，请在 Windows PowerShell 命令提示符下运行 cmdlet `Connect-AzureAD` 并提供凭据。 
* 使用**应用程序 ID** 为应用程序分配用户帐户管理员角色。 这些角色具有已知的标识符，因此，我们要做的就是在脚本中输入自己的**应用程序 ID**。

```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

将 `$AppId` 值更改为自己的 Azure AD **应用程序 ID**

## <a name="step-2-pre-migration-application-sample"></a>步骤 2：预迁移应用程序示例
下载并运行示例代码。 可以[下载 .zip 文件格式的示例代码](http://www.github.com)。

### <a name="step-21-edit-the-migration-data-file"></a>步骤 2.1：编辑迁移数据文件
示例应用使用包含虚构用户数据的 JSON 文件。 成功运行示例后，请更改代码以使用自己数据库中的数据。 或者将用户配置文件导出到 JSON 文件，并将应用设置为使用该文件。
若要编辑 JSON 文件，请打开 `AADB2C.UserMigration.sln` Visual Studio 解决方案。 在 `AADB2C.UserMigration` 项目中打开 `UsersData.json` 文件。


![用户数据文件](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

可以看到，该文件包含用户实体的列表。 每个用户实体包含：
* email
* displayName
* firstName
* lastName
* 密码（不能为空）

> [!NOTE]
> 编译时，Visual Studio 会将该文件复制到 `bin` 目录。

### <a name="step-22-configure-the-application-settings"></a>步骤 2.2：配置应用程序设置
在 `AADB2C.UserMigration` 项目下，打开 App.config 文件。 将以下应用设置替换为自己的值：

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users data e.g. UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure Table string}" />
    
</appSettings>
```

> [!NOTE]
> * 后续部分会介绍 Azure 表连接字符串的用法
> * B2C 租户的名称是在创建租户期间输入的域，已显示在 Azure 门户中。 它的末尾通常带有后缀 `.onmicrosoft.com`，例如 `contosob2c.onmicrosoft.com`。
>

### <a name="step-23-run-the-pre-migration-process"></a>步骤 2.3：运行预迁移过程
右键单击 `AADB2C.UserMigration` 解决方案并重新生成示例。 如果成功，则现在 `AADB2C.UserMigration\bin\Debug` 中应该有一个可执行文件 `UserMigration.exe`。 若要运行迁移过程，请使用以下命令行参数之一：

* 若要**使用密码迁移用户**，请使用 `UserMigration.exe 1` 命令。

* 若要**使用随机密码迁移用户**，请使用 `UserMigration.exe 2` 命令。 此操作还会创建 Azure 表实体。 稍后可以配置策略来调用 REST API 服务。 该服务使用 Azure 表来跟踪和管理迁移过程。

![迁移过程演示](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>步骤 2.4：检查预迁移过程
若要检查结果，请在 Azure 门户中打开“Azure AD B2C”，并单击“用户和组”。 在搜索框中，键入某个用户的显示名称，并查看用户配置文件。 或者，可以使用此示例应用程序按**登录电子邮件地址**检索用户。 若要按登录电子邮件地址搜索用户，请运行以下命令

```Console
UserMigration.exe 3 {email address}
```

还可将输出保存到 JSON 文件，如下所示：
```Console
UserMigration.exe 3 {email address} >> UserProfile.json
```


使用所需的 JSON 编辑器打开 UserProfile.json 文件。 在 Visual Studio Code 中，可以使用 `Shift+Alt+F` 或上下文菜单中的“设置文档的格式”来设置 JSON 文档的格式。

![用户配置文件 JSON](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

还可以使用 `UserMigration.exe 4 <Display name>` 命令按**显示名称**检索用户

### <a name="step-25-optional-environment-cleanup"></a>步骤 2.5：[可选] 清理环境
若要清理 Azure AD 租户并从 Azure AD 目录中删除用户，请运行 `UserMigration.exe 5` 命令

> [!NOTE]
> * 若要清理租户，请为应用程序配置“用户帐户管理员”权限
> * 示例迁移应用会清理 JSON 文件中列出的所有用户

### <a name="step-26-sing-in-with-migrated-users-with-password"></a>步骤 2.6：以迁移用户的身份登录（使用密码）
使用用户密码运行预迁移过程后，帐户可供使用，并且用户能够使用 Azure AD B2C 登录到应用程序。 如果无权访问用户密码，请转到下一部分。

## <a name="step-3-password-reset"></a>步骤 3：密码重置
如果使用随机密码迁移了用户，则用户需要重置其密码。 若要重置密码，请执行以下操作：
* 发送一封欢迎电子邮件并在其中包含用于重置密码的链接
* [可选] 更改策略，以处理用户不重置密码并尝试登录的情况。 登录时，策略会检查迁移状态。 如果用户未更改密码，则引发友好的错误消息，要求用户单击“忘记密码了?”

    > [!NOTE]
    > 若要检查并更改用户迁移状态，必须使用自定义策略。 完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。
    >

### <a name="step-31-send-a-welcome-email-with-link-to-reset-password"></a>步骤 3.1：发送一封欢迎电子邮件并在其中包含用于重置密码的链接
若要获取密码重置策略的链接，请执行以下操作：

1.  打开“Azure AD B2C 设置”，接着打开“重置密码”策略属性。

2. 选择应用程序
    >[!NOTE]
    >
    >“立即运行”需要在租户中至少预先注册一个应用程序。 在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。  

2.  选择“立即运行”并检查策略
3.  **复制** URL 并将其发送给用户

    ![设置诊断日志](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-user-migration-status"></a>步骤 4：[可选] 更改策略以检查并设置用户迁移状态

当用户在未事先重置密码的情况下尝试登录时，策略应该返回友好的错误消息。 例如：“密码已过期，若要重置密码，请单击重置密码链接”。  此可选步骤要求根据[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中所述，运行使用自定义策略的 Azure AD B2C。

在本部分，我们将更改策略，以便在登录时检查迁移状态。 如果用户未更改密码，则返回 HTTP 409 错误消息，要求用户单击“忘记密码了?” 链接。 若要跟踪密码更改，可以使用 Azure 表。 结合命令行参数 `2` 运行预迁移过程，在 Azure 表中创建用户实体。 服务：

* 登录时，Azure AD B2C 策略会调用 Restful 迁移服务，并以输入声明的形式发送电子邮件。 该服务在 Azure 表中搜索电子邮件地址。 如果存在该地址，则引发“必须更改密码”错误消息。

* 重置密码时，在用户成功更改密码后，该服务会从 Azure 表中删除实体。


> [!NOTE]
>让我们使用 Azure 表来简化该示例。 可在任何数据库中存储迁移状态，或将其作为自定义属性存储在 Azure AD B2C 帐户中。

### <a name="41-application-settings"></a>步骤 4.1：应用程序设置
测试演示 Restful API。 在 Visual Studio 中打开 `AADB2C.UserMigration.sln` Visual Studio 解决方案。 在 `AADB2C.UserMigration.API` 项目中，打开文件 App.config。使用自己的值替换这三个应用设置：

```XML
<appSettings>
    <add key="BlobStorageConnectionString" value="{The Azure Blob storage connection string"} />
</appSettings>
```

### <a name="step-42-deploy-your-web-application-to-azure-app-services"></a>步骤 4.2：将 Web 应用程序部署到 Azure 应用服务
将 API 服务发布到 Azure 应用服务。 有关详细信息，请参阅：[将应用部署到 Azure 应用服务](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy)

### <a name="step-43-add-technical-profile-and-technical-profile-validation-to-your-policy"></a>步骤 4.3：将技术配置文件和技术配置文件验证添加到策略 
1.  从工作目录打开扩展策略文件 (TrustFrameworkExtensions.xml)。 
2. 找到 `<ClaimsProviders>` 节
3. 在 `ClaimsProviders` 元素下添加以下 XML 片段
4. 将 `ServiceUrl` 的值更改为指向终结点 URL 

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>

    <TechnicalProfile Id="LocalAccountSignIn">
        <DisplayName>Local account just in time migration</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
        </InputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountPasswordReset">
        <DisplayName>Local account just in time migration</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        </InputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>

<ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>

    <!-- This technical profile uses a validation technical profile to authenticate the user. -->
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountSignIn" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountWritePasswordUsingObjectId">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountPasswordReset" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

技术配置文件定义一个输入声明：`signInName`（作为电子邮件发送）。 登录时，声明会发送到 Restful 终结点。

为 Restful API 定义技术配置文件后，请让 Azure AD B2C 策略调用该技术配置文件。 XML 片段会重写基本策略中定义的 `SelfAsserted-LocalAccountSignin-Email`。 XML 片段还会添加 `ValidationTechnicalProfile`，其中包含指向技术配置文件 `LocalAccountUserMigration` 的 ReferenceId。 

### <a name="step-44-upload-the-policy-to-your-tenant"></a>步骤 4.4：将策略上传到租户
1.  在 [Azure 门户](https://portal.azure.com)中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，单击“Azure AD B2C”。
2.  选择“标识体验框架”。
3.  单击“所有策略”。
4.  选择“上传策略”
5.  选中“覆盖策略(若存在)”框。
6.  上传 TrustFrameworkExtensions.xml，并确保它能够通过验证
7.  重复最后一个步骤并上传 SignUpOrSignIn.xml

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>步骤 4.5：使用“立即运行”测试自定义策略
1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。
2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_signup_signin”。 选择“立即运行”。
3. 尝试使用某个已迁移的用户登录，并单击“登录”。 应会看到来自 Rest API 的以下错误消息。

    ![设置诊断日志](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshooting-your-rest-api"></a>步骤 4.6：[可选] 排查 REST API 问题
可以近乎实时地监视和查看日志记录信息。

1. 在 Restful 应用程序的设置菜单中，向下滚动到“监视”部分并单击“诊断日志”。 
2. 启用应用程序日志记录（文件系统） 
3. 将“级别”设置为“详细”级别
4. 单击“保存”

    ![设置诊断日志](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. 在设置菜单中，单击“日志流”
6. 检查 Restful API 的输出。

有关详细信息，请参阅：[流式传输日志和控制台](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-streaming-logs-and-console)

> [!IMPORTANT]
> 只能在开发和测试期间使用__诊断日志__。 RESTful API 输出可能包含不应在生产环境中公开的机密信息。
>

## <a name="download-the-complete-policy-files"></a>下载完整的策略文件
可选：建议在完成自定义策略入门演练后，使用你自己的自定义策略文件来构建方案，而不是使用这些示例文件。  [参考示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration)