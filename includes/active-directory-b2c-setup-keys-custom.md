> [!NOTE]
> 我们计划改进此体验，取消以下步骤。

### <a name="create-an-administrator-credential-in-the-azure-ad-b2c-tenant"></a>在 Azure AD B2C 租户中创建管理员凭据。

至于下一部分，你需要使用的凭据会使用 Azure AD B2C 租户的域。 为此，你需要使用此类凭据创建管理员帐户。 为此，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中，切换到 Azure AD B2C 租户的上下文，然后打开 Azure AD B2C 边栏选项卡。 [显示操作方法。](..\articles\active-directory-b2c\active-directory-b2c-navigate-to-b2c-context.md)
1. 选择“用户和组”。
1. 选择“所有用户”。
1. 单击“+ 新建用户”。
    * 设置 **Name** = `Admin`。
    * 设置 **Username** = `admin@{tenantName}.onmicrosoft.com`，其中 `{tenantName}` 是 Azure AD B2C 租户的名称。
1. 在“目录角色”下，选择“全局管理员”并点击“确定”。
1. 单击“创建”创建管理员用户。
1. 选中“显示密码”，对密码进行复制。

### <a name="set-up-the-key-container"></a>设置密钥容器

密钥容器用于存储密钥。 若要设置一个，请执行以下操作：

1. 打开新的 PowerShell 命令提示符。  一种方法是按 **Windows 徽标键 + R**，键入 `powershell`，然后按 Enter。
1. 下载该存储库以获取 PowerShell ExploreAdmin 工具。

    ```powershell
    git clone https://github.com/Azure-Samples/active-directory-b2c-advanced-policies
    ```

1. 切换到包含 ExploreAdmin 工具的文件夹。

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. 将 ExploreAdmin 工具导入 PowerShell。

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. 确认 `b2c_1a_TokenSigningKeyContainer` 尚不存在。  将 `{tenantName}` 替换为租户的名称。

    ```powershell
    Get-CpimKeyContainer -TenantId {tenantName}.onmicrosoft.com -StorageReferenceId b2c_1a_TokenSigningKeyContainer -ForceAuthenticationPrompt
    ```

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 显示登录提示时，请使用在上一部分创建的管理员帐户。

    b.保留“数据库类型”设置，即设置为“共享”。 出现提示时，必须输入电话号码才能设置多重身份验证。

    c. 出现提示时，请更改密码。

    d. **预期会出现错误!**  此错误会指出找不到 `b2c_1a_TokenSigningKeyContainer`。  如果没有错误（因为你已完成这些步骤），请跳过本部分的其余内容。

1. 创建 `b2c_1a_TokenSigningKeyContainer`。  将 `{tenantName}` 替换为租户的名称。

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_TokenSigningKeyContainer  b2c_1a_TokenSigningKeyContainer rsa 2048 0 0
    ```

1. 创建 `b2c_1a_TokenEncryptionKeyContainer`。  将 `{tenantName}` 替换为租户的名称。

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com b2c_1a_TokenEncryptionKeyContainer b2c_1a_TokenEncryptionKeyContainer rsa 2048 0 0
    ```

1. 创建 `b2c_1a_FacebookSecret`。  将 `{tenantName}` 替换为租户的名称。

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_FacebookSecret  b2c_1a_FacebookSecret rsa 2048 0 0
    ```
