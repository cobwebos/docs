---
title: "用于使用 Active Directory 标识修剪 Azure 搜索结果的安全筛选器 | Microsoft Docs"
description: "使用安全筛选器和 Active Directory 标识对 Azure 搜索内容进行访问控制。"
services: search
author: revitalbarletz
manager: jlembicz
ms.service: search
ms.topic: article
ms.date: 11/07/2017
ms.author: revitalb
ms.openlocfilehash: 2113b59d6fec15067acbef8b4d4c1fc34c141e62
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2017
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>用于使用 Active Directory 标识修剪 Azure 搜索结果的安全筛选器

本文演示如何结合使用 Azure Active Directory (AAD) 安全标识和 Azure 搜索中的筛选器基于用户组成员身份来修剪搜索结果。

本文涵盖以下任务：
> [!div class="checklist"]
- 创建 AAD 组和用户
- 将用户与创建的组相关联
- 缓存新组
- 使用关联的组编制文档索引
- 使用组标识符筛选器发出搜索请求

>[!NOTE]
> 本文中的示例代码片段是用 C# 语言编写的。 可以 [在 GitHub 上](http://aka.ms/search-dotnet-howto)找到完整的源代码。 

## <a name="prerequisites"></a>先决条件

Azure 搜索中的索引必须有一个[安全字段](search-security-trimming-for-azure-search.md)用于存储对文档拥有读取访问权限的组标识列表。 此用例假设某个安全对象项（例如个人的大学申请）与指定谁有权访问该项（招生人员）的安全字段之间存在一对一的对应关系。

在本演练中，必须拥有 AAD 管理员权限才能在 AAD 中创建用户、组和关联。

此外，必须根据以下过程所述，将应用程序注册到 AAD。

### <a name="register-your-application-with-aad"></a>将应用程序注册到 AAD

此步骤将应用程序与 AAD 集成，以接受用户和组帐户的登录。 如果你不是组织中的 AAD 管理员，可能需要[创建新租户](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)才能执行以下步骤。

1. 转到[**应用程序注册门户**](https://apps.dev.microsoft.com)  >   选择“聚合应用” > “添加应用”。
2. 输入应用程序的名称，单击“创建”。 
3. 在“我的应用程序”页中选择新注册的应用程序。
4. 在应用程序注册页上 > 选择“平台” > “添加平台”>“Web API”。
5. 仍在应用程序注册页上，转到“Microsoft Graph 权限” > “添加”。
6. 在“选择权限”中添加以下委托权限，单击“确定”：

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

可以使用 Microsoft Graph 提供的某个 API 通过 REST API 以编程方式访问 AAD。 本演练的代码示例使用调用 Microsoft 图形 API 的权限来创建组、用户和关联。 还可以使用 API 缓存组标识符以提高筛选速度。

## <a name="create-users-and-groups"></a>创建用户和组

如果正在向建立的应用程序添加搜索，AAD 中可能已包含现有的用户和组标识符。 在这种情况下，可以跳过接下来的三个步骤。 

但是，如果没有现有用户，可以使用 Microsoft 图形 API 创建安全主体。 以下代码片段演示如何生成标识符，这些标识符将成为 Azure 搜索索引中安全字段的数据值。 在虚构的大学招生应用程序中，这些标识符将是招生工作人员的安全标识符。

用户和组的成员身份可能很不稳定，尤其是在大型组织中。 生成用户和组标识的代码应该以足够高的频率运行，以拾取组织成员身份的更改。 同样，Azure 搜索索引需有类似的更新计划，以反映受允许用户和资源的当前状态。

### <a name="step-1-create-aad-grouphttpsdevelopermicrosoftcomgraphdocsapi-referencev10apigrouppostgroups"></a>步骤 1：创建 [AAD 组](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/group_post_groups) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-userhttpsdevelopermicrosoftcomgraphdocsapi-referencev10apiuserpostusers"></a>步骤 2：创建 [AAD 用户](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_post_users) 
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>步骤 3：将用户和组相关联
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>步骤 4：缓存组标识符
（可选）为了降低网络延迟，可以缓存用户与组之间的关联，以便在发出搜索请求后，可以从缓存返回组，免除与 AAD 之间的一次往返。 可以使用 (AAD Batch API)[https://developer.microsoft.com/graph/docs/concepts/json_batching] 发送包含多个用户的单个 HTTP 请求并生成缓存。

Microsoft Graph 能够处理大量的请求。 如果发出无以数计的请求，Microsoft Graph 将会失败并返回 HTTP 状态代码 429。 有关详细信息，请参阅 [Microsoft Graph 限制](https://developer.microsoft.com/graph/docs/concepts/throttling)。

## <a name="index-document-with-their-permitted-groups"></a>使用受允许的组编制文档索引

Azure 搜索中的查询操作是基于 Azure 搜索索引执行的。 在此步骤中，索引操作会将可搜索的数据（包括用作安全筛选器的标识符）导入索引。 

Azure 搜索不会验证用户的身份，也不提供逻辑来确定用户有权查看哪些内容。 安全修整的用例假设在某个敏感文档与有权访问该文档的组标识符之间提供了关联，并将这种关联按原样导入了搜索索引。 

在虚构的示例中，Azure 搜索索引中 PUT 请求的正文应包含申请者的大学申请短文或成绩报告单，以及有权查看该内容的组标识符。 

在本演练的代码示例使用的常规示例中，索引操作可能如下所示：

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>发出搜索请求

为了进行安全修整，索引的安全字段中的值是静态值，用于在搜索结果中包含或排除文档。 例如，如果招生人员的组标识符为“A11B22C33D44-E55F66G77-H88I99JKK”，则会在发回给请求者的搜索结果中，包含（或排除）安全字段包含该标识符的 Azure 搜索索引中的所有文档。

若要基于发出请求的用户组筛选搜索结果中返回的文档，请查看以下步骤。

### <a name="step-1-retrieve-users-group-identifiers"></a>步骤 1：检索用户的组标识符

如果用户的组尚未缓存或缓存已过期，请发出[组](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/directoryobject_getmembergroups)请求
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>步骤 2：撰写搜索请求

假设你拥有用户的组成员身份，则可以使用相应的筛选器值发出搜索请求。

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>步骤 3：处理结果

响应包含文档的筛选列表，该列表由用户有权查看的文档构成。 根据搜索结果页的构造方式，可能需要包含视觉线索来反映筛选的结果集。

## <a name="conclusion"></a>结束语

本演练已介绍如何使用 AAD 登录名筛选 Azure 搜索结果中的文档，以及修剪与请求中提供的筛选器不匹配的文档结果。

## <a name="see-also"></a>另请参阅

+ [使用 Azure 搜索筛选器进行基于标识的访问控制](search-security-trimming-for-azure-search.md)
+ [Azure 搜索中的筛选器](search-filters.md)
+ [Azure 搜索操作中的数据安全性和访问控制](search-security-overview.md)
