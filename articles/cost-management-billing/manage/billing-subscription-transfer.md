---
title: 转移 Azure 订阅的计费所有权
description: 说明如何将 Azure 订阅的计费所有权转移到另一帐户。
keywords: 转移 Azure 订阅, Azure 转移订阅, 将 Azure 订阅转移到另一帐户, Azure 更改订阅所有者, 将 Azure 订阅转移到另一帐户, Azure 转移计费
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/23/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: ef497439d77fef63f4b0c902aee06428a30c5ff5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91276724"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>将 Azure 订阅的计费所有权转移到另一帐户

本文介绍将 Azure 订阅的计费所有权转移到另一帐户的所需步骤。 在转移订阅的计费所有权之前，请阅读[关于转移 Azure 订阅的计费所有权的简介](../understand/subscription-transfer.md)。

若要保留计费所有权，但要更改订阅的类型，请参阅[将 Azure 订阅切换到另一套餐](switch-azure-offer.md)。 若要控制谁可以访问订阅中的资源，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。

如果你是企业协议 (EA) 客户，则你的企业管理员可以在帐户之间转移订阅的计费所有权。 有关详细信息，请参阅[更改帐户所有者](ea-portal-get-started.md#change-account-owner)。

只有帐户的计费管理员可转移订阅的所有权。

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>转移 Azure 订阅的计费所有权

1. 以包含所要转移订阅的计费帐户的管理员身份登录到 [Azure 门户](https://portal.azure.com)。 如果你不确定你是否是管理员，或者需要确定谁是管理员，请参阅[确定帐户计费管理员](../understand/subscription-transfer.md#whoisaa)。
1. 搜索“成本管理 + 计费”。  
   ![显示 Azure 门户搜索的屏幕截图](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. 在左侧窗格中选择“订阅”。 根据你的访问权限，可能需要选择一个计费范围，然后选择“订阅”或“Azure 订阅”。 
1. 选择要转移的订阅对应的“转移计费所有权”。  
   ![选择要转移的订阅](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. 输入充当帐户计费管理员的用户（该用户将是订阅的新所有者）的电子邮件地址。
1. 若要将订阅转移到另一个 Azure AD 租户中的帐户，请选择是否要将该订阅移到新帐户的租户。 有关详细信息，请参阅[将订阅转移到另一个 Azure AD 租户中的帐户](#transfer-a-subscription-to-another-azure-ad-tenant-account)。
    > [!IMPORTANT]
    > 如果选择将订阅移到新帐户的 Azure AD 租户，将会永久删除用于访问订阅中的资源的所有 [Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)。 只有新帐户中接受了转移请求的用户才有权管理订阅中的资源。 或者，可以清除“订阅 Azure AD 租户”选项，以便在不将订阅移到新帐户的租户的情况下转移计费所有权。 如果这样做，将保留访问 Azure 资源的现有 Azure 角色分配。  
    ![“发送转移请求”页](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. 选择“发送转移请求”。
1. 用户将收到一封电子邮件，其中包含查看转让请求的说明。  
   ![发送给收件人的订阅转移电子邮件](./media/billing-subscription-transfer/billing-receiver-email.png)
1. 若要批准转移请求，用户需选择电子邮件中的链接，并按说明操作。 然后，用户选择用于支付订阅费用的付款方式。 如果用户没有 Azure 帐户，则他们必须注册一个新帐户。  
   ![第一个订阅转移网页](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![第二个订阅转移网页](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![第三个订阅转移网页](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. 成功！ 订阅现已转让。

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>将订阅转移到另一个 Azure AD 租户帐户

注册 Azure 时，会创建一个 Azure Active Directory (AD) 租户。 该租户代表你的帐户。 使用该租户可以管理对订阅和资源的访问权限。

创建新订阅时，该订阅将托管在帐户的 Azure AD 租户中。 若要为其他用户提供对你的订阅或其资源的访问权限，需要邀请他们加入你的租户。 这有助于控制对订阅和资源的访问。

将订阅的计费所有权转移到另一个 Azure AD 租户中的帐户时，可将订阅移到新帐户的租户。 这样，以前拥有管理订阅及其资源的 [Azure 角色分配](../../role-based-access-control/role-assignments-portal.md) 的用户、组或服务主体将失去其访问权限。 只有新帐户中接受了转移请求的用户才有权管理这些资源。 新所有者必须手动将这些用户添加到订阅，才能为他们提供失去的访问权限。 有关详细信息，请参阅[将 Azure 订阅转移到其他 Azure AD 目录（预览版）](../../role-based-access-control/transfer-subscription.md)。

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>转移 Visual Studio 和合作伙伴网络订阅

Visual Studio 和 Microsoft 合作伙伴网络订阅具有关联的每月定期 Azure 额度。 转移这些订阅时，你的额度在目标计费帐户中不可用。 订阅使用目标计费帐户中的额度。 例如，如果 Bob 在 9 月 9 日将 Visual Studio Enterprise 订阅转移到了 Jane 的帐户，而 Jane 接受了这种转移。 转移完成后，订阅将开始使用 Jane 帐户中的额度。 该额度在每个月的 9 号重置。

## <a name="next-steps-after-accepting-billing-ownership"></a>接受计费所有权后的步骤

如果你已接受 Azure 订阅的计费所有权，我们建议查看以下后续步骤：

1. 查看和更新服务管理员、共同管理员和 Azure 角色分配。 有关详细信息，请参阅[添加或更改 Azure 订阅管理员](add-change-subscription-administrator.md)和[使用 Azure 门户添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)。
1. 更新与此订阅的服务关联的凭据，包括：
   1. 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../../cloud-services/cloud-services-certs-create.md)
   1. 存储空间等服务的访问密钥。 有关详细信息，请参阅[关于 Azure 存储帐户](../../storage/common/storage-create-storage-account.md)
   1. Azure 虚拟机等服务的远程访问凭据。
1. 若正与合作伙伴合作，请考虑更新此订阅的合作伙伴 ID。 可以在 [Azure 门户](https://portal.azure.com)中更新合作伙伴 ID。 有关详细信息，请参阅[将合作伙伴 ID 链接到 Azure 帐户](link-partner-id.md)。

## <a name="troubleshooting"></a>疑难解答

如果在转移订阅时遇到问题，请使用以下故障排除信息。

### <a name="the-transfer-subscription-option-is-unavailable"></a>“转移订阅”选项不可用

<a name="no-button"></a> 

自助式服务订阅转移不适用于你的计费帐户。 目前，我们不支持在 Azure 门户上转移企业协议 (EA) 帐户中订阅的计费所有权。 此外，在 Microsoft 代表的配合下创建的 Microsoft 客户协议帐户不支持转移计费所有权。

###  <a name="not-all-subscription-types-can-transfer"></a>并非所有订阅类型都可以转移

并非所有类型的订阅都支持计费所有权转移。 若要查看支持转移的订阅类型列表，请参阅[支持的订阅类型](../understand/subscription-transfer.md#supported-subscription-types)

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>尝试转移订阅计费所有权时显示“拒绝访问”错误

如果你尝试转移 Microsoft Azure 计划订阅，但没有必要的权限，则会看到此错误。 若要转移 Microsoft Azure 计划订阅，你需是要从中计收订阅费用的发票科目的所有者或参与者。 有关详细信息，请参阅[管理发票科目的订阅](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 查看和更新服务管理员、共同管理员和 Azure 角色分配。 有关详细信息，请参阅[添加或更改 Azure 订阅管理员](add-change-subscription-administrator.md)和[使用 Azure 门户添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)。
