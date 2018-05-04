---
title: 将 Azure Web 应用程序配置为从 Key Vault 读取机密 | Microsoft Docs
description: 教程：将 ASP.Net Core 应用程序配置为从 Key Vault 读取机密
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 04/16/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 4a765b314b9879877bb6ff926e4a6584456b7823
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>教程：将 Azure Web 应用程序配置为从 Key Vault 读取机密

本教程演练将 Azure Web 应用程序配置为使用托管服务标识从 Key Vault 读取信息所要执行的步骤。 学习如何：

> [!div class="checklist"]
> * 创建 Key Vault。
> * 在 Key Vault 中存储机密。
> * 创建 Azure Web 应用程序。
> * 启用托管服务标识
> * 授予所需的权限，让应用程序从 Key Vault 读取数据。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

若要使用 CLI 登录到 Azure，可以键入：

```azurecli
az login
```

## <a name="create-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli
az group create --name ContosoResourceGroup --location eastus
```

刚刚创建的资源组将在整篇教程中使用。

## <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault

接下来，在上一步骤创建的资源组中创建 Key Vault。 必须提供某些信息：

>[!NOTE]
> 必须对 Key Vault 使用唯一的名称。整篇教程使用“ContosoKeyVault”作为 Key Vault 的名称。

* 保管库名称 **ContosoKeyVault**。
* 资源组名称 **ContosoResourceGroup**。
* 位置“美国东部”。

```azurecli
az keyvault create --name '<YourKeyVaultName>' --resource-group ContosoResourceGroup --location eastus
```

此命令的输出显示新建的 Key Vault 的属性。 请记下下面列出的两个属性：

* **保管库名称**：在本示例中为 **ContosoKeyVault**。 将在所有 Key Vault 命令中使用该 Key Vault 名称。
* **保管库 URI**：在本示例中为 https://<YourKeyVaultName>.vault.azure.net/。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

>[!IMPORTANT]
> 如果收到错误“参数 'vault_name' 必须符合以下模式: '^[a-zA-Z0-9-]{3,24}$'”，则表示 -name 参数值不唯一，或者不符合字符串必须由字母数字字符构成且长度为 3 到 24 个字符这一要求。

目前，只有你的 Azure 帐户才有权对这个新保管库执行任何操作。

## <a name="add-a-secret-to-key-vault"></a>向 Key Vault 添加机密

我们将添加一个机密，以帮助演示工作原理。 可以存储需要安全保存的，但同时也要提供给应用程序使用的 SQL 连接字符串或其他任何信息。 在本教程中，密码名为 **AppSecret**，将存储 **MySecret** 的值。

键入以下命令，在 Key Vault 中创建名为 **AppSecret** 的机密，用于存储 **MySecret** 值：

```azurecli
az keyvault secret set --vault-name '<YourKeyVaultName>' --name 'AppSecret' --value 'MySecret'
```

若要查看机密中包含的纯文本形式的值，请执行以下命令：

```azurecli
az keyvault secret show --name 'AppSecret' --vault-name '<YourKeyVaultName>'
```

此命令显示机密信息，包括 URI。 完成这些步骤后，Azure Key Vault 中应会出现某个机密的 URI。 请记下此信息， 后面的步骤需要用到。

## <a name="create-a-web-app"></a>创建 Web 应用

在本部分，我们将创建一个 ASP.NET MVC 应用程序，并将其作为 Web 应用部署到 Azure 中。 有关 Azure Web 应用的详细信息，请参阅 [Web 应用概述](../app-service/app-service-web-overview.md)。

1. 在 Visual Studio 中，通过依次选择“文件”>“新建”>“项目”创建项目。 

2. 在“新建项目”对话框中，选择“Visual C#”>“Web”>“ASP.NET Core Web 应用程序”。

3. 将该应用程序命名为 **WebKeyVault**，然后选择“确定”。
   >[!IMPORTANT]
   > 必须将该应用命名为 WebKeyVault，使复制并粘贴的代码与命名空间匹配。 如果为站点指定其他名称，则需要修改代码，以匹配站点名称。

    ![“新建 ASP.NET 项目”对话框](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. 可将任何类型的 ASP.NET Core Web 应用部署到 Azure。 在本教程中，请选择“Web 应用程序”模板，并确保将身份验证设置为“无身份验证”。

    ![ASPNET 无身份验证对话框](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. 选择“确定”。

6. 创建 ASP.NET Core 项目后，将显示 ASP.NET Core 欢迎页，其中提供了大量有助于入门的资源链接。

7. 在菜单中，选择“调试>启动但不调试”以在本地运行 Web 应用。

## <a name="modify-the-web-app"></a>修改 Web 应用

需要在 Web 应用程序中安装两个 NuGet 包。 若要安装这些包，请遵循以下步骤：

1. 在解决方案资源管理器中，右键单击网站名称。
2. 选择“管理解决方案的 NuGet 包...”。
3. 选中搜索框旁边的复选框。 **包括预发行版**
4. 搜索下面列出的两个 NuGet 包，并确认将其添加到解决方案：

    * [Microsoft.Azure.Services.AppAuthentication (预览版)](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) - 方便提取服务到 Azure 服务身份验证方案的访问令牌。 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/2.4.0-preview) - 包含用来与 Key Vault 交互的方法。

5. 使用解决方案资源管理器打开 `Program.cs`，将 Program.cs 文件的内容替换为以下代码。 将 ```<YourKeyVaultName>``` 替换为 Key Vault 的名称：

    ```csharp
    
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureKeyVault;
    
        namespace WebKeyVault
        {
        public class Program
        {
        public static void Main(string[] args)
        {
        BuildWebHost(args).Run();
        }
    
            public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                }
             )
                .UseStartup<Startup>()
                .Build();
    
            private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
        }
        }
    ```

6. 使用解决方案资源管理器导航到“页面”部分并打开 `About.cshtml`。 将 **About.cshtml.cs** 的内容替换为以下代码：

    ```csharp
    
    using Microsoft.AspNetCore.Mvc.RazorPages;
    using Microsoft.Extensions.Configuration;
    
    namespace WebKeyVault.Pages
    {
        public class AboutModel : PageModel
        {
            public AboutModel(IConfiguration configuration)
            {
                _configuration = configuration;
            }
    
            private readonly IConfiguration _configuration = null;
            public string Message { get; set; }
    
            public void OnGet()
            {
                Message = "My key val = " +  _configuration["AppSecret"];
            }
        }
    }
    
    ```

7. 在主菜单中，选择“调试” > “开始执行(不调试)”。 显示浏览器时，导航到“关于”页。 此时将显示 AppSecret 的值。

>[!IMPORTANT]
> 如果收到“HTTP 错误 502.5 - 进程失败”消息，请检查 `Program.cs` 中指定的 Key Vault 名称

## <a name="publish-the-web-application-to-azure"></a>将 Web 应用程序发布到 Azure

1. 在编辑器上方，选择“WebKeyVault”。
2. 选择“发布”。
3. 再次选择“发布”。
4. 选择“创建”。

>[!IMPORTANT]
> 此时会打开一个浏览器窗口，其中显示了“502.5 - 进程失败”消息。 这是正常情况。 需要向应用程序标识授予从 Key Vault 读取机密的权限。

## <a name="enable-managed-service-identity"></a>启用托管服务标识

虽然 Azure Key Vault 可用于安全存储凭据以及其他密钥和机密，但代码需要通过 Key Vault 的身份验证才能检索它们。 托管服务标识 (MSI) 为 Azure 服务提供了 Azure Active Directory (Azure AD) 中的自动托管标识，更巧妙地解决了这个问题。 此标识可用于通过支持 Azure AD 身份验证的任何服务（包括 Key Vault）的身份验证，这样就无需在代码中插入任何凭据了。

1. 返回 Azure CLI
2. 运行 assign-identity 命令，为此应用程序创建标识：

```azurecli
az webapp assign-identity --name WebKeyVault --resource-group ContosoResourcegroup
```

>[!NOTE]
>这相当于转到门户并在 Web 应用程序属性中将“托管服务标识”切换为“打开”。

## <a name="grant-rights-to-the-application-identity"></a>向应用程序标识授予权限

使用 Azure 门户，转到 Key Vault 的访问策略，并为自己授予对 Key Vault 的机密管理访问权限。 这样，便可以在本地开发计算机上运行应用程序。

1. 在 Azure 门户上的“搜索资源”对话框中搜索自己的 Key Vault。
2. 选择“访问策略”。
3. 选择“新增”，在“机密权限”部分选择“获取”和“列出”。
4. 选择“选择主体”，并添加应用程序标识。 该标识的名称与应用程序相同。
5. 选择“确定”

现在，Azure 中的帐户和应用程序标识都有权从 Key Vault 读取信息。 如果刷新页面，应会看到站点的登陆页。 如果选择“关于”， 会看到 Key Vault 中存储的值。

## <a name="clean-up-resources"></a>清理资源

若要删除资源组及其所有资源，请使用 **az group delete** 命令。

  ```azurecli
  az group delete -n ContosoResourceGroup
  ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Key Vault 开发人员指南](key-vault-developers-guide.md)
