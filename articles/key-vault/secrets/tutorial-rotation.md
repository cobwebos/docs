---
title: 资源的轮替教程，其中一组身份验证凭据存储在 Azure Key Vault 中
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
ms.openlocfilehash: b61ba7f160d012cc3d9ad9f477e969a626fdc38e
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541413"
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

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure Key Vault
* SQL Server

如果没有现成的 Key Vault 和 SQL Server，可以使用以下部署链接：

[![图像显示标记为“部署到 Azure”的按钮。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. 在“资源组”下，选择“新建”。 将组命名为“akvrotation”。
1. 在“Sql 管理员登录名”下，键入 Sql 管理员登录名。 
1. 选择“查看 + 创建”。
1. 选择“创建”

    ![创建资源组](../media/rotate2.png)

现在，你已拥有一个 Key Vault 和一个 SQL Server 实例。 可以在 Azure CLI 中运行以下命令来验证此设置：

```azurecli
az resource list -o table
```

结果类似于以下输出：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv          akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>创建和部署 sql server 密码轮替函数

接下来，创建一个使用系统托管标识的函数应用以及其他所需组件，并部署 sql server 密码轮替函数

该函数应用需要以下组件：
- 一个 Azure 应用服务计划
- 一个包含具有事件触发器和 http 触发器的 sql 密码轮替函数的函数应用 
- 进行函数应用触发器管理时所需的存储帐户
- 一个用于访问 Key Vault 中机密的函数应用标识的访问策略
- “SecretNearExpiry”事件的 EventGrid 事件订阅

1. 选择 Azure 模板部署链接： 

   [![图像显示标记为“部署到 Azure”的按钮。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. 在“资源组”列表中选择“akvrotation” 。
1. 在“Sql Server 名称”中，键入密码需进行轮替的 Sql Server 名称
1. 在“密钥保管库名称”中，键入密钥保管库名称
1. 在“函数应用名称”中，键入函数应用名称
1. 在“机密名称”中，键入将在其中存储密码的机密名称
1. 在“存储库 Url”中，键入函数代码 GitHub 位置 (https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp.git ) 
1. 选择“查看 + 创建”。
1. 选择“创建”。

   ![选择“查看 + 创建”](../media/rotate3.png)

完成上述步骤后，你将获得一个存储帐户、一个服务器场和一个函数应用。 可以在 Azure CLI 中运行以下命令来验证此设置：

```azurecli
az resource list -o table
```

结果类似于以下输出：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

有关如何创建函数应用和使用托管标识访问 Key Vault 的信息，请参阅[从 Azure 门户创建函数应用](../../azure-functions/functions-create-function-app-portal.md)和[使用托管标识提供 Key Vault 身份验证](../general/managed-identity.md)。

### <a name="rotation-function"></a>轮换函数
在先前步骤中部署的函数使用事件通过更新 Key Vault 和 SQL 数据库来触发机密轮替。 

#### <a name="function-trigger-event"></a>函数触发器事件

此函数读取事件数据并运行轮换逻辑：

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>机密轮换逻辑
此轮换方法从机密中读取数据库信息，创建机密的新版本，并使用新机密更新数据库：

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
可以在 [GitHub](https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp) 上找到完整代码。

## <a name="add-the-secret-to-key-vault"></a>将机密添加到 Key Vault
设置访问策略，以向用户授予“管理机密”权限：

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

使用包含 SQL Server 资源 ID、SQL Server 登录名和机密有效期（以天为单位）的标记创建一个新机密。 提供机密名称和 SQL 数据库中的初始密码（在我们的示例中为“Simple123”），并包含一个设置为明天的到期日期。

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

创建到期日期较短的机密会在 15 分钟内发布 `SecretNearExpiry` 事件，而该事件又会触发函数来轮换该机密。

## <a name="test-and-verify"></a>测试和验证

若要验证该机密是否已轮换，请转到“Key Vault” > “机密”：

![转到“机密”](../media/rotate8.png)

打开“sqlPassword”机密并查看原始版本和轮换后的版本：

![打开 sqluser 机密](../media/rotate9.png)

### <a name="create-a-web-app"></a>创建 Web 应用

若要验证 SQL 凭据，请创建一个 Web 应用。 此 Web 应用将从 Key Vault 获取机密，从机密中提取 SQL 数据库信息和凭据，然后测试与 SQL Server 的连接。

该 Web 应用需要以下组件：
- 一个使用系统托管标识的 Web 应用
- 一个用于通过 Web 应用托管标识访问 Key Vault 中的机密的访问策略

1. 选择 Azure 模板部署链接： 

   [![图像显示标记为“部署到 Azure”的按钮。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmaster%2Farm-templates%2FWeb-App%2Fazuredeploy.json)

1. 选择“akvrotation”资源组。
1. 在“Sql Server 名称”中，键入密码需进行轮替的 Sql Server 名称
1. 在“密钥保管库名称”中，键入密钥保管库名称
1. 在“机密名称”中，键入将在其中存储密码的机密名称
1. 在“存储库 Url”中，键入 Web 应用代码 GitHub 位置 (https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git ) 
1. 选择“查看 + 创建”。
1. 选择“创建”。


### <a name="open-the-web-app"></a>打开 Web 应用

转到部署的应用程序 URL：
 
https://akvrotation-app.azurewebsites.net/

当应用程序在浏览器中打开时，你会看到“生成的机密值”，并会看到“数据库已连接”的值为“true”。

## <a name="learn-more"></a>了解详细信息

- 教程：[具有两组凭据的资源的轮替](tutorial-rotation-dual.md)
- 概述：[通过 Azure 事件网格监视 Key Vault（预览版）](../general/event-grid-overview.md)
- 如何：[Key Vault 机密发生更改时接收电子邮件](../general/event-grid-logicapps.md)
- [Azure Key Vault 的 Azure 事件网格事件架构（预览版）](../../event-grid/event-schema-key-vault.md)
