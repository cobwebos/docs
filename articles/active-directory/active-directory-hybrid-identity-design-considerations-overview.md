---
title: "Azure Active Directory 混合标识设计注意事项 - 概述 | Microsoft 文档"
description: "混合标识设计注意事项指南的概述和内容地图"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: e2a70f2474298618dd8ee11c583f8f445d7eba7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory 混合标识设计注意事项
基于使用者的设备在公司内日益激增，基于云的软件即服务 (SaaS) 应用程序便于采用。 因此，对用户应用程序的内部数据中心和云平台访问保持控制并非易事。  

Microsoft 的标识解决方案跨越本地和基于云的功能，创建单一用户标识对所有资源进行身份验证和授权，而不考虑其位置。 我们称此为混合标识。 使用 Microsoft 解决方案的混合标识有不同的设计和配置选项，并且在某些情况下，可能难以确定哪些组合最符合组织的需求。 

此混合标识设计注意事项指南将帮助你了解如何设计一个最适合组织的业务和技术需求的混合标识解决方案。  本指南将详细介绍一系列可以遵循的步骤和任务，用于帮助你设计满足组织独特要求的混合标识解决方案。 在这些步骤和任务之中，该指南始终会提供组织可用的相关技术和功能选项，满足功能和服务质量（例如可用性、可伸缩性、性能、可管理性和安全性）级别要求。 

具体而言，混合标识设计注意事项指南目标是回答以下问题： 

* 需要提出和回答哪些问题来驱动最符合我的要求的技术或问题域的混合标识特定设计？
* 应依次完成哪些活动才能够针对技术或问题域设计混合标识解决方案？ 
* 我可以借助哪些混合标识技术和配置选项来达到自己的要求？ 为了选出最适合自己业务的选项，我应该如何权衡这些选项之间的利弊？

## <a name="who-is-this-guide-intended-for"></a>本指南适用于谁？
 负责为大中型组织设计混合标识解决方案的首席信息官、CITO、首席标识架构师、企业架构师和 IT 架构师。

## <a name="how-can-this-guide-help-you"></a>本指南可以在哪些方面提供帮助？
可以使用本指南了解如何设计可以将基于云的标识管理系统与当前的本地标识解决方案集成的混合标识解决方案。 

下图显示了一个混合标识解决方案，该解决方案支持 IT 管理员对将位于本地的当前 Windows Server Active Directory 解决方案与 Microsoft Azure Active Directory 集成以支持用户在位于云中和本地的应用程序之间使用单一登录 (SSO)。

![](./media/hybrid-id-design-considerations/hybridID-example.png)

上图是一个混合标识解决方案示例，该示例利用云服务与本地功能集成，以向最终用户的身份验证过程提供单一体验，并帮助 IT 人员管理这些资源。 尽管该方案可能非常常见，但每个组织的混合标识设计可能因不同的要求而与图 1 中所示的示例不同。 

本指南将提供一系列可以遵循的步骤和任务，用于设计满足组织独特要求的混合标识解决方案。 在这些步骤和任务之中，该指南始终会向你提供可用的相关技术和功能选项，以满足组织的功能和服务质量级别要求。

**假设**：有使用 Windows Server、Active Directory 域服务和 Azure Active Directory 的一些经验。 在本文档中，我们假设你正在寻找这些解决方案如何才能满足业务需求：基于自身还是通过集成的解决方案。

## <a name="design-considerations-overview"></a>设计注意事项概述
本文档将提供一组可以遵循的步骤和任务，用于设计最符合你需求的混合标识解决方案。 将按顺序提供这些步骤。 后续步骤中学习的设计注意事项可能会要求更改之前步骤中所做的决策，只是因为与设计选择冲突。 在整个文档中，我们会尽可能地提醒你潜在的设计冲突。 

只有在根据需要多次反复执行相关步骤以兼顾文档内的所有注意事项后，才能完成最符合需求的设计。 

| 混合标识阶段 | 主题列表 |
| --- | --- |
| 确定标识要求 |[确定业务需求](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [确定目录同步要求](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [确定多重身份验证要求](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [定义混合标识采用策略](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| 通过强标识解决方案增强数据安全性的计划 |[确定数据保护要求](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [确定内容管理要求](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [确定访问控制要求](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [确定事件响应要求](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [定义数据保护策略](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) |
| 规划混合标识生命周期 |[确定混合标识管理任务](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [同步管理](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [确定混合标识管理采用策略](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="download-this-guide"></a>下载此指南
可以从 [Technet 库](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288)下载 PDF 版本的混合标识设计注意事项指南。 

