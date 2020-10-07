---
title: 链接合作伙伴 ID 以启用委托资源的合作伙伴获得信用额度
description: 了解如何关联你的合作伙伴 ID 以接收通过 Azure Lighthouse 管理的客户资源的合作伙伴获得的信用 (PEC) 。
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 5caa205ce74152c7ec047952f66c1bf9188ddf02
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776164"
---
# <a name="link-your-partner-id-to-enable-partner-earned-credit-on-delegated-resources"></a>链接合作伙伴 ID 以启用委托资源的合作伙伴获得信用额度

如果你是 [Microsoft 合作伙伴网络](https://partner.microsoft.com/)的成员，则可以将合作伙伴 ID 链接到用于管理委派的客户资源的凭据。 这样，你就可以跟踪客户约定的影响，并 [ (PEC) 为托管服务接收合作伙伴获得的信用额度 ](/partner-center/partner-earned-credit)。

如果你 [在 Azure Marketplace 中为客户提供托管服务产品/服务](publish-managed-services-offers.md)，则此链接将使用与用于发布产品/服务的合作伙伴中心帐户关联的 MPN ID 自动发生。 对于这些客户，无需执行其他操作即可接收 PEC。

如果你 [使用 Azure 资源管理模板来加入客户](onboard-customer.md)，则需要采取措施来创建此链接。 完成此操作的方法是：将 [MPN ID](../../cost-management-billing/manage/link-partner-id.md) 与管理租户中至少一个用户帐户链接到你的每个载入订阅。

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>在加入新客户时关联你的合作伙伴 ID

使用 Azure 资源管理器模板为客户加入 (ARM 模板) ，请使用以下过程链接你的合作伙伴 ID 并启用合作伙伴获得的信用额度。 若要完成这些步骤，需要了解 [MPN 合作伙伴 ID](/partner-center/partner-center-account-setup#locate-your-mpn-id) 。 请确保使用伙伴配置文件中显示的 **关联 MPN ID** 。

为简单起见，我们建议在租户中创建一个服务主体帐户，将其链接到 **关联的 MPN ID**，然后向其授予对你所登记的每个客户的访问权限， [该内置角色适用于 PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)。

1. 在管理租户中[创建服务主体帐户](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。 在此示例中，我们将命名此服务主体 PEC Automation 帐户。
1. 使用该服务主体帐户， [链接到](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) 管理租户中关联的 MPN ID。 只需执行此操作一次。
1. 当你 [使用 ARM 模板加入客户](onboard-customer.md)时，请确保加入一种授权，其中包含 Pec 自动化帐户，作为具有 [适用于 pec 的 Azure 内置角色](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)的用户。

通过执行这些步骤，你管理的每个客户租户都将与你的合作伙伴 ID 相关联，从而允许你为这些客户接收 PEC。 PEC 自动化帐户无需在客户租户中进行身份验证或执行任何操作。

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>将你的合作伙伴 ID 添加到之前载入的客户

如果已载入客户，你可能不想要执行其他部署来添加 PEC Automation 帐户服务主体。 相反，你可以使用已有权在该客户的租户中工作的用户帐户链接 **关联的 MPN ID** 。 确保该帐户已被授予可 [用于 PEC 的 Azure 内置角色](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)。

一旦帐户已链接到管理租户中 [关联的 MPN ID](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) ，就可以接收该客户的 PEC。

## <a name="confirm-partner-earned-credit"></a>确认合作伙伴获得的信用额度

可以 [在 Azure 门户中查看 pec 详细信息](/partner-center/partner-earned-credit-explanation#azure-cost-management) ，并确认哪些成本已获得 pec 权益。

如果按照上述步骤操作，但未看到关联，请在 Azure 门户中打开支持请求。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Microsoft 合作伙伴网络](/partner-center/mpn-overview)。
- 了解 [如何计算和支付 PEC](/partner-center/partner-earned-credit-explanation)。
- 将[客户](onboard-customer.md)加入 Azure Lighthouse。
