<properties
	pageTitle="应用模型 v2.0 概述 | Microsoft Azure"
	description="使用 Microsoft 帐户和 Azure Active Directory 登录的构建应用简介。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.date="11/06/2015"
	wacn.date=""/>

# 应用模型 v2.0 预览版：单个应用中的 Microsoft 帐户登录和 Azure AD 用户

> [AZURE.NOTE]
	此信息适用于 2.0 版应用模型的公共预览版。有关如何集成通常可用的 Azure AD 服务的说明，请参阅 [Azure Active Directory 开发人员指南](/documentation/articles/active-directory-developers-guide)。

在过去，想要支持 Microsoft 帐户和 Azure Active Directory 的应用开发人员需要集成两个单独的系统。通过使用 2.0 版应用模型，你现在可以使用这两种类型的帐户登录用户。通过一个简单的集成，你就可以与具有个人和工作/学校帐户的数以百万计的用户受众进行沟通。

你的应用也可以使用任一类型的帐户运用 [Office 365 REST API 集](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)。目前，这些 API 包括 Outlook 的邮件、联系人和日历 API。在不久的将来会添加其他服务。
<!-- TODO: customer reference article -->
<!-- Several apps have already begun to bridge the gap between consumer and enterprise accounts, including: [Boomerang](), [TripIt](), & [Uber](). -->

2\.0 版应用模型为预览版。在预览版期间，当你尝试使用此版本时，我们渴望听到你对新应用模型的反馈和体验。基于该反馈，为了改进服务，我们可能会进行重大更改。不应在此期间使用 2.0 版应用模型发布生产应用。
<!-- TODO: Get approval on how it looks  -->

## 入门
有两种方法创建你自己的应用并和 2.0 版应用模型运行。使用 [OAuth 2.0](/documentation/articles/active-directory-v2-protocols#oauth2-authorization-code-flow) 或 [Open ID Connect](/documentation/articles/active-directory-v2-protocols#openid-connect-sign-in-flow)，你可以选择直接发送协议消息。或者，你可以使用我们的库来完成工作 - 选择下面你最喜欢的平台并快速入门。
<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../includes/active-directory-v2-quickstart-table.md)]

## 新增功能
请经常在此确认，以了解有关将来对 2.0 版应用模型的公共预览版进行的更改。我们还会使用 @AzureAD 在推特上发表任何更新。

- 了解有关[可以使用应用模型 v2.0 版构建的应用的类型](/documentation/articles/active-directory-v2-flows)。
- 对于熟悉 Azure Active Directory 的开发人员，则应查看 [我们协议的更新和 2.0 版应用模型中的差异](/documentation/articles/active-directory-v2-compare)。
- 当前 [预览版局限、限制和约束](/documentation/articles/active-directory-v2-limitations)。

## 引用
这些链接有助于深入地利用平台：

- 使用 [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 或 [adal](http://stackoverflow.com/questions/tagged/adal) 标记获取有关堆栈溢出的帮助。
- 使用“[用户之声](http://feedback.azure.com/forums/169401-azure-active-directory)”向我们提供你对预览版的看法 -我们希望听到这些！ 在你的帖子标题中使用短语“AppModelv2:”，以便我们可以找到该帖子。
- [应用模型 v2.0 版协议参考](/documentation/articles/active-directory-v2-protocols)
- [应用模型 v2.0 版令牌参考](/documentation/articles/active-directory-v2-tokens)
- [Office 365 REST API 参考](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [v2 终结点中的范围和许可](/documentation/articles/active-directory-v2-scopes)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](/documentation/articles/active-directory-v2-faq)
-->

<!---HONumber=Mooncake_0118_2016-->