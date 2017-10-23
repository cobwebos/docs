---
title: "使用.NET SDK 在 Azure DNS 中创建 DNS 区域和记录集 | Microsoft Docs"
description: "如何使用 .NET SDK 在 Azure DNS 中创建 DNS 区域和记录集。"
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: jonatul
ms.openlocfilehash: c0fb0be8da1c0ca48a4d43ea027d30a0bc17fe30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>使用 .NET SDK 创建 DNS 区域和记录集

可以通过结合使用 DNS SDK 与 .NET DNS 管理库自动执行创建、删除或更新 DNS 区域、记录集和记录的操作。 [此处](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)提供了完整的 Visual Studio 项目。

## <a name="create-a-service-principal-account"></a>创建服务主体帐户

通常情况下，通过专用帐户（而不是自己的用户凭据）授予对 Azure 资源的编程访问权限。 将这些专用帐户称为“服务主体”帐户。 若要使用 Azure DNS SDK 示例项目，首先需要创建一个服务主体帐户并为其分配正确的权限。

1. 请按照[这些说明](../azure-resource-manager/resource-group-authenticate-service-principal.md)创建服务主体帐户（Azure DNS SDK 示例项目采用基于密码的身份验证。）
2. 创建资源组（[此处提供了方法](../azure-resource-manager/resource-group-template-deploy-portal.md)）。
3. 使用 Azure RBAC 将服务主体帐户的“DNS 区域参与者”权限授予资源组（[此处提供了方法](../active-directory/role-based-access-control-configure.md)）。
4. 如果使用 Azure DNS SDK 示例项目，请如下所示编辑 program.cs 文件：

   * 请插入在步骤 1 中使用的 tenantId、clientId（也称为帐户 ID）、密钥（服务主体帐户的密码）和 subscriptionId 的正确值。
   * 输入在步骤 2 中选择的资源组名称。
   * 输入所选的 DNS 区域名称。

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet 包和命名空间声明

若要使用 Azure DNS .NET SDK，需要安装 **Azure DNS 管理库** NuGet 包和其他所需的 Azure 包。

1. 在 **Visual Studio** 中，打开项目或新的项目。
2. 转到“**工具**”**>**“**NuGet 包管理器**”**>“** **管理解决方案的 NuGet 包...**”。
3. 单击“**浏览**”，启用“**包括预发行版**”复选框，并在搜索框中键入 **Microsoft.Azure.Management.Dns**。
4. 选择此包，然后单击“**安装**”将其添加到 Visual Studio 项目中。
5. 重复以上过程，还能安装以下包：**Microsoft.Rest.ClientRuntime.Azure.Authentication** 和 **Microsoft.Azure.Management.ResourceManager**。

## <a name="add-namespace-declarations"></a>添加命名空间声明

添加以下命名空间声明

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>初始化 DNS 管理客户端

*DnsManagementClient* 包含管理 DNS 区域和记录集所必需的方法和属性。  以下代码在登录到服务主体帐户并创建一个 DnsManagementClient 对象。

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>创建或更新 DNS 区域

若要创建 DNS 区域，首先创建一个 "Zone" 对象来包含 DNS 区域参数。 因为 DNS 区域未链接到某一特定区域，此位置会被设置为 'global'。 在此示例中，还会将 [Azure Resource Manager“标记”](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)添加到此区域。

要实际创建或更新 Azure DNS 中的区域，会将包含区域参数的区域对象传递给 *DnsManagementClient.Zones.CreateOrUpdateAsyc* 方法。

> [!NOTE]
> DnsManagementClient 支持三种操作模式：同步 ('CreateOrUpdate')、异步 ('CreateOrUpdateAsync') 或异步但可以访问 HTTP 响应 ('CreateOrUpdateWithHttpMessagesAsync')。  可以选择上述任何一种模式，具体取决于应用程序的需要。

Azure DNS 支持乐观并发，称为 [Etag](dns-getstarted-create-dnszone.md)。 在此示例中，为“If-None-Match”标头指定 "*"，通知 Azure DNS 创建一个 DNS 区域（如果尚不存在）。  如果指定的资源组内已存在具有特定名称的区域，此调用失败。

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>创建 DNS 记录集和记录

DNS 记录是作为记录集管理的。 记录集是区域内一组名称和记录类型都相同的记录。  记录集的名称相对于区域名称，而不是完全限定的 DNS 名称。

若要创建或更新记录集，请创建 RecordSet 参数对象并将其传递到 *DnsManagementClient.RecordSets.CreateOrUpdateAsync*。 和 DNS 区域一样，也有三种操作模式：同步 ('CreateOrUpdate')、异步 ('CreateOrUpdateAsync') 或异步但可以访问 HTTP 响应 ('CreateOrUpdateWithHttpMessagesAsync')。

和 DNS 区域一样，记录集的操作包括对乐观并发的支持。  在此示例中，由于既未指定 If-match 也未指定 If-None-Match，始终都会创建记录集。  此调用将覆盖 DNS 区域中任何现有的名称和记录类型都相同的记录。

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>获取区域和记录集

*DnsManagementClient.Zones.Get* 和 *DnsManagementClient.RecordSets.Get* 方法分别检索各个区域和记录集。 RecordSets 按它们的类型、名称以及所在的区域和资源组进行标识。 区域按它们的名称以及所在的资源组进行标识。

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>更新现有的记录集

要更新现有的 DNS 记录集，首先检索记录集，再更新记录集的内容，然后提交更改。  在此示例中，我们从 If-match 参数检索到的记录集内指定 Etag。 如果在此期间并发操作修改了记录集，调用会失败。

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>列出区域和记录集

若要列出区域，请使用 *DnsManagementClient.Zones.List...* 方法，它支持列出给定资源组中的所有区域或给定 Azure 订阅中的所有区域（跨资源组）。若要列出记录集，请使用 *DnsManagementClient.RecordSets.List...* 方法，它支持列出给定区域中的所有记录集或仅列出特定类型的记录集。

请注意，列出区域和记录集时，可能会对结果进行分页。  下面的示例演示如何循环访问各页结果。 （人为减小的‘2’页面大小用于强制分页；在实践中，应省略此参数并使用默认页面大小。）

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>后续步骤

下载 [Azure DNS.NET SDK 示例项目](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)，其中包括如何使用 Azure DNS.NET SDK 的更多示例，还有其他 DNS 记录类型的示例。
