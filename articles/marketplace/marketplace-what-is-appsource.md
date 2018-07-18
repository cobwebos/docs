---
title: AppSource 简介及其与 Azure 配合使用的工作原理
description: AppSource 概述，借助 AppSource，Microsoft 合作伙伴可使客户通过支持 Microsoft 的在线店面发现其技术和服务。
services: Marketplace, AppSource, Compute, Storage, Networking, Security, SaaS
documentationcenter: ''
author: ellacroi
manager: ''
editor: v-brela
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/12/2018
ms.author: ellacroi
ms.openlocfilehash: 7da3ae1ca0fc59b0d606a22e9c00b8e7b58674ba
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335085"
---
# <a name="what-is-appsource"></a>AppSource 简介
借助 AppSource，Microsoft 合作伙伴可使客户通过支持 Microsoft 的在线店面发现其技术和服务。 AppSource 发布者可以推动 AppSource 上的业务用户查找、试用以及获取业务线 SaaS 应用程序和实现服务，以帮助推动业务成果，减少创造价值所需的时间： 

| 客户需求       | AppSource          | 
|:---------------------------------------- |:----------------------------------------------------- |
| **寻找适合目前所用 Microsoft 产品的业务解决方案**    | 允许合作伙伴使用第三方应用程序和服务来扩展 Microsoft 的云应用程序和技术。 |
| **可以轻松找到适当的解决方案或实施服务。**     | 提供一站式商店，让客户发现、试用和获取应用程序、服务、加载项等更多内容。 |
| **可解决具体业务难题的行业特定的业务线解决方案**   | 提供已完成的端到端行业解决方案，帮助解决跨多个行业的具体要求。 |
| **帮助提高生产力、效率和业务见解的应用**       | 为客户服务、人力资源、运营等业务线提供应用。 |
| **经验丰富的实施合作伙伴可帮助根据独特情况改编应用**     | AppSource 中为基于 Dynamics 365、Power BI、PowerApps 和第三方应用的解决方案提供咨询服务产品目录，以帮助企业用户找到能实现可预测结果的咨询服务。 |

## <a name="appsource-publishing"></a>AppSource 发布
通过 AppSource，你可以列出在客户使用 Office 365、Dynamics 365、Power BI 和 Power Apps 等 Microsoft 产品时可帮助满足其需求的应用程序或咨询服务。 AppSource Marketplace 中提供以下产品/服务： 
*   **Dynamics 365 for Finance and Operations**：企业资源规划 (ERP) 解决方案。 差异化解决方案包括面向大中型公司的 Enterprise Edition 和面向中小型企业的 Business Edition。
*   **Dynamics 365 for Customer Engagement**：客户关系管理 (CRM) 解决方案，包括 Sales、Customer Service、Field Service 和 Project Service Automation 应用程序。
*   **Dynamics NAV 托管服务**：Microsoft Dynamics NAV 是面向中型组织的企业资源规划 (ERP) 软件套件。 该服务提供面向制造业、配送、政府、零售和其他行业的专用功能。
*   **Power BI**：业务分析可视化解决方案。
*   **咨询服务**：Microsoft 合作伙伴提供的服务，用于帮助客户了解、试用和实现专用技术解决方案。
*   **Cortana Intelligence**：Cortana Intelligence Suite 是 Azure 提供的一套独立但完全集成的数据和分析平台工具。
*   **Office 365**：云中的现代工作区。 可免费与 Microsoft Word、PowerPoint、Excel 和 OneNote 进行协作。

### <a name="office-365"></a>Office 365
Office 中的应用程序现在可用于 AppSource！ 查看[发布过程和指导]( https://docs.microsoft.com/office/dev/store/submit-to-the-office-store "Office 365 - AppSource")。

### <a name="dynamics-365-for-finance-and-operations"></a>Dynamics 365 for Finance and Operations
为 Enterprise Edition 生成项目时，请查看[发布过程和指导](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source "Dynamics 365 Enterprise Edition - AppSource")。  

### <a name="dynamics-365-for-customer-engagement"></a>Dynamics 365 for Customer Engagement
查看[发布过程和指导](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource "Dynamics 365 - AppSource")。

### <a name="power-bi"></a>Power BI
创建自定义视觉对象并使其通过 AppSource 可用。 查看[发布过程和指导](https://docs.microsoft.com/power-bi/developer/office-store "Power BI - AppSource")。

### <a name="consulting-offers"></a>咨询服务
如果要提供服务来帮助客户推动数字化转型或实现解决方案或应用程序，则可以在 AppSource 中将此服务作为咨询服务提供。 
[查看指导并了解如何提交报价](https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf "AppSource - Partner Listing Guidelines")。

### <a name="cortana-intelligence"></a>Cortana Intelligence
[Cortana Intelligence AppSource 发布指南](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide "Cortana Intelligence AppSource - Publishing guide")

## <a name="azure-active-directory-integration"></a>Azure Active Directory 集成
某些 AppSource 店面应用程序需要与 Azure Active Directory (Azure AD) 集成后才能发布。 应用程序与 Azure AD 的集成已有完善的文档；Microsoft 提供多个 SDK 和额外资源来满足需求。 

| 产品/服务名称 | 需要 Azure AD 集成 | 说明 |
|:--- |:---:|:--- |
| Dynamics 365 for Operations | 否 | 基于许可证的产品/服务不需要 Azure AD 集成 |
| Dynamics 365 for Finance | 否 | 基于许可证的产品/服务不需要 Azure AD 集成 |
| Dynamics 365 for Customer Engagement | 否 | 基于许可证的产品/服务不需要 Azure AD 集成 |
| Dynamics NAV 托管服务 | 否 | 基于许可证的产品/服务不需要 Azure AD 集成 |
| Power BI | 否 | 基于许可证的产品/服务不需要 Azure AD 集成 |
| 咨询服务 | 否 | 服务由合作伙伴（而不是通过 Web 体验）进行协调 |
| Cortana Intelligence | 是 | Azure AD 是确保无缝客户体验和吸引最优质潜在客户的最佳做法 |
| SaaS 应用（以前称为“Web 应用”） | 首选 | Azure AD 是确保无缝客户体验和吸引最优质潜在客户的最佳做法 |
###### <a name="table-appsource-offerings-that-require-azure-active-directory-integration"></a>表格：需要 Azure Active Directory 集成的 AppSource 产品/服务

首先，建议设置用于 Azure 市场发布的专用订阅\*，让你的工作与其他计划隔离开来。 此外，如果尚未安装以下工具，建议将以下工具添加到开发环境： 
*   [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest "Azure - CLI")
*   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.0.0 "PowerShell - Azure")
*   查看 [Azure 开发人员工具](https://azure.microsoft.com/tools/ "Azure - Developer Tools")页中的可用工具
*   [Visual Studio Code](https://code.visualstudio.com/ "Visual Studio Code - Main")

如果刚开始使用 Azure AD，可通过以下链接获取最佳资源： 

**文档**
*   [Azure Active Directory 开发人员指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide "Azure AD - developers guide")
*   [与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate "Integrating - Azure AD")
*   [将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications "Integrating applications - Azure AD")
*   [Azure 路线图 - 安全和标识](https://azure.microsoft.com/roadmap/?category=security-identity "Azure Roadmap - Security and Identity")

**视频**
*   [使用 Vittorio Bertocci 进行 Azure Active Directory 身份验证](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration "Azure AD Authentication - Vittorio Bertocci")
*   [Azure Active Directory 标识技术简介 - 第 1/2 部分](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration "Azure AD - Identity Technical Briefing 1/2")
*   [Azure Active Directory 标识技术简介 - 第 2/2 部分](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration "Azure AD - Identity Technical Briefing 2/2")
*   [使用 Microsoft Azure Active Directory 生成应用](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration "Building Apps - Azure AD")
*   [侧重于 Active Directory 的 Microsoft Azure 视频](https://azure.microsoft.com/resources/videos/index/?services=active-directory "Azure AD - 视频")

**培训**
*   [面向 IT 专业人士的 Microsoft Azure 内容系列：Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965 "Microsoft Azure for IT Pros Content Series: Azure AD")

**Azure Active Directory 服务更新**
*   [Azure AD 服务更新](https://azure.microsoft.com/updates/?product=active-directory "Azure AD - Service Updates")

以下链接提供有关支持的良好资源： 
*   MSDN 论坛：[Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD "Azure AD in the MSDN forums")
*   StackOverflow：[Azure Active Directory](https://stackoverflow.com/questions/tagged/azure-active-directory "Azure AD on stackoverflow")
