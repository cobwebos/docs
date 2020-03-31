---
title: 单用户/密码轮换教程
description: 参考本教程自动轮换用于单用户/密码身份验证的机密
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223354"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>自动轮换使用单用户/密码身份验证的资源的机密

尽管对 Azure 服务进行身份验证的最佳方法是使用[托管标识](managed-identity.md)，但某些情况下无法做到这一点。 在这种情况下，将使用访问密钥或机密。 应定期轮换访问密钥或机密。

本教程演示如何定期自动轮换使用单用户/密码身份验证的数据库和服务的机密。 具体而言，此方案将使用事件网格通知触发的函数，来轮换 Key Vault 中存储的 SQL 服务器密码：

![轮换示意图](./media/rotate1.png)

1. 在机密过期之前的 30 天，Key Vault 会向事件网格发布“即将过期”事件。
1. 事件网格将检查事件订阅，并使用 HTTP POST 调用订阅此事件的函数应用终结点。
1. 函数应用接收机密信息，生成新的随机密码，然后在 Key Vault 中使用新密码为机密创建新的版本。
1. 函数应用使用新密码更新 SQL。

> [!NOTE]
> 步骤 3 与 4 之间可能会出现滞后时间，在此期间，Key Vault 中的机密尚未生效，无法用于对 SQL 进行身份验证。 如果任一步骤失败，事件网格将重试 2 小时。

## <a name="setup"></a>设置

## <a name="create-a-key-vault-and-sql-server"></a>创建 Key Vault 和 SQL 服务器

在开始之前，必须先创建 Key Vault、创建 SQL 服务器和数据库，并将 SQL 服务器管理员密码存储在 Key Vault 中。

本教程使用预先创建的 Azure 资源管理器模板来创建组件。 可在以下位置找到完整代码：[基本机密轮换模板示例](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates)。

1. 单击 Azure 模板部署链接：
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. 对于“资源组”，请选择“新建”并将资源组命名为“simplerotation”。
1. 选择“购买”。

    ![新建资源组](./media/rotate2.png)

完成这些步骤后，你将获得一个 Key Vault、一个 SQL 服务器和一个 SQL 数据库。 可以在 Azure CLI 终端中运行以下命令进行确认：

```azurecli
az resource list -o table
```

结果将如下所示：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>创建函数应用

创建使用系统托管标识的函数应用和其他所需组件： 

函数应用需要以下组件和配置：
- 应用服务计划
- 存储帐户
- 用于通过函数应用托管标识访问 Key Vault 中的机密的访问策略

1. 单击 Azure 模板部署链接：
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. 对于“资源组”，请选择“simplerotation”。
1. 选择“购买”。

   ![“购买”屏幕](./media/rotate3.png)

完成上述步骤后，你将获得一个存储帐户、一个服务器场和一个函数应用。  可以在 Azure CLI 终端中运行以下命令进行确认：

```azurecli
az resource list -o table
```

结果将如下所示：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

有关如何创建函数应用和使用托管标识访问 Key Vault 的信息，请参阅[从 Azure 门户创建函数应用](../azure-functions/functions-create-function-app-portal.md)和[使用托管标识提供 Key Vault 身份验证](managed-identity.md)

### <a name="rotation-function-and-deployment"></a>轮换函数和部署
函数使用事件作为触发器，并执行机密轮换来更新 Key Vault 和 SQL 数据库。

#### <a name="function-event-trigger-handler"></a>函数事件触发器处理程序

以下函数读取事件数据并执行轮换逻辑

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>机密轮换逻辑
此轮换方法从机密中读取数据库信息，创建机密的新版本，并使用新机密更新数据库。

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
可在以下位置找到完整代码： https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>函数部署

1. 下载函数应用 zip 文件： https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. 将 simplerotationsample-fn.zip 文件上传到 Azure Cloud Shell。
 
1. 使用以下 CLI 命令将 zip 文件部署到函数应用：

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![“购买”屏幕](./media/rotate4.png)

部署完成后，应会在 simplerotation-fn 下面看到两个函数：

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>添加对“SecretNearExpiry”事件的事件订阅

复制函数应用 eventgrid_extension 密钥。

![Azure Cloud Shell](./media/rotate6.png)

![测试和验证](./media/rotate7.png)

在以下命令中使用复制的 eventgrid_extension 密钥和你的订阅 ID，创建对 SecretNearExpiry 事件的事件网格订阅。

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>将机密添加到 Key Vault
设置访问策略，以向用户授予“管理机密”权限。

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

现在创建一个新机密，该机密的标记包含 SQL 数据库数据源和用户 ID，并将过期日期设置为明天。

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

创建过期时间较短的机密会立即发布 SecretNearExpiry 事件，而该事件又会触发函数来轮换该机密。

### <a name="test-and-verify"></a>测试和验证
几分钟后，sqluser 机密应会自动轮换。

若要确认机密轮换的验证结果，请转到“Key Vault”>“机密”

![测试和验证](./media/rotate8.png)

打开“sqluser”机密并查看原始版本和轮换后的版本

![测试和验证](./media/rotate9.png)

## <a name="create-web-app"></a>创建 Web 应用

若要验证 SQL 凭据，请创建一个 Web 应用程序。 此 Web 应用程序将从 Key Vault 获取密钥，从机密中提取 SQL 数据库信息和凭据，然后测试与 SQL 的连接。

该 Web 应用需要以下组件和配置：
- 使用系统托管标识的 Web 应用
- 用于通过 Web 应用托管标识访问 Key Vault 中的机密的访问策略

1. 单击 Azure 模板部署链接：
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. 选择“simplerotation”资源组 
1. 单击“购买”

### <a name="deploy-web-app"></a>部署 Web 应用

可在 https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp 中找到该 Web 应用的源代码。若要部署 Web 应用，请执行以下操作：

1. 从 https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip 下载函数应用 zip 文件
1. 将文件 `simplerotationsample-app.zip` 上传到 Azure Cloud Shell。
1. 使用以下 Azure CLI 命令将 zip 文件部署到函数应用：

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>打开 Web 应用程序

转到部署的应用程序，然后单击“URL”：
 
![测试和验证](./media/rotate10.png)

此时应显示生成的机密值，其 Database Connected 值为 true。

## <a name="learn-more"></a>了解详细信息：

- 概述：[通过 Azure 事件网格监视 Key Vault（预览版）](event-grid-overview.md)
- 如何：[Key Vault 机密发生更改时接收电子邮件](event-grid-logicapps.md)
- [Azure Key Vault 的 Azure 事件网格事件架构（预览版）](../event-grid/event-schema-key-vault.md)
