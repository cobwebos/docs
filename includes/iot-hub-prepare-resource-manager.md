## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>准备 Azure 资源管理器请求进行身份验证
必须进行身份验证使用的资源执行的所有操作[Azure 资源管理器][ lnk-authenticate-arm]与 Azure Active Directory (AD)。 对此进行配置的最简单方法是使用 PowerShell 或 Azure CLI。

安装[Azure PowerShell cmdlet] [ lnk-powershell-install]在继续操作之前。

以下步骤显示如何使用 PowerShell 的 AD 应用程序的密码身份验证设置。 你可以在标准的 PowerShell 会话中运行这些命令。

1. 登录到 Azure 订阅使用以下命令：

    ```powershell
    Login-AzureRmAccount
    ```

1. 如果你有多个 Azure 订阅，登录到 Azure 授予访问权限与凭据关联的所有 Azure 订阅。 使用以下命令列出可供你使用的 Azure 订阅：

    ```powershell
    Get-AzureRMSubscription
    ```

    使用以下命令来选择你想要用于运行命令，以管理你的 IoT 中心的订阅。 从上一命令的输出，可以使用的订阅名称或 ID:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. 记下你**TenantId**和**SubscriptionId**。 稍后需要它们。
3. 创建新的 Azure Active Directory 应用程序使用以下命令，并替换占位符：
   
   * **{显示名称}:**如应用程序的显示名称**MySampleApp**
   * **{主页 URL}:**如应用程序主页的 URL **http://mysampleapp/home**。 此 URL 不需要为指向实际的应用程序。
   * **{应用程序标识符}:**如的唯一标识符**http://mysampleapp**。 此 URL 不需要为指向实际的应用程序。
   * **{Password}:**使用进行身份验证它与你的应用密码。
     
     ```powershell
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
     ```
4. 记下**ApplicationId**你创建的应用程序。 需要此更高版本。
5. 创建新的服务主体使用以下命令、 替换**{MyApplicationId}**与**ApplicationId**从前面的步骤：
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. 设置角色分配，使用以下命令、 替换**{MyApplicationId}**与你**ApplicationId**。
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

你现在已完成创建 Azure AD 应用程序，可用于从你的自定义 C# 应用程序进行身份验证。 在本教程后面，你需要以下值：

* TenantId
* SubscriptionId
* ApplicationId
* Password

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
