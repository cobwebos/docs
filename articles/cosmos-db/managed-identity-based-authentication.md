---
title: 如何使用系统分配的托管标识访问 Azure Cosmos DB 数据
description: 了解如何配置 Azure Active Directory (Azure AD) 系统分配的托管标识（托管服务标识），以访问 Azure Cosmos DB 中的密钥。
author: j-patrick
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 07bfaabf051a016ca9617245ba8628ef6c7e80c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91566612"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>使用系统分配的托管标识访问 Azure Cosmos DB 数据

在本文中，你将设置一个无论是否轮换密钥都能可靠运行的解决方案，以使用[托管标识](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)访问 Azure Cosmos DB 密钥  。 本文中的示例使用 Azure Functions，但你可以使用任何支持托管标识的服务。 

本文将介绍如何创建一个无需复制任何 Azure Cosmos DB 密钥即可访问 Azure Cosmos DB 数据的函数应用。 该函数应用每分钟唤醒一次，记录水族鱼缸的当前温度。 若要了解如何设置计时器触发的函数应用，请参阅[在 Azure 中创建由计时器触发的函数](../azure-functions/functions-create-scheduled-function.md)一文。

为了简化该方案，已配置了 [生存时间](./time-to-live.md) 设置，以清理较旧的温度文档。 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>向函数应用分配系统分配的托管标识

此步骤向函数应用分配系统分配的托管标识。

1. 在 [Azure 门户](https://portal.azure.com/)中，打开“Azure 函数”窗格并转到你的函数应用  。 

1. 打开“平台功能” > “标识”选项卡   ： 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="显示了函数应用的“平台功能”和“标识”选项的屏幕截图。":::

1. 在“标识”选项卡上，开启系统标识的“状态”并选择“保存”     。 “标识”窗格应如下所示  ：  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="显示了函数应用的“平台功能”和“标识”选项的屏幕截图。":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>向 Azure Cosmos 帐户授予访问权限

此步骤向函数应用的系统分配的托管标识分配角色。 Azure Cosmos DB 具有多个内置角色，可将这些角色分配到托管标识。 对于此解决方案，你将使用以下两个角色：

|内置角色  |说明  |
|---------|---------|
|[DocumentDB 帐户参与者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可管理 Azure Cosmos DB 帐户。 允许检索读取/写入密钥。 |
|[Cosmos DB 帐户读者角色](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以读取 Azure Cosmos DB 帐户数据。 允许检索读取密钥。 |

> [!IMPORTANT]
> Azure Cosmos DB 中对基于角色的访问控制的支持仅适用于控制平面操作。 通过主键或资源令牌保护数据平面操作。 有关详细信息，请参阅[保护对数据的访问](secure-access-to-data.md)一文。

> [!TIP] 
> 分配角色时，请仅分配所需的访问权限。 如果服务只需读取数据，请向托管标识分配“Cosmos DB 帐户读取者”角色  。 有关最低权限访问权限的重要性的详细信息，请参阅 [特权帐户的低公开部分](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) 。

在此方案中，函数应用将读取水族箱的温度，然后将此数据写回到 Azure Cosmos DB 中的容器。 由于函数应用必须写入数据，因此你需要分配“DocumentDB 帐户参与者”角色  。 

### <a name="assign-the-role-using-azure-portal"></a>使用 Azure 门户分配角色

1. 登录到 Azure 门户并转到你的 Azure Cosmos DB 帐户。 依次打开“访问控制(IAM)”窗格和“角色分配”选项卡   ：

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="显示了函数应用的“平台功能”和“标识”选项的屏幕截图。":::

1. 选择“+ 添加”   >   “添加角色分配”。

1. “添加角色分配”面板会在右侧打开  ：

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="显示了函数应用的“平台功能”和“标识”选项的屏幕截图。":::

   * **角色**：选择“DocumentDB 帐户参与者” 
   * **将访问权限分配到**：在“选择系统分配的托管标识”子部分下，选择“函数应用”   。
   * **选择**：窗格中将会填充你的订阅中具有托管系统标识的所有函数应用  。 在本例中，请选择“FishTankTemperatureService”函数应用  ： 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="显示了函数应用的“平台功能”和“标识”选项的屏幕截图。":::

1. 选择函数应用后，选择“保存”  。

### <a name="assign-the-role-using-azure-cli"></a>使用 Azure CLI 分配角色

若要使用 Azure CLI 分配角色，请打开 Azure Cloud Shell 并运行以下命令：

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>以编程方式访问 Azure Cosmos DB 密钥

现在，我们已有一个函数应用，它具有系统分配的托管标识，该标识具有“DocumentDB 帐户参与者”角色和 Azure Cosmos DB 权限  。 以下函数应用代码将获取 Azure Cosmos DB 密钥，创建 CosmosClient 对象，获取水族箱温度，然后将此数据保存到 Azure Cosmos DB。

此示例使用[“列出密钥”API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) 来访问 Azure Cosmos DB 帐户密钥。

> [!IMPORTANT] 
> 若要[分配 Cosmos DB 帐户读取者](#grant-access-to-your-azure-cosmos-account)角色，需要使用[列出只读密钥 API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys)。 这只会填充只读密钥。

“列出密钥”API 将返回 `DatabaseAccountListKeysResult` 对象。 C# 库中未定义此类型。 以下代码显示了此类的实现：  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

该示例还使用了名为“TemperatureRecord”的简单文档，该文档的定义如下：

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

你将使用 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 库来获取系统分配的托管标识令牌。 若要了解获取令牌的其他方法以及有关 `Microsoft.Azure.Service.AppAuthentication` 库的详细信息，请参阅[服务到服务身份验证](../key-vault/general/service-to-service-authentication.md)一文。


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

现在，你已准备好[部署函数应用](../azure-functions/functions-create-first-function-vs-code.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Azure Cosmos DB 和 Azure Active Directory 进行基于证书的身份验证](certificate-based-authentication.md)
* [使用 Azure Key Vault 保护 Azure Cosmos DB 密钥](access-secrets-from-keyvault.md)
* [Azure Cosmos DB 的安全基线](security-baseline.md)
