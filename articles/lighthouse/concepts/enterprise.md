---
title: 企业应用场景中的 Azure Lighthouse
description: Azure Lighthouse 的功能可以简化使用多个 Azure AD 租户的企业中的跨租户管理。
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 3f452e6810fa6809b5ba1b83b664f8b38d82a895
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88167343"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>企业应用场景中的 Azure Lighthouse

[Azure Lighthouse](../overview.md)的常见方案是服务提供商管理其客户 Azure Active Directory (Azure AD) 租户中的资源。 但是，Azure Lighthouse 的功能还可用于简化企业内使用多个 Azure AD 租户的跨租户管理。

## <a name="single-vs-multiple-tenants"></a>单个租户与多个租户

对于大多数组织而言，单个 Azure AD 租户的管理比较轻松。 将所有资源部署在一个租户中可按该租户中的指定用户、用户组或服务主体来集中处理管理任务。 我们建议尽量对组织使用一个租户。 但是，某些组织可能有多个 Azure AD 租户。 有时这可能是一种暂时性的情况，因为在进行了收购并尚未定义长期的租户合并策略时。 其他情况下，由于完全独立的子公司、地理位置或法律要求或其他注意事项，组织可能需要持续维护多个租户。

在需要多租户体系结构的情况下，Azure Lighthouse 可帮助集中和简化管理操作。 通过使用 [Azure 委托资源管理](azure-delegated-resource-management.md)，一个管理租户中的用户可以采用集中、可缩放的方式执行 [跨租户管理功能](cross-tenant-management-experience.md) 。

## <a name="tenant-management-architecture"></a>租户管理体系结构

若要在企业中使用 Azure Lighthouse，需要确定哪个租户将包含对其他租户执行管理操作的用户。 换句话说，您需要确定哪个租户将是其他租户的管理租户。

例如，假设你的组织有一个租户，我们将调用 *租户 a*。然后，你的组织将获得 *租户 B* 和 *租户 C*，并且你的业务原因要求你将它们维护为单独的租户。

你的组织希望在所有租户中使用相同的策略定义、备份实践和安全流程。 由于租户 A 已包括负责这些任务的用户，因此你可以在租户 B 和租户 C 内加入订阅，以允许租户 A 中的相同用户执行这些任务。

![显示租户 B 中的用户和租户 C 中的管理资源的关系图。](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>安全和访问注意事项

在大多数企业方案中，你将需要委托 Azure Lighthouse 的完整订阅。 你还可以选择仅委托订阅中的特定资源组。

无论采用哪种方式， [在定义哪些用户将有权访问委派的资源时，请确保遵循最低权限原则](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)。 这有助于确保用户只拥有执行所需任务而需要的权限，并可以减少意外出错的可能性。

Azure Lighthouse 仅提供管理租户和托管租户之间的逻辑链接，而不是物理移动数据或资源。 此外，访问始终只朝一个方向进行，即，从管理租户访问被管理租户。  在对被管理租户的资源执行管理操作时，管理租户中的用户和组应继续使用多重身份验证。

实施内部或外部监管与合规性准则的企业可以使用 [Azure 活动日志](../../azure-monitor/platform/platform-logs-overview.md)来满足其透明性要求。 如果企业租户已建立管理和托管租户关系，则每个租户中的用户都可以查看记录的活动，以查看管理租户中用户执行的操作。

## <a name="onboarding-considerations"></a>载入注意事项

订阅 (或订阅中的资源组) 可通过部署 Azure 资源管理器模板或发布到 Azure Marketplace 的托管服务提供载入到 Azure Lighthouse。

因为企业用户通常会直接访问企业的租户，并且不需要市场或提升管理产品/服务，所以部署 Azure 资源管理器模板通常更快、更简单。 尽管 [载入指南](../how-to/onboard-customer.md) 指的是服务提供商和客户，但企业可以使用相同的过程来加入其租户。

如果需要，可以通过[将托管服务套餐发布到 Azure 市场](../how-to/publish-managed-services-offers.md)来登记企业中的租户。 为了确保该套餐仅适用于相应的租户，请务必将计划标记为“专用”。 使用私有计划，你可以为计划加入的每个租户提供订阅 Id，而其他人将无法获得你的产品/服务。

## <a name="terminology-notes"></a>术语注释

对于企业内的跨租户管理，可以了解如何将 Azure Lighthouse 文档中的服务提供商参考应用于企业内的管理租户，即包括将通过 Azure Lighthouse 管理其他租户中的资源的用户的租户。 同样，对客户的任何引用都可以被理解为适用于通过管理租户中的用户委派要管理的资源的租户。

例如，在上述示例中，可将租户 A 视为服务提供商租户（管理租户），将租户 B 和租户 C 视为客户租户。

在该示例中，具有相应权限的租户 A 用户可以在 Azure 门户的“我的客户”页中[查看和管理委托的资源](../how-to/view-manage-customers.md)。**** 同理，具有相应权限的租户 B 和租户 C 用户可以在 Azure 门户的“服务提供商”页中[查看和管理委托给租户 A 的资源](../how-to/view-manage-service-providers.md)。****

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
- 了解 [Azure 委派资源管理](azure-delegated-resource-management.md)。