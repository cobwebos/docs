---
title: "Azure 顾问简介 | Microsoft Docs"
description: "使用 Azure 顾问优化 Azure 部署。"
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 96925f251cf4984a11516a962740e19a7b9589dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-advisor"></a>Azure 顾问简介

了解 Azure 顾问及其主要功能，获取常见问题的答案。

## <a name="what-is-advisor"></a>顾问是什么？
顾问是一种个性化的云顾问，可帮助遵循最佳做法来优化 Azure 部署。 它可分析资源配置和遥测使用情况，并推荐解决方案，有助于提高 Azure 资源的经济效益、性能、高可用性和安全性。

使用顾问可以：
* 获取主动的、可操作的以及个性化的最佳做法建议。 
* 提高资源的性能、安全性和高可用性，同时确定机会减少总体 Azure 支出。
* 通过提议的内联操作获取建议。

可通过 [Azure 门户](https://aka.ms/azureadvisordashboard)访问顾问。 登录到[门户](https://portal.azure.com)，选择“浏览”，并滚动到“Azure 顾问”。 顾问仪表板显示针对所选订阅的个性化建议。 

建议分为四类： 

* **高可用性**：确保并提高关键业务应用程序的连续性。 有关详细信息，请参阅[顾问高可用性建议](advisor-high-availability-recommendations.md)。

* **安全性**：检测可能引起安全问题的威胁和漏洞。 有关详细信息，请参阅[顾问安全性建议](advisor-security-recommendations.md)。

* **性能**：提高应用程序的速度。 有关详细信息，请参阅[顾问性能建议](advisor-performance-recommendations.md)。

* **成本**：优化并降低 Azure 总支出。 有关详细信息，请参阅[顾问成本建议](advisor-cost-recommendations.md)。

  ![顾问建议类型](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> 若要访问顾问建议，必须先向顾问*注册订阅*。 订阅所有者启动顾问仪表板并单击“获取建议”按钮，即可注册订阅。 这是*一次性操作*。 注册订阅后，可通过订阅、资源组或特定资源的所有者、参与者或读者身份访问顾问建议。

可以单击一个建议，了解有关它的详细信息。 还可了解可执行的操作，通过这些操作获取商机或解决问题。 

顾问通过内联操作或文档链接提供建议。 单击内联操作，转到“用户指南之旅”以实现该操作。 单击文档链接会转到介绍如何手动实现该操作的文档。 

顾问每小时更新建议。 如果不打算立即采取建议的操作，可将它推迟指定时间段或解除。 

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-do-i-access-advisor"></a>如何访问顾问？
可通过 [Azure 门户](https://aka.ms/azureadvisordashboard)访问顾问。 登录到[门户](https://portal.azure.com)，选择“浏览”，并滚动到“Azure 顾问”。 顾问仪表板显示针对所选订阅的个性化建议。 

还可通过虚拟机资源边栏选项卡查看顾问建议。 选择虚拟机，并滚动到菜单中的顾问建议。 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>需要哪些权限才能访问顾问？

若要访问顾问建议，必须先向顾问*注册订阅*。 订阅所有者启动顾问仪表板并单击“获取建议”按钮，即可注册订阅。 这是*一次性操作*。 注册订阅后，可通过订阅、资源组或特定资源的所有者、参与者或读者身份访问顾问建议。

### <a name="how-often-are-advisor-recommendations-updated"></a>顾问建议更新的频率是多久？

顾问建议每小时更新。

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>顾问为哪些资源提供建议？

顾问为虚拟机、可用性集、应用程序网关、应用服务、SQL 服务器、SQL 数据库和 Redis 缓存提供建议。

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>是否可以暂停或关闭建议？

若要暂停或关闭建议，请单击“暂停”按钮或链接。 可指定暂停时间段或选择“从不”以关闭该建议。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：

* [顾问入门](advisor-get-started.md)
* [顾问高可用性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问成本建议](advisor-cost-recommendations.md)
