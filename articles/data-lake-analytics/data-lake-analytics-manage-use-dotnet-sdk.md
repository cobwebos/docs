---
title: "使用 Azure .NET SDK 管理 Azure Data Lake Analytics | Microsoft Docs"
description: "了解如何管理 Data Lake Analytics 作业、数据源、用户。 "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/23/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 63ce27657ecf1c89f5d3dfc163ee655bc15ce0b7


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>使用 Azure .NET SDK 管理 Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

了解如何使用 Azure .NET SDK 管理 Azure Data Lake Analytics 帐户、数据源、用户和作业。 若要查看使用其他工具的管理主题，请单击上述选项卡选项。

**先决条件**

在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

<!-- ################################ -->
<!-- ################################ -->


## <a name="connect-to-azure-data-lake-analytics"></a>连接到 Azure Data Lake Analytics
需要以下 Nuget 包：

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common 
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


以下代码示例演示如何连接到 Azure，并列出 Azure 订阅下面的现有 Data Lake Analytics 帐户。

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;

            private static void Main(string[] args)
            {

                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = SUBSCRIPTIONID;

                var adlaAccounts = ListADLAAccounts();

                Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
                for (int i = 0; i < adlaAccounts.Count; i ++)
                {
                    Console.WriteLine(adlaAccounts[i].Name);
                }

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            public static ServiceClientCredentials AuthenticateAzure(
            string domainName,
            string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }
        }
    }


## <a name="manage-accounts"></a>管理帐户
运行任何 Data Lake Analytics 作业之前，必须具有 Data Lake Analytics 帐户。 与 Azure HDInsight 不同，Analytics 帐户未运行作业时无需付费。  只需在其运行作业时付费。  有关详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。  

### <a name="create-accounts"></a>创建帐户
运行以下示例前，必须具有 Azure 资源管理组和 Data Lake Store 帐户。

以下代码演示如何创建资源组：

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

以下代码演示如何创建 Data Lake Store 帐户：

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

以下代码演示如何创建 Data Lake Analytics 帐户：

    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
    var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

### <a name="list-accounts"></a>列出帐户
请参阅[连接到 Azure Data Lake Analytics](#connect_to_azure_data_lake_analytics)。

### <a name="find-an-account"></a>查找帐户
获取 Data Lake Analytics 帐户列表中的一个对象后，可使用以下代码查找帐户：

    Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
    var myAdlaAccount = adlaAccounts.Find(accountFinder);

### <a name="delete-data-lake-analytics-accounts"></a>删除 Data Lake Analytics 帐户
以下代码片段会删除 Data Lake Analytics 帐户：

    _adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>管理帐户数据源
Data Lake Analytics 当前支持以下数据源：

* [Azure Data Lake 存储](../data-lake-store/data-lake-store-overview.md)
* [Azure 存储](../storage/storage-introduction.md)

创建 Analytics 帐户时，必须指定一个 Azure Data Lake 存储帐户作为默认存储帐户。 默认 Data Lake Store 帐户用于存储作业元数据和作业审核日志。 创建 Analytics 帐户后，可添加其他 Data Lake 存储帐户和/或 Azure 存储帐户。 

### <a name="find-the-default-data-lake-store-account"></a>查找默认 Data Lake Store 帐户
请参阅本文中的“查找帐户”来查找 Data Lake Analytics 帐户。 然后使用以下代码：

    string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## <a name="use-azure-resource-manager-groups"></a>使用 Azure Resource Manager 组
应用程序通常由许多组件构成，例如 Web 应用、数据库、数据库服务器、存储和第三方服务。 可使用 Azure Resource Manager 以组（称为 Azure 资源组）的方式处理应用程序中的资源。 你可以通过一个协调的操作为应用程序部署、更新、监视或删除所有资源。 你可以使用一个模板来完成部署，该模板适用于不同的环境，例如测试、过渡和生产。 你可以通过查看整个组的累积费用，明确了解组织的帐单开支。 有关详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。 

Data Lake Analytics 服务可包括以下组件：

* Azure Data Lake Analytics 帐户
* 所需的默认 Azure Data Lake 存储帐户
* 其他 Azure Data Lake 存储帐户
* 其他 Azure 存储帐户

可在一个资源管理组下创建所有这些组件，使其更易于管理。

![Azure Data Lake Analytics 帐户和存储](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Data Lake Analytics 帐户和从属存储帐户必须位于同一个 Azure 数据中心。
但资源管理组可以位于不同的数据中心。  

## <a name="see-also"></a>另请参阅
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [Get started with Data Lake Analytics using Azure portal](data-lake-analytics-get-started-portal.md)
* [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)




<!--HONumber=Nov16_HO3-->


