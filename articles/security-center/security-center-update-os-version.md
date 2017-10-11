---
title: "在 Azure 安全中心更新 OS 版本 | Microsoft 文档"
description: "本文档演示如何实现 Azure 安全中心建议**更新 OS 版本**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
ms.openlocfilehash: ce0d178914907750e5da59f223a4b1e04b9bb6fb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="update-os-version-in-azure-security-center"></a>在 Azure 安全中心更新 OS 版本
对于云服务中的虚拟机 (VM)，如果存在可用的较新版本的操作系统 (OS)，Azure 安全中心将建议对其进行更新。  仅监视云服务 web 和在生产槽运行的辅助角色。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  这并非一份循序渐进的指南。
> 
> 

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡中，选择“更新 OS 版本”。
   ![更新 OS 版本][1]
2. 这会打开“更新 OS 版本”边栏选项卡。 按照此边栏选项卡中的步骤更新 OS 版本。

## <a name="see-also"></a>另请参阅
本文档演示如何实现安全中心建议“更新 OS 版本”。 若要了解有关云服务和更新云服务的 OS 版本的详细信息，请参阅：

* [云服务概述](../cloud-services/cloud-services-choose-me.md)
* [如何更新云服务](../cloud-services/cloud-services-update-azure-service.md)
* [如何配置云服务](../cloud-services/cloud-services-how-to-configure-portal.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md)了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) -- 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md)查找有关使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/)获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
