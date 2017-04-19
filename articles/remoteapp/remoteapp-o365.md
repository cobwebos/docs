---
title: "结合使用 Office 和 Azure RemoteApp | Microsoft Docs"
description: "了解 Office 和 Azure RemoteApp 如何协同工作"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: f1773baf-8aa1-423c-a2f9-e4679e0463d3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 5a8af257eb964be4677d5da6a57aadaad1eb2cc3
ms.lasthandoff: 03/31/2017


---
# <a name="using-office-with-azure-remoteapp"></a>结合使用 Office 和 Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

有两种方法在 Azure RemoteApp 中托管 Office 应用程序：Office 365 ProPlus 或 Office 2013 Professional Plus 试用版。

**你好，你是否知道我们有一篇新的更好的文章即将替代此文章？请查看[如何将 Office 365 订阅与 Azure RemoteApp 一起使用](remoteapp-officesubscription.md)，其中介绍了使用 Office 365 + Azure RemoteApp 所需的所有信息。**

## <a name="office-365-proplus"></a>Office 365 ProPlus
你可以使用 Office 365 ProPlus 模板映像创建 RemoteApp 集合。 此选项允许你将 Office 365 服务扩展到 RemoteApp。 你必须具有现有的订阅计划，而且你的用户必须获得 Office 365 ProPlus 服务的许可，无论是单机许可还是通过 Office 365 服务计划获得许可。

RemoteApp 支持 Office 365 共享计算机激活。 当你启用共享计算机激活并使用 [Office 部署工具](http://www.microsoft.com/download/details.aspx?id=36778)进行安装时，无需激活即可安装 Office 365 ProPlus。 当用户登录到包含 Office 365 的集合时，Office 会检查是否已经为 Office 365 ProPlus 设置了用户。 如果已设置，则 Office 会暂时激活 Office 365 ProPlus - 此激活会一直保持，直到该用户注销服务。

若要使用 Office 365 共享计算机激活，你需要遵循[这些方向](https://technet.microsoft.com/library/dn782858.aspx)创建[自定义模板](remoteapp-create-custom-image.md)并安装 Office 365 ProPlus。

你可以在 [Office 365 管理门户](https://portal.office365.com/)管理用户的 Office 365 许可证。 阅读有关 [Office 365 服务计划](http://technet.microsoft.com/library/office-365-plan-options.aspx)的详细信息。  

## <a name="office-2013-professional-plus-trial"></a>Office 2013 Professional Plus 试用版
在 RemoteApp 的 30 天试用期内，你可以使用 Office 2013 Professional Plus（试用版）模板映像创建 RemoteApp 集合。 你可以使用用户的 Azure Active Directory 工作帐户或 Microsoft 帐户将其分配到此试用版集合。 无需任何其他订阅。

此选项非常适合用于开始试用并在 RemoteApp 中获取 Office 的良好体验。 但是，此选项仅用于评估和测试。 使用 Office 2013 Professional Plus（试用版）模板映像创建的 RemoteApp 集合不能转换到生产模式，而且在试用期结束时将被禁用。

## <a name="switching-from-trial-to-production"></a>从试用版切换到生产模式
当你开始 30 天的免费试用时，该门户的 RemoteApp 部分中的注释将告诉你试用期还有多长时间，之后你需要转换为付费帐户。 你可以激活你的帐户，并使用此注释中的链接切换到生产模式。

激活你的帐户时，这将影响你的帐户中的所有 RemoteApp 集合。

* 使用 Windows Server 2012 R2 或 Office 365 ProPlus 模板映像运行的集合将无缝转换到生产模式。 所有用户数据和设置（包括正在进行的会话）都保持不变。
* 如果你已上载自定义模板映像，这使用这些映像的集合也将无缝转换。
* Office 2013 Professional Plus（试用版）模板映像仅用于评估。 使用此模板映像运行的集合不能转换到生产模式。 它们将处于“已禁用”状态。

如果在试用版过期之前不转换到生产模式，RemoteApp 集合将被禁用。 别担心 - 你的设置和用户的数据会再保存 90 天，因此，你仍然可以激活你的服务，并切换到生产模式，而不会丢失任何数据。


