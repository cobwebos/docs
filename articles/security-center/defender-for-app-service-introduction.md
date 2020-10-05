---
title: 适用于应用服务的 Azure Defender - 优点和功能
description: 适了解适用于应用服务的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c53f7e712668f32766feaf76d6a08582bda9af22
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91449103"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>用于应用服务的 Azure Defender 简介

Azure 应用服务是一种完全托管的平台，用于生成和托管 Web 应用和 API，无需考虑基础结构的管理。 它提供管理、监视和操作见解，以满足企业级的性能、安全性和符合性要求。 有关详细信息，请参阅 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)。

用于应用服务的 Azure Defender 使用云的规模来识别针对应用服务运行的应用程序受到的攻击。 攻击者会探查 Web 应用程序，以找出并恶意利用其中的弱点。 向 Azure 中运行的应用程序发出的请求在路由到特定的环境之前会流经多个网关，网关会对其进行检查并记录其状态。 然后，将使用这些数据来识别恶意利用行为和攻击者，并了解稍后要使用的新模式。

凭借云提供商 Azure 提供的可见性，安全中心可以分析应用服务的内部日志，以识别多个目标上的攻击方法。 例如，方法包括大范围扫描和分布式攻击。 此类攻击通常来自一小部分 IP，会展示多个主机上类似终结点的爬网模式。 攻击会搜索有漏洞的页面或插件，从单个主机的角度无法识别到。


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|预览|
|定价：|[用于应用服务的 Azure Defender](azure-defender.md) 按[定价页中](security-center-pricing.md)的定价计费|
|支持的应用服务计划：|![是](./media/icons/yes-icon.png)“基本”、“标准”、“高级”、“独立”或“Linux”<br>![否](./media/icons/no-icon.png)“免费”、“共享”或“消耗”<br>[了解有关应用服务计划的详细信息](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>适用于应用服务的 Azure Defender 可以保护什么？

启用应用服务计划后，安全中心会评估应用服务计划涵盖的资源并根据其发现生成安全建议。 安全中心保护运行应用服务的 VM 实例以及管理接口。 此外，它还监视向/从应用服务中运行的应用发送的请求和响应。

如果运行基于 Windows 的应用服务计划，则安全中心还可以访问底层沙盒与 VM。 结合上面所述的日志数据，基础结构可以讲述从四处传播的新攻击，到客户计算机遭到入侵的整个历程。 因此，即使是在 Web 应用被恶意利用后再部署安全中心，它也能够检测不断发生的攻击。


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>保护 Azure 应用服务 web 应用和 API
若要通过适用于应用服务的 Azure Defender 保护 Azure 应用服务计划：

- 确保具有与专用计算机关联的受支持的应用服务计划。 上文中的[可用性](#availability)中列出了支持的计划。

- 根据 [Azure 安全中心定价](security-center-pricing.md)中的说明在订阅上启用 Azure Defender（可选择只启用适用于应用服务的 Azure Defender 计划）

安全中心原生与应用服务集成，因此不需要部署和载入 - 集成是透明的。

>[!NOTE]
> “定价和设置”页列出了“资源数量”的实例数。 这表示当你打开“定价层”页时，此订阅的所有应用服务计划中运行的计算实例总数。
>
> Azure 应用服务提供各种计划。 应用服务计划为要运行的 Web 应用定义一组计算资源。 它们等效于传统 Web 托管中的服务器场。 可将一个或多个应用配置为在相同的计算资源中（或相同的应用服务计划中）运行。
>
>若要验证该计数，可转到 Azure 门户中的“应用服务计划”，在其中查看每个计划使用的计算实例数。 



## <a name="next-steps"></a>后续步骤

本文介绍了适用于应用服务的 Azure Defender。 

如需相关材料，请参阅以下文章： 

- 无论警报是由安全中心生成，还是由安全中心从其他安全产品接收，你都可以导出该警报。 若要将警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。
- 有关 Azure 应用服务警报的列表，请参阅[警报参考表](alerts-reference.md#alerts-azureappserv)。
- 有关应用服务计划的详细信息，请参阅[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/plans/)。
- > [!div class="nextstepaction"]
    > [启用 Azure Defender](security-center-pricing.md)