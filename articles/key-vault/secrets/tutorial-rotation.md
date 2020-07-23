---
title: 使用一组身份验证凭据的资源的轮换教程
description: 通过本教程了解如何自动轮换使用一组身份验证凭据的资源的机密。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 9bff8c040f4cfed612278dd83ebb354b31a3a1f3
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801438"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>自动轮换使用一组身份验证凭据的资源的机密

向 Azure 服务进行身份验证的最佳方法是使用[托管标识](../general/managed-identity.md)，但某些情况下无法做到这一点。 在此类情况下，将使用访问密钥或机密。 应定期轮换访问密钥或机密。

本教程介绍如何定期自动轮换使用一组身份验证凭据的数据库和服务的机密。 具体而言，本教程将使用 Azure 事件网格通知触发的函数来轮换 Azure Key Vault 中存储的 SQL Server 密码：

![轮换解决方案示意图](../media/rotate1.png)

1. 在机密过期之前的 30 天，Key Vault 会向事件网格发布“即将过期”事件。
1. 事件网格会检查事件订阅，并使用 HTTP POST 调用已订阅该事件的函数应用终结点。
1. 函数应用接收机密信息，生成新的随机密码，然后在 Key Vault 中使用新密码为机密创建新的版本。
1. 函数应用使用新密码更新 SQL Server。

> [!NOTE]
> 步骤 3 与 4 之间可能会存在滞后现象。 在此期间，Key Vault 中的机密无法向 SQL Server 进行身份验证。 如果任一步骤失败，事件网格将重试两小时。

## <a name="create-a-key-vault-and-sql-server-instance"></a>创建密钥保管库和 SQL Server 实例

第一步是创建密钥保管库、SQL Server 实例和数据库，并将 SQL Server 管理员密码存储在 Key Vault 中。

本教程使用现有的 Azure 资源管理器模板来创建组件。 可在此处找到代码：[基本机密轮换模板示例](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates)。

1. 选择 Azure 模板部署链接：
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. 在“资源组”下，选择“新建”。 将组命名为 simplerotation。
1. 选择“购买”。

    ![创建资源组](../media/rotate2.png)

现在，你已获得一个密钥保管库、一个 SQL Server 实例和一个 SQL 数据库。 可以在 Azure CLI 中运行以下命令来验证此设置：

```azurecli
az resource list -o table
```

结果类似于以下输出：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>创建函数应用

接下来，创建一个使用系统托管标识的函数应用，以及其他所需组件。

该函数应用需要以下组件：
- 一个 Azure 应用服务计划
- 一个存储帐户
- 一个用于通过函数应用托管标识访问 Key Vault 中的机密的访问策略

1. 选择 Azure 模板部署链接：
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. 在“资源组”列表中选择“simplerotation”。 
1. 选择“购买”。

   ![选择“购买”](../media/rotate3.png)

完成上述步骤后，你将获得一个存储帐户、一个服务器场和一个函数应用。 可以在 Azure CLI 中运行以下命令来验证此设置：

```azurecli
az resource list -o table
```

结果类似于以下输出：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

有关如何创建函数应用和使用托管标识访问 Key Vault 的信息，请参阅[从 Azure 门户创建函数应用](../../azure-functions/functions-create-function-app-portal.md)和[使用托管标识提供 Key Vault 身份验证](../general/managed-identity.md)。

### <a name="rotation-function"></a>轮换函数
该函数使用事件通过更新 Key Vault 和 SQL 数据库来触发机密轮换。

#### <a name="function-trigger-event"></a>函数触发器事件

此函数读取事件数据并运行轮换逻辑：

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
此轮换方法从机密中读取数据库信息，创建机密的新版本，并使用新机密更新数据库：

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
可以在 [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function) 上找到完整代码。

#### <a name="function-deployment"></a>函数部署

1. 从 [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip) 下载函数应用 zip 文件。

1. 将 simplerotationsample-fn.zip 文件上传到 Azure Cloud Shell。

   ![上传文件](../media/rotate4.png)
1. 使用以下 Azure CLI 命令将 zip 文件部署到函数应用：

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

部署该函数后，应会在 simplerotation-fn 下看到两个函数：

![SimpleRotation 和 SimpleRotationHttpTest 函数](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>添加对 SecretNearExpiry 事件的事件订阅

复制函数应用的 `eventgrid_extension` 密钥：

   ![选择函数应用设置](../media/rotate6.png)

   ![eventgrid_extension 密钥](../media/rotate7.png)

在以下命令中使用复制的 `eventgrid_extension` 密钥和你的订阅 ID，创建对 `SecretNearExpiry` 事件的事件网格订阅：

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>将机密添加到 Key Vault
设置访问策略，以向用户授予“管理机密”权限：

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

创建一个新机密，该机密的标记包含 SQL 数据库数据源和用户 ID。 包含一个设置为明天的过期日期。

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

创建过期时间较短的机密会立即发布 `SecretNearExpiry` 事件，而该事件又会触发函数来轮换该机密。

## <a name="test-and-verify"></a>测试和验证
几分钟后，`sqluser` 机密应会自动轮换。

若要验证该机密是否已轮换，请转到“Key Vault” > “机密”： 

![转到“机密”](../media/rotate8.png)

打开“sqluser”机密并查看原始版本和轮换后的版本：

![打开 sqluser 机密](../media/rotate9.png)

### <a name="create-a-web-app"></a>创建 Web 应用

若要验证 SQL 凭据，请创建一个 Web 应用。 此 Web 应用将从 Key Vault 获取机密，从机密中提取 SQL 数据库信息和凭据，然后测试与 SQL Server 的连接。

该 Web 应用需要以下组件：
- 一个使用系统托管标识的 Web 应用
- 一个用于通过 Web 应用托管标识访问 Key Vault 中的机密的访问策略

1. 选择 Azure 模板部署链接：
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. 选择“simplerotation”资源组。
1. 选择“购买”。

### <a name="deploy-the-web-app"></a>部署 Web 应用

可以在 [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp) 上找到该 Web 应用的源代码。

若要部署 Web 应用，请完成以下步骤：

1. 从 [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip) 下载函数应用 zip 文件。
1. 将 simplerotationsample-app.zip 文件上传到 Azure Cloud Shell。
1. 使用以下 Azure CLI 命令将 zip 文件部署到函数应用：

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>打开 Web 应用

转到部署的应用程序并选择 URL：
 
![选择 URL](../media/rotate10.png)

当应用程序在浏览器中打开时，你会看到“生成的机密值”，并会看到“数据库已连接”的值为“true”。

## <a name="learn-more"></a>了解详细信息

- 概述：[通过 Azure 事件网格监视 Key Vault（预览版）](../general/event-grid-overview.md)
- 如何：[Key Vault 机密发生更改时接收电子邮件](../general/event-grid-logicapps.md)
- [Azure Key Vault 的 Azure 事件网格事件架构（预览版）](../../event-grid/event-schema-key-vault.md)
