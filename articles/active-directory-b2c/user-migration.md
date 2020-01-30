---
title: 用户迁移方法
titleSuffix: Azure AD B2C
description: 讨论使用 Azure AD 图形 API 的用户迁移的核心和高级概念，并选择性地使用 Azure AD B2C 自定义策略。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6c70ab718235a67d9f039df638f8ba90b21ab15b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847831"
---
# <a name="migrate-users-to-azure-active-directory-b2c"></a>将用户迁移到 Azure Active Directory B2C

将标识提供者迁移到 Azure Active Directory B2C （Azure AD B2C）时，可能还需要迁移用户帐户。 本文介绍如何将现有用户帐户从任何标识提供者迁移到 Azure AD B2C。 本文不是规范的，而是介绍了几种方案。 开发人员负责判断每种方法是否适用。

## <a name="user-migration-flows"></a>用户迁移流

使用 Azure AD B2C，可以通过[Azure AD 图形 API][B2C-GraphQuickStart]迁移用户。 用户迁移过程划分为两个流：

- **预迁移**：如果对用户凭据（用户名和密码）拥有明确的访问权限，或者凭据已加密但可将其解密，则适合使用此流。 预迁移过程涉及到从旧的标识提供者读取用户，并在 Azure AD B2C 目录中创建新帐户。

- **预迁移和密码重置**：如果无法访问用户的密码，则适合使用此流。 例如：
  - 密码以 HASH 格式存储。
  - 密码存储在你无权访问的标识提供者中。 旧标识提供者通过调用 Web 服务来验证用户凭据。

在这两个流中，首先需要运行预迁移过程，从旧标识提供者读取用户，并在 Azure AD B2C 目录中创建新帐户。 如果没有密码，可以使用随机生成的密码创建帐户。 然后要求用户更改密码，或者，当用户首次登录时，Azure AD B2C 会要求用户重置密码。

## <a name="password-policy"></a>密码策略

本地帐户的 Azure AD B2C 密码策略基于 Azure AD 策略。 Azure AD B2C 注册或登录和密码重置策略使用 "强" 密码强度，并且不会使任何密码过期。 有关详细信息，请参阅 [Azure AD 密码策略][AD-PasswordPolicies]。

如果要迁移的帐户使用的密码强度弱于 [Azure AD B2C 强制实施的强密码强度][AD-PasswordPolicies]，可以禁用强密码要求。 若要更改默认密码策略，请将 `passwordPolicies` 属性设置为 `DisableStrongPassword`。 例如，可按如下所示修改创建用户请求：

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>步骤 1：使用 Azure AD 图形 API 迁移用户

通过图形 API 创建 Azure AD B2C 用户帐户（使用密码或随机密码）。 本部分介绍使用图形 API 在 Azure AD B2C 目录中创建用户帐户的过程。

### <a name="step-11-register-your-application-in-your-tenant"></a>步骤 1.1：在租户中注册应用程序

若要与图形 API 通信，首先必须创建具有管理特权的服务帐户。 在 Azure AD 中，你将注册应用程序并对目录启用写入访问权限。 应用程序凭据为**应用程序 ID**和**应用程序机密**。 应用程序代表自身而不是用户来调用图形 API。

首先，注册一个可用于管理任务的应用程序，例如用户迁移。

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>步骤1.2：向应用程序授予管理权限

接下来，向应用程序授予 Azure AD 写入目录所需图形 API 权限。

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>步骤1.3：创建应用程序机密

创建用于稍后步骤中配置的用户迁移应用程序的客户端机密（密钥）。

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

现在，你的应用程序具有在 Azure AD B2C 租户中创建、读取和更新用户的权限。

### <a name="step-14-optional-environment-cleanup"></a>步骤 1.4：（可选）清理环境

"*读取和写入目录数据*" 权限*不*包括删除用户的权限。 如果想要为应用程序提供删除用户（以清理环境）的功能，必须执行额外的步骤，这涉及到运行 PowerShell 来设置“用户帐户管理员”权限。 否则，可以跳到下一部分。

> [!IMPORTANT]
> 必须使用 B2C 租户本地的 B2C 租户管理员帐户。 帐户名称语法为*管理员\@contosob2c.onmicrosoft.com*。

在需要[Azure AD Powershell V2 模块][AD-Powershell]的此 PowerShell 脚本中，请执行以下操作：

1. 连接到联机服务。 为此，请在 Windows PowerShell 命令提示符下运行 cmdlet `Connect-AzureAD` 并提供凭据。

1. 使用**应用程序 ID** 为应用程序分配用户帐户管理员角色。 这些角色具有已知的标识符，因此，我们要做的就是在脚本中输入自己的**应用程序 ID**。

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

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

将 `$AppId` 值更改为自己的 Azure AD **应用程序 ID**。

## <a name="step-2-pre-migration-application-sample"></a>步骤 2：预迁移应用程序示例

可在社区维护的 `azure-ad-b2c/user-migration` GitHub 存储库中找到预迁移代码示例：

[azure-ad-b2c/用户迁移/预迁移][UserMigrationSample-code]（GitHub）

### <a name="step-21-edit-the-migration-data-file"></a>步骤 2.1：编辑迁移数据文件

示例应用使用包含虚构用户数据的 JSON 文件。 成功运行示例后，可以更改代码以使用自己数据库中的数据。 或者，可将用户配置文件导出到 JSON 文件，然后将应用设置为使用该文件。

若要编辑 JSON 文件，请打开 `AADB2C.UserMigration.sln` Visual Studio 解决方案。 在 `AADB2C.UserMigration` 项目中打开 `UsersData.json` 文件。

![显示两个用户的 JSON 块的 UsersData 文件部分](./media/user-migration/pre-migration-data-file.png)

可以看到，该文件包含用户实体的列表。 每个用户实体包含以下属性：

- 电子邮件
- displayName
- firstName
- lastName
- 密码（不能为空）

> [!NOTE]
> 编译时，Visual Studio 会将该文件复制到 `bin` 目录。

### <a name="step-22-configure-the-application-settings"></a>步骤 2.2：配置应用程序设置

在 `AADB2C.UserMigration` 项目下，打开 *App.config* 文件。 将以下应用设置替换为自己的值：

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - 后续部分会介绍 Azure 表连接字符串的用法。
> - B2C 租户的名称是在创建租户期间输入的域，已显示在 Azure 门户中。 租户名称通常以后缀 *.onmicrosoft.com* 结尾（例如 *contosob2c.onmicrosoft.com*）。

### <a name="step-23-run-the-pre-migration-process"></a>步骤 2.3：运行预迁移过程

右键单击 `AADB2C.UserMigration` 解决方案并重新生成示例。 如果成功，则现在 `AADB2C.UserMigration\bin\Debug\net461` 中应该有一个可执行文件 `UserMigration.exe`。 若要运行迁移过程，请使用以下命令行参数之一：

- 若要**使用密码迁移用户**，请使用 `UserMigration.exe 1` 命令。

- 若要**使用随机密码迁移用户**，请使用 `UserMigration.exe 2` 命令。 此操作还会创建 Azure 表实体。 稍后可以配置策略来调用 REST API 服务。 该服务使用 Azure 表来跟踪和管理迁移过程。

![显示 UserMigration 命令输出的命令提示符窗口](./media/user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>步骤 2.4：检查预迁移过程

若要验证迁移，请使用以下两种方法之一：

- 若要按显示名称搜索用户，请使用 Azure 门户：

   1. 打开**Azure AD B2C**，然后选择 "**用户**"。
   1. 在搜索框中，键入用户的显示名称，并查看该用户的配置文件。

- 若要通过登录电子邮件地址检索用户，请使用示例应用程序：

   1. 运行以下命令：

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > 还可以使用以下命令按显示名称检索用户：`UserMigration.exe 4 "<Display name>"`。

   1. 在 JSON 编辑器中打开 UserProfile.json 文件以查看用户信息。

      ![在 Visual Studio Code 编辑器中打开 UserProfile 文件](./media/user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>步骤 2.5：（可选）清理环境

若要清理 Azure AD 租户并从 Azure AD 目录中删除用户，请运行 `UserMigration.exe 5` 命令。

> [!NOTE]
> * 若要清理租户，请为应用程序配置“用户帐户管理员”权限。
> * 示例迁移应用会清理 JSON 文件中列出的所有用户。

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>步骤 2.6：以迁移用户的身份登录（使用密码）

使用用户密码运行预迁移过程后，帐户可供使用，并且用户能够使用 Azure AD B2C 登录到应用程序。 如果无权访问用户密码，请转到下一部分。

## <a name="step-3-help-users-reset-their-password"></a>步骤 3：帮助用户重置其密码

如果使用随机密码迁移了用户，则用户必须重置其密码。 若要帮助用户重置密码，请发送一封欢迎电子邮件并在其中包含用于重置密码的链接。

若要获取密码重置策略的链接，请执行以下步骤。 此过程假定你以前创建了密码重置[自定义策略](custom-policy-get-started.md)。

1. 使用[Azure 门户](https://portal.azure.com)右上部分的 "**目录 + 订阅**" 筛选器，选择包含 Azure AD B2C 租户的目录。
1. 选择左侧菜单中的 "Azure AD B2C"，或从 "**所有服务**" 中选择 " "。
1. 在 "**策略**" 下，选择 "**标识体验框架**"。
1. 选择 "密码重置策略"。 例如， *B2C_1A_PasswordReset*。
1. 在 "**选择应用程序**" 下拉菜单中选择应用程序。

    > [!NOTE]
    > "**立即运行**" 需要在租户中注册至少一个应用程序。 若要了解如何注册应用程序，请参阅[教程：在 Azure Active Directory B2C 中注册应用程序][B2C-AppRegister]。

1. 复制 "**立即运行终结点**" 文本框中显示的 URL，然后将其发送给用户。

    ![突出显示 "立即运行" 终结点的密码重置策略页](./media/user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>步骤 4：（可选）更改策略以检查并设置用户迁移状态

> [!NOTE]
> 若要检查并更改用户迁移状态，必须使用自定义策略。 必须完成[自定义策略入门][B2C-GetStartedCustom]中的设置说明。

当用户在未事先重置密码的情况下尝试登录时，策略应该返回友好的错误消息。 例如：

> *您的密码已过期。要重置密码，请选择 "重置密码" 链接。*

此可选步骤要求根据[自定义策略入门][B2C-GetStartedCustom]一文中所述使用 Azure AD B2C 自定义策略。

在本部分，我们将更改策略，以便在登录时检查用户迁移状态。 如果用户未更改密码，则返回 HTTP 409 错误消息，要求用户选择“忘记密码了?”链接。

若要跟踪密码更改，可以使用 Azure 表。 结合命令行参数 `2` 运行预迁移过程时，会在 Azure 表中创建一个用户实体。 服务执行以下操作：

- 登录时，Azure AD B2C 策略会调用 RESTful 迁移服务，并以输入声明的形式发送电子邮件。 服务在 Azure 表中搜索电子邮件地址。 如果存在该地址，服务会引发错误消息：“必须更改密码”。

- 在用户成功更改密码后，服务会从 Azure 表中删除该实体。

> [!NOTE]
> 让我们使用 Azure 表来简化该示例。 可在任何数据库中存储迁移状态，或将其作为自定义属性存储在 Azure AD B2C 帐户中。

### <a name="41-update-your-application-setting"></a>步骤 4.1：更新应用程序设置

1. 若要测试 RESTful API 演示，请在 Visual Studio 中打开 `AADB2C.UserMigration.sln`。
1. 在 `AADB2C.UserMigration.API` 项目中，打开 web.config*文件。* 将设置替换为[步骤 2.2](#step-22-configure-the-application-settings) 中配置的设置：

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>步骤 4.2：将 Web 应用程序部署到 Azure 应用服务

在解决方案资源管理器中，右键单击 `AADB2C.UserMigration.API`，然后选择“发布...”。 按照说明发布到 Azure 应用服务。 有关详细信息，请参阅[将应用部署到 Azure 应用服务][AppService-Deploy]。

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>步骤 4.3：将技术配置文件和技术配置文件验证添加到策略

1. 在解决方案资源管理器中，展开“解决方案项目”，然后打开 TrustFrameworkExtensions.xml 策略文件。
1. 将 `yourtenant.onmicrosoft.com` 中的 `TenantId`、`PublicPolicyUri` 和 `<TenantId>` 字段更改为租户的名称。
1. 在 `<TechnicalProfile Id="login-NonInteractive">` 元素下，将 `ProxyIdentityExperienceFrameworkAppId` 和 `IdentityExperienceFrameworkAppId` 的所有实例替换为[自定义策略][B2C-GetStartedCustom]入门中配置的应用程序 id。
1. 在 `<ClaimsProviders>` 节点下，找到以下 XML 代码片段。 将 `ServiceUrl` 的值更改为指向 Azure 应用服务 URL。

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
    ```

上述技术配置文件定义一个输入声明：`signInName`（作为电子邮件发送）。 登录时，该声明会发送到 RESTful 终结点。

为 RESTful API 定义技术配置文件后，请将现有 `SelfAsserted-LocalAccountSignin-Email` 技术配置文件配置为在*trustframeworkextensions.xml*文件中进行重写，以便另外调用你的 REST API 技术配置文件：

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="LocalAccountUserMigration" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

然后，将 `LocalAccountSignIn` 技术配置文件的 `Id` 更改为 "`LocalAccountUserMigration`"。

### <a name="step-44-upload-the-policy-to-your-tenant"></a>步骤 4.4：将策略上传到租户

1. 在 [Azure 门户][Portal]中，切换到[你的 Azure AD B2C 租户的上下文][B2C-NavContext]，然后选择“Azure AD B2C”。
1. 选择“标识体验框架”。
1. 选择“所有策略”。
1. 选择“上传策略”。
1. 选中“覆盖策略(如果存在)”复选框。
1. 上传 *TrustFrameworkExtensions.xml* 文件，并确保它能够通过验证。

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>步骤 4.5：使用“立即运行”测试自定义策略

1. 选择 " **Azure AD B2C**"，然后选择 "**标识体验框架**"。
1. 打开已上传的信赖方 (RP) 自定义策略 *B2C_1A_signup_signin*，然后选择“立即运行”。
1. 输入某个已迁移用户的凭据，然后选择 "**登录**"。 REST API 应会引发以下错误消息：

    ![显示 "更改密码" 错误消息的登录注册页](./media/user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>步骤 4.6：（可选）排查 REST API 问题

可以近乎实时地查看和监视日志记录信息。

1. 在 RESTful 应用程序的设置菜单中的“监视”下，选择“诊断日志”。
1. 将“应用程序日志记录(文件系统)”设置为“打开”。
1. 将“级别”设置为“详细”。
1. 选择“保存”

    ![Azure 门户中的 "诊断日志" 配置页](./media/user-migration/pre-migration-diagnostic-logs.png)

1. 在“设置”菜单中，选择“日志流”。
1. 检查 RESTful API 的输出。

> [!IMPORTANT]
> 只能在开发和测试期间使用诊断日志。 RESTful API 输出可能包含不应在生产环境中公开的机密信息。

## <a name="optional-download-the-complete-policy-files"></a>（可选）下载完整的策略文件

完成[自定义策略入门][B2C-GetStartedCustom]演练后，建议使用自己的自定义策略文件来构建方案。 对于参考，我们提供了[示例策略文件][UserMigrationSample-policy]。

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
