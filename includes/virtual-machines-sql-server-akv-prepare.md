## <a name="prepare-for-akv-integration"></a>准备 AKV 集成
若要使用 Azure 密钥保管库集成配置 SQL Server VM，有多个先决条件： 

1. [安装 Azure Powershell](#install-azure-powershell)
2. [创建 Azure Active Directory](#create-an-azure-active-directory)
3. [创建密钥保管库](#create-a-key-vault)

以下各节描述了这些先决条件，以及你需要收集更高版本运行的 PowerShell cmdlet 的信息。

### <a name="install-azure-powershell"></a>安装 Azure PowerShell
请确保已安装最新的 Azure PowerShell SDK。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

### <a name="create-an-azure-active-directory"></a>创建 Azure Active Directory
首先，你需要具有[Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) 你的订阅中。 其中许多好处，这样，你可以授予对密钥保管库为特定用户和应用程序的权限。

接下来，向 AAD 注册应用程序。 这将为您的服务主体帐户有权访问 VM 所需的密钥保管库。 在 Azure 密钥保管库文章中，你可以找到这些步骤[与 Azure Active Directory 注册应用程序](../articles/key-vault/key-vault-get-started.md#register)部分中，也可以看到这些步骤中的屏幕快照以及**标识获取的应用程序部分**的[这篇博客文章](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx)。 之前完成这些步骤，请注意，你需要收集在之后在 SQL VM 上启用 Azure 密钥保管库集成时需要此注册过程中的以下信息。

* 添加应用程序后，查找**客户端 ID**上**配置**选项卡。 
    ![Azure Active Directory 客户端 ID](./media/virtual-machines-sql-server-akv-prepare/aad-client-id.png)
  
    客户端 ID 分配更高版本给**$spName** （服务主体名称） 参数在 PowerShell 脚本以启用 Azure 密钥保管库集成。 
* 此外，在这些步骤创建你的密钥时，将复制你的密钥的密钥如以下屏幕截图中所示。 此密钥密码分配更高版本给**$spSecret** （服务主体密钥） 中的 PowerShell 脚本的参数。  
    ![Azure Active Directory 密码](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)
* 必须授权此新的客户端 ID，才可具有以下访问权限：**加密**，**解密**， **wrapKey**， **unwrapKey**，**登录**，和**验证**。 这通过完成[Set-azurermkeyvaultaccesspolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx) cmdlet。 有关详细信息请参阅[授权应用程序使用密钥或机密](../articles/key-vault/key-vault-get-started.md#authorize)。

### <a name="create-a-key-vault"></a>创建密钥保管库
若要使用 Azure 密钥保管库存储将用于在 VM 中的加密密钥，你需要访问密钥保管库。 如果你具有尚未设置密钥保管库，创建一个按照中的步骤[Getting Started with Azure 密钥保管库](../articles/key-vault/key-vault-get-started.md)主题。 之前完成这些步骤，请注意，你需要在设置期间收集一些信息，则为更高版本时需要在 SQL VM 上启用 Azure 密钥保管库集成。

当您获取创建密钥保管库的步骤时，请注意返回**vaultUri**属性，它是密钥保管库 URL。 因此在该步骤中，如下所示，密钥保管库名称是 ContosoKeyVault，提供的示例在密钥保管库 URL 将是 https://contosokeyvault.vault.azure.net/。

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

密钥保管库 URL 分配更高版本给**$akvURL** PowerShell 脚本以启用 Azure 密钥保管库集成中的参数。

