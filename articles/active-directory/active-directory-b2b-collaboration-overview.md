<properties
   pageTitle="Azure Active Directory 企业到企业 (B2B) 协作"
   description="Azure Active Directory B2B 协作可让业务合作伙伴访问你的企业应用程序，合作伙伴的每个用户将由单个 Azure AD 帐户表示"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="msStevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.date="09/17/2015"
   wacn.date=""/>

# Azure Active Directory B2B 协作

Azure Active Directory B2B 协作可让你通过合作伙伴管理的标识来访问企业应用程序。你可以通过邀请并授权合作伙伴公司的用户访问你的资源，来创建跨公司关系。由于每家公司只与 Azure Active Directory (Azure AD) 联合一次，并且每位用户都由单个 Azure AD 帐户来表示，因此降低了复杂性。由于当合作伙伴用户从其组织离职时，其访问权限即会吊销，而无法通过内部目录中的成员身份进行意外的访问，因此提高了安全性。对于尚未部署 Azure AD 的业务合作伙伴，B2B 协作简化了注册体验，可以更方便地向业务合作伙伴提供 Azure AD 帐户。

-   业务合作伙伴可以使用他们自己的登录凭据，因此你无需管理外部合作伙伴目录，也无需在用户离开合作伙伴组织时删除访问权限。

-   你可以独立于业务合作伙伴的帐户生命周期管理对应用的访问。例如，这意味着你可以自行吊销访问权限，而不必请求业务合作伙伴的 IT 部门执行任何操作。

## 功能

B2B 协作简化了管理并改进了合作伙伴访问企业资源（包括 Office 365、Salesforce、Azure 服务等 SaaS 应用，以及每个移动、云和本地声明感知应用程序）时的安全性。B2B 协作可让合作伙伴管理自己的帐户，企业可以将安全策略应用于合作伙伴访问权限。

Azure Active Directory B2B 协作易于配置，各种规模的合作伙伴都能通过电子邮件验证过程轻松完成注册，即使他们没有自己的 Azure Active Directory。维护也很容易，无需外部目录或基于合作伙伴的联合配置。

过程：

1. Azure AD B2B 协作允许公司管理员将一个不超过 2000 行的逗号分隔值 (CSV) 文件上载到 B2B 协作门户，以便邀请和授权一组外部用户。

  ![CSV 文件上载对话框](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. 门户发送电子邮件邀请给这些外部用户。

3. 受邀的用户通过 Microsoft 登录现有工作帐户（在 Azure AD 中管理），或者在 Azure AD 中获取新的工作帐户。

4. 登录之后，用户将重定向到与他们共享的应用。

目前不支持邀请消费者电子邮件地址（例如，gmail 或 [*comcast.net*](http://comcast.net/)）。

有关 B2B 协作工作原理的详细信息，请观看[此视频](http://aka.ms/aadshowb2b)。

## CSV 文件格式

CSV 文件采用以下的格式。

**Email：**受邀用户的电子邮件地址。<br/> 
**DisplayName：**受邀用户的显示名称（通常是名字和姓氏）。<br/> 
**InviteAppID：**在电子邮件邀请和接受页面上用于表示品牌的应用程序 ID。<br/> 
**InviteReplyURL：**在受邀用户接受邀请后，将用户定向到的 URL。这应该是公司特定的 URL（例如 [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)）。<br/> 
**InviteAppResources：**应用程序可分配给用户的 AppID。可以通过调用 `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` 来检索 AppID。<br/>
**InviteGroupResources：**要将用户添加到的组的 ObjectIDs。可以通过调用 `Get-MsolGroup | fl DisplayName, ObjectId` 来检索 ObjectIDs。<br/>
**InviteContactUsUrl：**可在电子邮件邀请中添加的“联系我们”URL，方便受邀用户联系你的组织。<br/>

## 示例 CSV 文件
下面是一个 CSV 示例，你可以根据自己的目的对其进行修改。可以使用所需的任何文件名保存它，但必须确保扩展名为“.csv”。

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```

<!---HONumber=79-->