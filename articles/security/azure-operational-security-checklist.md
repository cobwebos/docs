---
title: Azure 操作安全性清单 | Microsoft Docs
description: 本文提供有关 Azure 操作安全性的一组清单。
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: ae8f2393b6965cc2fc78a297ea79e1170acc011f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894743"
---
# <a name="azure-operational-security-checklist"></a>Azure 操作安全性清单
在 Azure 上部署应用程序的过程快速、轻松且经济高效。 在生产环境中部署云应用程序之前，准备好一个清单会很有用，这样可以根据一份必要和建议的操作安全措施列表来评估应用程序。

## <a name="introduction"></a>介绍

Azure 提供一套可用于部署应用程序的基础结构服务。 Azure 操作安全性是指用户可用于在 Microsoft Azure 中保护其数据、应用程序和其他资产的服务、控件和功能。

-   为了最大程度地发挥云平台的优势，我们建议利用 Azure 服务并遵循本清单的建议。
-   在推出应用程序之前投入时间和资源评估应用程序操作就绪性的组织，比不采取这些措施的组织最终收获的满意度要高得多。 在执行这项工作时，清单可以充当一个极其有效的机制，确保以一致且整体的方式评估应用程序。
-   操作评估的级别根据组织的云利用成熟度、应用程序的开发阶段、可用性需求和数据敏感度要求而异。

## <a name="checklist"></a>清单

此清单的目的是帮助企业在 Azure 上部署复杂的企业应用程序时全盘考虑各种操作安全因素。 此外，它还有助于为组织构建安全的云迁移和操作策略。

|清单类别| 说明|
| ------------ | -------- |
| [<br>安全角色和访问控制](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)|<ul><li>使用[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 向特定范围的用户、组和应用程序分配权限。</li></ul> |
| [<br>数据收集和存储](https://docs.microsoft.com/azure/storage/storage-security-guide)|<ul><li>使用[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 通过管理平面安全性来保护存储帐户。</li><li>使用[共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) 和存储访问策略通过数据平面安全性来保护对数据的访问。</li><li>使用传输级加密 – 使用 [SMB（服务器消息块协议）3.0](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) 对 [Azure 文件共享](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files)所用的 HTTPS 和加密。</li><li>需要专门控制加密密钥时，使用[客户端加密](https://docs.microsoft.com/azure/storage/storage-client-side-encryption)来保护发送到存储帐户的数据。 </li><li>使用[存储服务加密 (SSE)](https://docs.microsoft.com/azure/storage/storage-service-encryption) 来自动加密 Azure 存储中的数据，使用 [Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 来加密 OS 和数据磁盘的虚拟机磁盘文件。</li><li>使用 Azure [存储分析](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)监视授权类型；像使用 Blob 存储时一样，可以查看用户使用的是共享访问签名还是存储帐户密钥。</li><li>使用[跨域资源共享 (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 访问不同域中的存储资源。</li></ul> |
|[<br>安全策略和建议](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)|<ul><li>使用 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-install-endpoint-protection)部署终结点解决方案。</li><li>添加 [Web 应用程序防火墙 (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) 来保护 Web 应用程序。</li><li>  使用从 Microsoft 合作伙伴的[下一代防火墙 (NGFW)](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) 来增强安全保护。 </li><li>为 Azure 订阅应用安全联系人详细信息；如果 [Microsoft 安全响应中心 (MSRC)](https://technet.microsoft.com/security/dn528958.aspx) 发现客户数据被非法或未授权的一方访问，MSRC 会联系你。</li></ul> |
| [<br>标识和访问管理](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices)|<ul><li>[使用 Azure AD 将本地目录与云目录同步](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)。</li><li>使用[单一登录](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/)让用户基于他们在 Azure AD 中的组织帐户访问其 SaaS 应用程序。</li><li>使用[密码重置注册活动](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights)报告来监视正在注册的用户。</li><li>为用户启用[多重身份验证 (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)。</li><li>开发人员可对应用使用安全标识功能，例如 [Microsoft 安全开发生命周期 (SDL)](https://www.microsoft.com/download/details.aspx?id=12379)。</li><li>使用 Azure AD Premium 异常报告和 [Azure AD 标识保护功能](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)主动监视可疑活动。</li></ul> |
|[<br>持续安全监视](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)|<ul><li>使用反恶意软件评估解决方案 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 来报告基础结构中的反恶意软件保护状态。</li><li>使用[更新评估](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)确定潜在安全问题的总体风险，以及这些更新对环境是否重要、有多重要。</li><li>[标识和访问](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources)提供用户的概述，具体信息包括： </li><ul><li>用户标识状态；</li><li>登录尝试失败次数；</li><li>    尝试登录期间使用的用户帐户、已锁定的帐户；</li> <li>密码已更改或重置的帐户； </li><li>当前已登录的帐户数目。</li></ul></ul> |
| [<br>Azure 安全中心检测功能](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)|<ul><li>使用[检测功能](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)识别针对 Microsoft Azure 资源的现行威胁。</li><li>使用[集成威胁情报](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/19/get-threat-intelligence-reports-with-azure-security-center/)，利用 Microsoft 产品和服务、[Microsoft 反数字犯罪部门 (DCU)](https://www.microsoft.com/stories/cyber.aspx)、[Microsoft 安全响应中心 (MSRC)](https://docs.microsoft.com/azure/security/azure-security-response-center) 以及外部源提供的全球威胁情报，搜寻已知的行为不端的攻击者。</li><li>使用[行为分析](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/)，运用已知模式发现恶意行为。 </li><li>使用[异常检测](https://msdn.microsoft.com/library/azure/dn913096.aspx)，利用统计分析构建历史基线。</li></ul> |
| [<br>开发运营 (DevOps)](https://docs.microsoft.com/azure/architecture/checklist/dev-ops)|<ul><li>[基础结构即代码 (IaC)](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) 实务可以自动化并验证网络和虚拟机的创建与解除流程，帮助交付安全、稳定的应用程序托管平台。</li><li>[持续集成和部署](https://www.visualstudio.com/docs/build/overview)能够推动代码的持续合并与测试，帮助提前发现缺陷。 </li><li>[版本管理](https://msdn.microsoft.com/library/vs/alm/release/overview)可通过管道的每个阶段管理自动化部署。</li><li>运行中应用程序的[应用性能监视](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/)（包括生产环境中应用程序的运行状况）以及客户使用情况信息可帮助组织建立假设，并快速验证或推翻策略。</li><li>使用[负载测试和自动缩放](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)可以发现应用中的性能问题，提高部署质量，确保应用始终保持运行或可用，以迎合业务需求。</li></ul> |


## <a name="conclusion"></a>结束语
许多组织已在 Azure 中成功部署并运行其云应用程序。 提供的清单强调了几项必备要点，可帮助提高成功部署和顺畅运营的几率。 我们强烈建议针对 Azure 上的现有应用程序部署和新的部署实施这些操作性和策略性事项。

## <a name="next-steps"></a>后续步骤
本文档介绍 OMS 安全和审核解决方案。 若要了解有关 OMS 安全的详细信息，请参阅以下文章：

- [Operations Management Suite (OMS) 概述](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)
- [设计和操作安全性](https://www.microsoft.com/trustcenter/security/designopsecurity)
- [Azure 安全中心规划和操作](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
