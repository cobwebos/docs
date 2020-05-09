---
title: Azure 顾问简介
description: 使用 Azure 顾问优化 Azure 部署。
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 74048073677cdf0f9f57d84469959a84e78cd6c7
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854430"
---
# <a name="introduction-to-azure-advisor"></a>Azure 顾问简介

了解 Azure 顾问的主要功能，获取常见问题的答案。

## <a name="what-is-advisor"></a>顾问是什么？
顾问是一种个性化的云顾问，可帮助遵循最佳做法来优化 Azure 部署。 它可分析资源配置和遥测使用情况，并推荐解决方案，有助于提高 Azure 资源的经济效益、性能、高可用性和安全性。

使用顾问可以：
* 获取主动的、可操作的以及个性化的最佳做法建议。 
* 提高资源的性能、安全性和高可用性，同时确定机会减少总体 Azure 支出。
* 通过提议的内联操作获取建议。

可通过 [Azure 门户](https://aka.ms/azureadvisordashboard)访问顾问。 登录到[门户](https://portal.azure.com)，在导航菜单中查找“顾问”，或在“所有服务”菜单中搜索它   。

顾问仪表板显示针对所有订阅的个性化建议。  可为特定的订阅和资源类型应用筛选器，以显示相关建议。  建议分为五类： 

* **高可用性**：确保并提高关键业务应用程序的连续性。 有关详细信息，请参阅[顾问高可用性建议](advisor-high-availability-recommendations.md)。
* **安全性**：检测可能引起安全问题的威胁和漏洞。 有关详细信息，请参阅[顾问安全性建议](advisor-security-recommendations.md)。
* **性能**：提高应用程序的速度。 有关详细信息，请参阅[顾问性能建议](advisor-performance-recommendations.md)。
* 成本：优化并降低 Azure 总支出  。 有关详细信息，请参阅[顾问成本建议](advisor-cost-recommendations.md)。
* **卓越运营**：帮助实现过程和工作流效率、资源可管理性和部署最佳实践。 . 有关详细信息，请参阅[Advisor 操作优秀建议](advisor-operational-excellence-recommendations.md)。

  ![顾问建议类型](./media/advisor-overview/advisor-dashboard.png)

可以单击“类别”以显示此类别建议的列表，并选择某个建议以了解相关详细信息。  还可了解可执行的操作，通过这些操作获取商机或解决问题。

![顾问建议类别](./media/advisor-overview/advisor-ha-category-example.png) 

选择推荐的操作以实现该建议。  系统将打开一个简单界面以帮助你实现建议，或为你提供有助于实现建议的参考文档。  顾问确认建议已实现的时间可能长达一天。

如果不打算立即采取建议的操作，可将它推迟指定时间段或将其消除。  如果不想接收某个特定订阅或资源组的相关建议，可通过配置，使顾问仅生成某些指定订阅和资源组的相关建议。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-do-i-access-advisor"></a>如何访问顾问？
可以通过[Azure 门户](https://aka.ms/azureadvisordashboard)访问顾问。 登录到[门户](https://portal.azure.com)，在导航菜单中查找“顾问”，或在“所有服务”菜单中搜索它********。

还可通过虚拟机资源接口查看顾问建议。 选择虚拟机，并滚动到菜单中的顾问建议。 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>需要哪些权限才能访问顾问？
 
可以访问订阅的所有者、参与者或读者身份访问顾问建议******。

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>顾问为哪些资源提供建议？

顾问提供了应用程序网关、应用服务、可用性集、Azure 缓存、Azure 数据工厂、Azure Database for MySQL、Azure Database for PostgreSQL、Azure Database for MariaDB、Azure ExpressRoute、Azure Cosmos DB、Azure 公共 IP 地址、SQL 数据仓库、SQL server、存储帐户、流量管理器配置文件和虚拟机的建议。

Azure 顾问还包括来自 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-recommendations)的建议，其中可能包含有关其他资源类型的建议。

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>是否可以推迟或消除建议？

若要推迟或消除建议，请单击“推迟”链接****。 可以指定推迟时间段，或选择“从不”**** 以消除该建议。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：

* [顾问入门](advisor-get-started.md)
* [顾问高可用性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问成本建议](advisor-cost-recommendations.md)
