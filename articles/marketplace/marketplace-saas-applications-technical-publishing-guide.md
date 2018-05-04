---
title: Azure Marketplace SaaS 应用程序技术发布指南
description: 将 SaaS 应用程序发布到 Azure Marketplace 的分步指南和发布查检表
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: eb6db45ca0fcb6879aeaeaaf70715691cac438b0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>SaaS 应用程序技术发布指南

欢迎使用 Azure Marketplace SaaS 应用程序技术发布指南。 本指南旨在帮助候选发布者和现有发布者使用 SaaS 应用程序产品将其应用程序和服务列入 Azure Marketplace。  
假设你要使用 SaaS 应用程序产品将解决方案部署在自己的 Azure 订阅中，然后，客户可以通过你设计和管理的界面登录，以测试应用程序。 在此过程中，需要通过 [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 利用现有的试用环境。 换而言之，它是一个客户导向的、合作伙伴托管的免费试用环境。 公开解决方案时，必须使云采购商能够独立地免费体验该解决方案，并且此产品类型能够根据客户搜索云解决方案的习惯来提供试用体验。  

有关其他所有 Marketplace 产品的概述，请参阅 [Marketplace 发布者指南](https://aka.ms/sellerguide)。

## <a name="saas-application-technical-guidance"></a>SaaS 应用程序技术指南
SaaS 应用程序的技术要求非常简单。 发布者只需与要发布的 Azure AD 集成。  Azure AD 与应用程序的集成已有完善的文档，并且 Microsoft 可以提供多个 SDK 和资源来实现此目的。  

若要开始，我们建议专门使用一个订阅进行 Azure Marketplace 发布，使发布工作与其他活动相互独立。 此外，我们建议在开发环境中安装以下工具（如果尚未安装）： 
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.0.0)  
- [Azure 开发人员工具（查看可用工具）](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>资源
以下列表提供了最适合用于入门的 Azure AD 资源的链接： 

**文档**

- [Azure Active Directory 开发人员指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

- [与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

- [将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

- [Azure 路线图 - 安全和标识](https://azure.microsoft.com/roadmap/?category=security-identity)

**视频**

- [使用 Vittorio Bertocci 进行 Azure Active Directory 身份验证](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

- [Azure Active Directory 标识技术简介 - 第 1/2 部分](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

- [Azure Active Directory 标识技术简介 - 第 2/2 部分](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

- [使用 Microsoft Azure Active Directory 生成应用](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

- [侧重于 Active Directory 的 Microsoft Azure 视频](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**培训**  
- [面向 IT 专业人士的 Microsoft Azure 内容系列：Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

**Azure Active Directory 服务更新**  
- [Azure AD 服务更新](https://azure.microsoft.com/updates/?product=active-directory)

可以使用以下资源获取支持：
- [MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Azure Active Directory 库
除了在 Azure Marketplace/AppSource 中列出应用程序以外，还可以在 Azure Marketplace AppStore 随附的 Azure AD 应用程序库中列出应用程序。 使用 Azure AD 作为标识提供者的客户可以查找此处发布的各种 SaaS 应用程序连接器。 IT 管理员可从应用库中添加连接器，然后对连接器进行配置并将其用于单一登录 (SSO) 和预配。 Azure AD 支持使用所有主要的联合身份验证协议（包括 SAML 2.0、OpenID Connect、OAuth 和 WS-Fed）进行 SSO。  

在测试应用程序集成可在 Azure AD 中正常工作后，请在应用程序网络门户中提交访问请求。 如果已创建 Office 365 帐户，请使用该帐户登录到此门户。 如果没有 Office 365 帐户，可以使用 Microsoft 帐户（例如 Outlook 或 Hotmail）登录。

## <a name="program-benefits"></a>计划权益
- 为客户提供尽可能最佳的单一登录体验
- 简化并最大程度地减少应用程序的配置
- 客户可以搜索应用程序，也可以在库中查找应用程序
- 任何客户都可以使用此集成，无论他们使用的 Azure AD SKU 是什么（免费版、基本版或高级版）。
- 为共同客户提供分步配置教程
- 如果使用 SCIM，可为同一应用实现用户预配

## <a name="prerequisites"></a>先决条件
若要将某个应用程序列在 Azure AD 库中，该应用程序首先需要实现 Azure AD 支持的联合身份验证协议之一。 请阅读 [Microsoft Azure 法律信息](https://azure.microsoft.com/support/legal/)中的 Azure AD 应用程序库条款和条件。  

下面根据所用的协议描述了其他先决条件信息：

**OpenID Connect** - 在 Azure AD 中创建多租户应用程序并为应用程序实现许可框架。 将登录请求发送到公用终结点，以便任何客户都可以向应用程序提供许可。 你可以根据在令牌中收到的租户 ID 和用户 UPN 控制客户用户访问。  
**SAML 2.0 或 WS-Fed** – 应用程序应该能够以 SP 或 IdP 模式执行 SAML 或 WS-Fed SSO 集成。
