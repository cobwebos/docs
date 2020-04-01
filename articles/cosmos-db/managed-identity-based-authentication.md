---
title: 如何使用系统分配的托管标识访问 Azure Cosmos 数据库数据
description: 了解如何配置 Azure AD 系统分配的托管标识以从 Azure Cosmos DB 访问密钥。 msi， 托管服务标识， aad， azure 活动目录， 标识
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 102efba5d028eef621f392ef1739ea9ebeca0b44
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80417228"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>如何使用系统分配的托管标识访问 Azure Cosmos 数据库数据

在本文中，您将设置一个**健壮的、与密钥轮换无关的解决方案，** 以便利用[托管标识](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)访问 Azure Cosmos DB 密钥。 本文中的示例使用 Azure 函数。 但是，您可以使用支持托管标识的任何服务来实现此解决方案。 

您将学习如何创建 Azure 函数，该函数可以访问 Azure Cosmos DB，而无需复制任何 Azure Cosmos DB 键。 该功能将每分钟醒来，并记录水族馆鱼缸的当前温度。 要了解如何设置触发的 Azure 函数的计时器，请参阅[在 Azure 中创建由计时器文章触发的函数](../azure-functions/functions-create-scheduled-function.md)。

为了简化方案，旧温度文档的清理由已配置[的"实时生存时间"](./time-to-live.md)设置处理。 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>将系统分配的托管标识分配给 Azure 函数

在此步骤中，您将为 Azure 函数分配系统分配的托管标识。

1. 在[Azure 门户](https://portal.azure.com/)中，打开**Azure 函数**窗格并导航到函数应用。 

1. 打开**平台功能** > **标识**选项卡： 

   ![“标识”选项卡](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. 在 **"标识"** 选项卡**上，打开****"系统标识"** 状态。 请务必选择 **"保存**"，并确认要打开系统标识。 最后，"**系统标识"** 窗格应如下所示：  

   ![系统标识已打开](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>授予 Azure Cosmos 帐户的托管标识访问权限

在此步骤中，您将为 Azure 函数的系统分配的托管标识分配角色。 Azure Cosmos DB 具有多个内置角色，您可以将其分配给托管标识。 对于此解决方案，您将使用以下两个角色：

|内置角色  |说明  |
|---------|---------|
|[DocumentDB 帐户参与者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可管理 Azure Cosmos DB 帐户。 允许检索读/写密钥。 |
|[Cosmos DB 帐户读取者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以读取 Azure Cosmos DB 帐户数据。 允许检索读取密钥。 |

> [!IMPORTANT]
> Azure Cosmos DB 中的 RBAC 支持仅适用于控制平面操作。 使用主密钥或资源令牌保护数据平面操作。 要了解更多信息，请参阅[安全访问数据](secure-access-to-data.md)一文。

> [!TIP] 
> 分配角色时，仅分配所需的访问权限。 如果您的服务只需要读取数据，则将托管标识分配给**Cosmos DB 帐户读取器**角色。 有关最低特权访问的重要性的详细信息，请参阅[特权帐户的较低曝光](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)率一文。

对于方案，您将读取温度，然后将该数据写回 Azure Cosmos DB 中的容器。 由于您必须写入数据，因此将使用**DocumentDB 帐户参与者**角色。 

1. 登录到 Azure 门户，导航到 Azure Cosmos DB 帐户。 打开**访问管理 （IAM） 窗格**，然后打开**角色分配**选项卡：

   ![IAM 窗格](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. 选择 **+ 添加**按钮，然后**添加角色分配**。

1. "**添加角色分配"** 面板将向右打开：

   ![添加角色](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **角色**- 选择**文档数据库帐户参与者**
   * 在选择**系统分配的托管标识**子部分下**分配访问权限**，选择**函数应用**。
   * **选择**- 窗格将填充订阅中的所有具有**托管系统标识**的功能应用。 在我们的案例中，我选择**摘要服务**功能应用： 

      ![选择分配](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. 选择函数应用的标识后，单击"**保存**"。

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>从 Azure 函数以编程方式访问 Azure 宇宙数据库密钥

现在，我们有一个函数应用，具有系统分配的托管标识。 该标识在 Azure Cosmos DB 权限中被赋予**DocumentDB 帐户参与者**角色。 以下函数应用代码将获取 Azure Cosmos DB 键，创建 CosmosClient 对象，获取温度，然后将该参数数据库保存到 Cosmos DB。

此示例使用[列表键 API](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys)访问 Azure Cosmos DB 帐户密钥。

> [!IMPORTANT] 
> 如果要[分配**Cosmos DB 帐户读取器**](#grant-the-managed-identity-access-to-your-azure-cosmos-account)角色，则需要使用只读[列表密钥 api](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)。 这将仅填充只读密钥。

列表键 API 返回`DatabaseAccountListKeysResult`对象。 此类型未在 C# 库中定义。 以下代码显示了此类的实现：  

```csharp 
namespace SummarizationService 
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

您将使用[Microsoft.Azure.Services.App 身份验证](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)库来获取系统分配的托管标识令牌。 要了解获取令牌的其他方法以及有关`Microsoft.Azure.Service.AppAuthentication`库的详细信息，请参阅[服务到服务身份验证](../key-vault/service-to-service-authentication.md)一文。

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
    public static class TemperatureMonitor
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

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
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
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

现在，您已准备好[部署 Azure 函数](../azure-functions/functions-create-first-function-vs-code.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Azure Cosmos DB 和 Active Directory 进行基于证书的身份验证](certificate-based-authentication.md)
* [使用 Azure Key Vault 保护 Azure Cosmos 密钥](access-secrets-from-keyvault.md)
* [Azure 宇宙数据库的安全基线](security-baseline.md)
