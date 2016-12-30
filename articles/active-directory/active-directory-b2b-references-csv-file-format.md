---
title: "适用于 Azure Active Directory B2B 协作预览版的 CSV 文件格式 | Microsoft 文档"
description: "Azure Active Directory B2B 可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持"
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: bd2c5364-4164-407d-ac25-34088c175c4a
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f972fa0f8322ee33e1d198738f35d800564b4ddf


---
# <a name="azure-ad-b2b-collaboration-preview-csv-file-format"></a>Azure AD B2B 协作预览版：CSV 文件格式
Azure AD B2B 协作预览版需要 CSV 文件，该文件用于指定要通过 Azure AD 门户上传的合作伙伴用户信息。 该 CSV 文件应包含必需的以下标签以及可选字段（如有需要）。 修改以下示例 CSV 文件，无需更改第一行中的标签拼写。

> [!NOTE]
> 第一行标签（如 Email、DisplayName 等）对于成功分析 CSV 文件而言必不可少。 拼写必须匹配指定的以下字段。 这些标签标识位于其下的内容。 对于不需要的可选字段，可以从 CSV 文件中将它们的标签删除。 整个列可以保留为空。
> 
> 

## <a name="required-fields-br"></a>必填字段： <br/>
**Email：**受邀用户的电子邮件地址。 <br/>
**DisplayName：**受邀用户的显示名称（通常是名字和姓氏）。<br/>

## <a name="optional-fields-br"></a>可选字段： <br/>
**InvitationText：**在应用品牌推广之后和兑换链接之前的位置，自定义邀请电子邮件文本。<br/>
**InvitedToApplications：**分配给用户的企业应用程序的 AppID。 在 PowerShell 中通过调用 `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` 可检索 AppID<br/>
**InvitedToGroups：**要将用户添加到的组的 ObjectID。 在 PowerShell 中通过调用 `Get-MsolGroup | fl DisplayName, ObjectId` 可检索 ObjectID<br/>
**InviteRedirectURL：**在受邀用户接受邀请后，将用户定向到的 URL。 这应该是公司特定的 URL（例如 [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)）。 如果未指定此可选字段，会将受邀用户定向到“应用访问面板”，用户可在该面板中导航到选定的企业应用。 “应用访问面板”的 URL 的格式为 `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`。<br/>
**CcEmailAddress**：复制通过电子邮件发送的邀请的电子邮件地址。 当使用 CcEmailAddress 字段时，该邀请不能用于电子邮件验证的用户或租户创建。<br/>
**Language：**邀请电子邮件和兑换体验所用语言，“en”（英语）为默认语言（未指定时）。 其他 10 种受支持的语言代码如下所示：<br/>

1. de：德语<br/>
2. es：西班牙语<br/>
3. fr：法语<br/>
4. it：意大利语<br/>
5. ja：日语<br/>
6. ko：朝鲜语<br/>
7. pt-BR：葡萄牙语(巴西)<br/>
8. ru：俄语<br/>
9. zh-HANS：中文(简体)<br/>
10. zh-HANT：中文(繁体)<br/>

## <a name="sample-csv-file"></a>示例 CSV 文件
下面是一个可以修改的 CSV 示例。

> [!NOTE]
> 将该文件的内容复制并粘贴到记事本中，并使用“.csv”文件扩展名进行保存。 然后使用 Excel 对文件内容进行编辑。 将结构化为第一行含有标签的表。
> 
> 在 Excel 中，通过指定标签以及在其下方填充列可添加更多可选字段。
> 
> 

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## <a name="related-articles"></a>相关文章
在 Azure AD B2B 协作网站上浏览我们的其他文章

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [工作原理](active-directory-b2b-how-it-works.md)
* [详细演练](active-directory-b2b-detailed-walkthrough.md)
* [外部用户令牌格式](active-directory-b2b-references-external-user-token-format.md)
* [外部用户对象属性更改](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [当前预览版限制](active-directory-b2b-current-preview-limitations.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)




<!--HONumber=Dec16_HO5-->


