---
title: "批处理帐户使用管理资源的客户端库用于.NET 的 Azure |Microsoft 文档"
description: "创建、 删除和修改使用批次管理.NET 库的 Azure Batch 帐户资源。"
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eafde9258222a2ab09ade2e366f9cc595a303dec
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>管理适用于.NET 的批处理帐户和与批处理管理客户端库的配额

> [!div class="op_single_selector"]
> * [Azure 门户](batch-account-create-portal.md)
> * [批次管理.NET](batch-management-dotnet.md)
> 
> 

您可以降低维护开销 Azure 批处理应用程序中使用[批次管理.NET] [ api_mgmt_net]库来自动执行创建批处理帐户、 删除、 密钥管理和配额发现。

* **创建和删除批处理帐户**任何区域内。 如果是独立软件供应商 (ISV) 例如，需要提供作为你的客户端在其中每个出于计费目的分配单独的批处理帐户的服务，你可以向客户门户添加帐户创建和删除功能。
* **检索和重新生成帐户密钥**以编程方式为任何批处理帐户。 这可以帮助您符合强制定期滚动更新或到期的帐户密钥的安全策略。 如果在多个不同的 Azure 区域中具有多个批处理帐户，此滚动更新过程自动化提高你的解决方案的效率。
* **检查帐户配额**并采取试用错误猜测确定哪些批处理帐户有哪些限制。 通过在启动作业之前检查你帐户的配额，创建池，或者添加计算节点，你可以主动调整 where 或时这些计算创建资源。 你可以确定哪些帐户需要配额增加然后将分配给这些帐户中的其他资源。
* **将其他 Azure 服务的功能组合**全功能的管理体验-通过使用批次管理.NET [Azure Active Directory][aad_about]，和[Azure 资源管理器][ resman_overview]一起在同一应用程序。 通过使用这些功能和其 Api，你可以提供顺畅的身份验证体验，能够创建和删除资源组，并上述的端到端管理解决方案的功能。

> [!NOTE]
> 虽然本文将着重的批处理帐户、 密钥和配额的编程管理，你可以执行许多这些活动通过使用[Azure 门户][azure_portal]。 有关详细信息，请参阅[创建使用 Azure 门户的 Azure Batch 帐户](batch-account-create-portal.md)和[Azure 批处理服务的配额和限制](batch-quota-limit.md)。
> 
> 

## <a name="create-and-delete-batch-accounts"></a>创建和删除批处理帐户
如前文所述，批处理管理 API 的主要功能之一是创建和删除的 Azure 区域中的批处理帐户。 为此，请使用[BatchManagementClient.Account.CreateAsync] [ net_create]和[DeleteAsync][net_delete]，或它们对应的同步。

下面的代码段创建一个帐户，从批处理服务，获取新创建的帐户，然后删除它。 在此代码段，在本文中，其他`batchManagementClient`是具有完全初始化的实例[BatchManagementClient][net_mgmt_client]。

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> 使用批次管理.NET 库和其 BatchManagementClient 类应用程序需要**服务管理员**或**coadministrator**对订阅拥有要管理的批处理帐户的访问。 有关详细信息，请参阅[Azure Active Directory](#azure-active-directory)部分和[AccountManagement] [ acct_mgmt_sample]的代码示例。
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>检索和重新生成帐户密钥
获取从订阅中任何 Batch 帐户的主要和辅助帐户密钥，通过使用[ListKeysAsync][net_list_keys]。 可以通过重新生成这些密钥[RegenerateKeyAsync][net_regenerate_keys]。

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> 你可以为你管理的应用程序中创建简化的连接工作流。 首先，获取你想要使用管理批处理帐户的帐户密钥[ListKeysAsync][net_list_keys]。 然后，使用此密钥，初始化 Batch.NET 库时[BatchSharedKeyCredentials] [ net_sharedkeycred]类，该类在初始化时使用[BatchClient][net_batch_client]。
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>检查 Azure 订阅和 Batch 帐户配额
Azure 订阅和像批处理所有单独的 Azure 服务有限制其中某些实体数的默认配额。 有关 Azure 订阅的默认配额，请参阅[Azure 订阅和服务限制、 配额和约束](../azure-subscription-service-limits.md)。 批处理服务的默认配额，请参阅[Azure 批处理服务的配额和限制](batch-quota-limit.md)。 通过使用批次管理.NET 库，你可以在应用程序中检查这些配额。 这使您能够分配决策之前添加帐户或计算资源，如池和计算节点。

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>检查批处理帐户配额的 Azure 订阅
之前在一个区域中创建批处理帐户，您可以检查你的 Azure 订阅，以查看你是否能够在该区域添加帐户。

在以下代码段中，我们首先使用[BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts]来获取在订阅中的所有批处理帐户的集合。 一旦我们已获得此集合，我们确定多少个帐户处于目标区域。 然后我们使用[BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions]获取 Batch 帐户配额，并确定可以在该区域中创建多少个帐户 （如果有）。

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

在上面的代码段`creds`是的一个实例[TokenCloudCredentials][azure_tokencreds]。 若要查看创建此对象的示例，请参阅[AccountManagement] [ acct_mgmt_sample] GitHub 上的代码示例。

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>检查计算资源配额的批处理帐户
之前增加批处理解决方案中的计算资源，您可以检查以确保你想要分配的资源不会超过该帐户的配额。 在下面的代码段中，我们打印的名为的批处理帐户的配额信息`mybatchaccount`。 在自己的应用程序，你可以使用此类信息确定该帐户是否可以处理要创建的其他资源。

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> 尽管有 Azure 订阅和服务的默认配额，许多这些限制可能引发通过发出中的请求[Azure 门户][azure_portal]。 有关示例，请参阅[Azure 批处理服务的配额和限制](batch-quota-limit.md)有关增加 Batch 帐户配额的说明。
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>使用与批次管理.NET 的 Azure AD

批次管理.NET 库是 Azure 资源提供程序客户端，并连同使用[Azure 资源管理器][ resman_overview]以编程方式管理帐户资源。 Azure AD 需要进行任何 Azure 资源提供程序客户端，包括批次管理.NET 库中，通过和通过的请求进行身份验证[Azure 资源管理器][resman_overview]。 有关使用 Azure AD 与批次管理.NET 库的信息，请参阅[使用 Azure Active Directory 进行身份验证批处理解决方案](batch-aad-auth.md)。 

## <a name="sample-project-on-github"></a>在 GitHub 上的示例项目

若要在操作中看到批次管理.NET，请查看[AccountManagment] [ acct_mgmt_sample] GitHub 上的示例项目。 AccountManagment 示例应用程序演示以下操作：

1. 通过使用获取从 Azure AD 安全令牌[ADAL][aad_adal]。 如果用户尚未登录，将提示他们输入其 Azure 凭据。
2. 与安全令牌从 Azure AD 中获得，创建[SubscriptionClient] [ resman_subclient]查询 Azure 订阅与帐户关联的列表。 如果它包含多个订阅，用户可以从列表选择的订阅。
3. 获取与所选订阅关联的凭据。
4. 创建[ResourceManagementClient] [ resman_client]对象使用的凭据。
5. 使用[ResourceManagementClient] [ resman_client]对象来创建资源组。
6. 使用[BatchManagementClient] [ net_mgmt_client]对象来执行多个批处理帐户操作：
   * 新的资源组中创建批处理帐户。
   * 从批处理服务中获取新创建的帐户。
   * 打印新帐户的帐户密钥。
   * 重新生成新的帐户的主要密钥。
   * 打印帐户的配额的信息。
   * 打印订阅的配额的信息。
   * 打印在订阅中的所有帐户。
   * 删除新创建的帐户。
7. 删除资源组。

在删除之前的新创建的批处理帐户和资源组，可以查看在[Azure 门户][azure_portal]:

若要成功运行示例应用程序，必须先将其注册到你在 Azure 门户中的 Azure AD 租户中，并向 Azure 资源管理器 API 授予权限。 请按照中提供的步骤[与 Active Directory 进行身份验证批次管理解决方案](batch-aad-auth-management.md)。


[aad_about]: ../active-directory/active-directory-whatis.md "什么是 Azure Active Directory？"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Azure AD 的身份验证方案"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "与 Azure Active Directory 集成应用程序"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
