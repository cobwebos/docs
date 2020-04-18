---
title: 如何使用系统分配的托管标识访问 Azure Cosmos 数据库数据
description: 了解如何配置 Azure 活动目录 （Azure AD） 系统分配的托管标识（托管服务标识）以从 Azure Cosmos DB 访问密钥。
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 8136ad7a1fe29bc3394e959c10aafc52988c0a23
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641240"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>使用系统分配的托管标识访问 Azure Cosmos 数据库数据

在本文中，您将设置一个*健壮的密钥旋转无关*解决方案，以便使用[托管标识](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)访问 Azure Cosmos DB 密钥。 本文中的示例使用 Azure 函数，但可以使用支持托管标识的任何服务。 

您将学习如何创建一个函数应用，该应用可以访问 Azure Cosmos DB 数据，而无需复制任何 Azure Cosmos 数据库键。 功能应用程序将每分钟醒来，并记录水族馆鱼缸的当前温度。 要了解如何设置计时器触发的函数应用，请参阅[在 Azure 中创建由计时器文章触发的函数](../azure-functions/functions-create-scheduled-function.md)。

为了简化方案，已配置["实时到实时"](./time-to-live.md)设置以清理较旧的温度文档。 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>将系统分配的托管标识分配给函数应用

在此步骤中，您将为函数应用分配系统分配的托管标识。

1. 在[Azure 门户](https://portal.azure.com/)中，打开**Azure 函数**窗格并转到函数应用。 

1. 打开**平台功能** > **标识**选项卡： 

   ![显示功能应用的平台功能和标识选项的屏幕截图。](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. 在 **"标识"** 选项卡**上，打开**系统标识**状态**并选择"**保存**"。 **"标识"** 窗格应如下所示：  

   ![显示系统标识状态设置为"打开"的屏幕截图。](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-access-to-your-azure-cosmos-account"></a>授予对 Azure Cosmos 帐户的访问权限

在此步骤中，您将为函数应用的系统分配的托管标识分配角色。 Azure Cosmos DB 具有多个内置角色，您可以将其分配给托管标识。 对于此解决方案，您将使用以下两个角色：

|内置角色  |说明  |
|---------|---------|
|[DocumentDB 帐户参与者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可管理 Azure Cosmos DB 帐户。 允许检索读/写密钥。 |
|[Cosmos DB 帐户读取者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以读取 Azure Cosmos DB 帐户数据。 允许检索读取密钥。 |

> [!IMPORTANT]
> Azure Cosmos DB 中对基于角色的访问控制的支持仅适用于控制平面操作。 数据平面操作通过主键或资源令牌进行保护。 要了解更多信息，请参阅[安全访问数据](secure-access-to-data.md)一文。

> [!TIP] 
> 分配角色时，请仅分配所需的访问权限。 如果您的服务只需要读取数据，则将**Cosmos DB 帐户读取器**角色分配给托管标识。 有关最低特权访问的重要性的详细信息，请参阅[特权帐户的"低曝光"](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)一文。

在这种情况下，函数应用将读取水族馆的温度，然后将该数据写回 Azure Cosmos DB 中的容器。 由于函数应用必须写入数据，因此您需要分配**DocumentDB 帐户参与者**角色。 

1. 登录到 Azure 门户并转到 Azure Cosmos 数据库帐户。 打开 **"访问控制 （IAM）"** 窗格，然后打开**角色分配**选项卡：

   ![显示"访问控制"窗格和角色分配选项卡的屏幕截图。](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. 选择 **=** > **添加角色分配**。

1. "**添加角色分配**"面板将向右打开：

   ![显示"添加角色分配"窗格的屏幕截图。](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **角色**： 选择**文档数据库帐户参与者**
   * 在 **"选择系统分配托管标识**"子部分下**分配访问权限**，选择 **"函数应用**"。
   * **选择**：窗格将填充具有**托管系统标识**的订阅中的所有功能应用。 在这种情况下，选择**鱼缸温度服务**功能应用程序： 

      ![显示填充示例的"添加角色分配"窗格的屏幕截图。](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. 选择函数应用后，选择 **"保存**"。

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>以编程方式访问 Azure 宇宙数据库密钥

现在，我们有一个函数应用，该函数应用具有在 Azure Cosmos DB 权限中具有**DocumentDB 帐户参与者**角色的系统分配的托管标识。 以下函数应用代码将获取 Azure Cosmos DB 键、创建 CosmosClient 对象、获取水族馆的温度，然后将其保存到 Azure Cosmos DB。

此示例使用[列表键 API](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys)访问 Azure Cosmos DB 帐户密钥。

> [!IMPORTANT] 
> 如果要[分配 Cosmos DB 帐户读取器](#grant-access-to-your-azure-cosmos-account)角色，则需要使用[列表只读密钥 API](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)。 这将只填充只读密钥。

列表键 API 返回`DatabaseAccountListKeysResult`对象。 此类型未在 C# 库中定义。 以下代码显示了此类的实现：  

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

该示例还使用名为"TemperatureRecord"的简单文档，该文档的定义如下：

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

您将使用[Microsoft.Azure.Services.App 身份验证](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)库来获取系统分配的托管标识令牌。 要了解获取令牌的其他方法并了解有关库的详细信息，`Microsoft.Azure.Service.AppAuthentication`请参阅[服务到服务身份验证](../key-vault/general/service-to-service-authentication.md)一文。


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

现在，您可以[部署函数应用](../azure-functions/functions-create-first-function-vs-code.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Azure Cosmos DB 和 Azure 活动目录进行基于证书的身份验证](certificate-based-authentication.md)
* [使用 Azure 密钥保管库保护 Azure 宇宙数据库密钥](access-secrets-from-keyvault.md)
* [Azure 宇宙数据库的安全基线](security-baseline.md)
