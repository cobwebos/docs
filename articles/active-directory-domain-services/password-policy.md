---
title: 在 Azure AD 域服务中创建和使用密码策略 |微软文档
description: 了解如何以及为什么使用细粒度密码策略来保护和控制 Azure AD DS 托管域中的帐户密码。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: b14fed07c9bd9b5fcb6a5489719481902351fc0d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654868"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>托管域中的密码和帐户锁定策略

要管理 Azure 活动目录域服务 （Azure AD DS） 中的用户安全性，可以定义控制帐户锁定设置或最小密码长度和复杂性的细粒度密码策略。 将创建默认细粒度密码策略，并将其应用于 Azure AD DS 托管域中的所有用户。 为了提供精细控制并满足特定业务或合规性需求，可以创建其他策略并将其应用于特定的用户组。

本文介绍如何使用活动目录管理中心在 Azure AD DS 中创建和配置细粒度密码策略。

> [!NOTE]
> 密码策略仅适用于使用资源管理器部署模型创建的 Azure AD DS 托管域。 对于使用经典创建的较旧的托管域，[从经典虚拟网络模型迁移到资源管理器][migrate-from-classic]。

## <a name="before-you-begin"></a>在开始之前

要完成本文，您需要以下资源和特权：

* 一个有效的 Azure 订阅。
  * 如果没有 Azure 订阅，[请创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
  * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
  * 如果需要，请完成创建[和配置 Azure 活动目录域服务实例][create-azure-ad-ds-instance]的教程。
  * Azure AD DS 实例必须使用资源管理器部署模型创建。 如果需要，[从经典虚拟网络模型迁移到资源管理器][migrate-from-classic]。
* 加入到 Azure AD DS 托管域的 Windows 服务器管理 VM。
  * 如果需要，请完成教程以创建[管理 VM][tutorial-create-management-vm]。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。**

## <a name="default-password-policy-settings"></a>默认密码策略设置

细粒度密码策略 （FGP） 允许您对域中的不同用户应用密码和帐户锁定策略的特定限制。 例如，要保护特权帐户，您可以应用比常规非特权帐户更严格的帐户锁定设置。 您可以在 Azure AD DS 托管域中创建多个 FGP，并指定优先级顺序以将其应用于用户。

有关密码策略和使用活动目录管理中心的详细信息，请参阅以下文章：

* [了解细粒度密码策略](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 管理中心配置细粒度密码策略](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

策略通过 Azure AD DS 托管域中的组关联分发，所做的任何更改都应用于下一个用户登录。 更改策略不会解锁已锁定的用户帐户。

密码策略的行为略有不同，具体取决于它们应用于的用户帐户的创建方式。 在 Azure AD DS 中创建用户帐户有两种方式：

* 可以从 Azure AD 同步用户帐户。 这包括直接在 Azure 中创建的仅云用户帐户，以及使用 Azure AD Connect 从本地 AD DS 环境同步的混合用户帐户。
    * Azure AD DS 中的大多数用户帐户都是通过 Azure AD 的同步过程创建的。
* 用户帐户可以在 Azure AD DS 托管域中手动创建，并且在 Azure AD 中不存在。

所有用户，无论其创建方式如何，都应用了 Azure AD DS 中的默认密码策略应用的以下帐户锁定策略：

* **帐户锁定持续时间：** 30
* **允许的失败登录尝试次数：** 5
* **重置失败登录尝试计数后：** 30 分钟
* **最大密码期限（生存期）：90**天

使用这些默认设置，如果在 2 分钟内使用 5 个无效密码，用户帐户将锁定 30 分钟。 帐户将在 30 分钟后自动解锁。

帐户锁定仅在托管域中发生。 用户帐户仅在 Azure AD DS 中锁定，并且仅由于针对托管域的登录尝试失败。 从 Azure AD 或本地同步的用户帐户不会锁定在其源目录中，仅在 Azure AD DS 中。

如果 Azure AD 密码策略指定最大密码期限大于 90 天，则该密码期限将应用于 Azure AD DS 中的默认策略。 可以配置自定义密码策略，以在 Azure AD DS 中定义不同的最大密码期限。 如果 Azure AD DS 密码策略中配置的最大密码期限比 Azure AD 或本地 AD DS 环境中的密码期限短，请小心。 在这种情况下，用户的密码可能会在 Azure AD DS 中过期，然后再提示他们在 Azure AD 或本地 AD DS 环境中进行更改。

对于在 Azure AD DS 托管域中手动创建的用户帐户，也会从默认策略中应用以下附加密码设置。 这些设置不适用于从 Azure AD 同步的用户帐户，因为用户无法直接在 Azure AD DS 中更新其密码。

* **最小密码长度（字符）：** 7
* **密码必须符合复杂性要求**

无法修改默认密码策略中的帐户锁定或密码设置。 相反 *，AAD DC 管理员*组的成员可以创建自定义密码策略，并将其配置为覆盖（优先于）默认的内置策略，如下一节所示。

## <a name="create-a-custom-password-policy"></a>创建自定义密码策略

在 Azure 中生成和运行应用程序时，可能需要配置自定义密码策略。 例如，您可以创建策略来设置不同的帐户锁定策略设置。

自定义密码策略应用于 Azure AD DS 托管域中的组。 此配置有效地覆盖默认策略。

要创建自定义密码策略，请使用加入域的 VM 中的 Active Directory 管理工具。 活动目录管理中心允许您在 Azure AD DS 托管域（包括 OEM）中查看、编辑和创建资源。

> [!NOTE]
> 要在 Azure AD DS 托管域中创建自定义密码策略，必须登录到*属于 AAD DC 管理员*组成员的用户帐户。

1. 在"开始"屏幕中，选择 **"管理工具**"。 在本教程中显示了用于[创建管理 VM][tutorial-create-management-vm]的可用管理工具的列表。
1. 要创建和管理 O，请从管理工具列表中选择**活动目录管理中心**。
1. 在左侧窗格中，选择 Azure AD DS 托管域，如*aaddscontoso.com*。
1. 打开**系统**容器，然后打开**密码设置容器**。

    将显示 Azure AD DS 托管域的内置密码策略。 无法修改此内置策略。 而是创建自定义密码策略以覆盖默认策略。

    ![在活动目录管理中心创建密码策略](./media/password-policy/create-password-policy-adac.png)

1. 在右侧的 **"任务"** 面板中，选择 **"新>密码设置**"。
1. 在 **"创建密码设置"** 对话框中，输入策略的名称，如*MyCustomFGPP*。
1. 如果存在多个密码策略，则具有最高优先级或优先级的策略将应用于用户。 编号越低，优先级越高。 默认密码策略的优先级为*200*。

    将自定义密码策略的优先级设置为覆盖默认值，例如*1*。

1. 根据需要编辑其他密码策略设置。 记住以下要点：

    * 密码复杂性、期限或过期时间等设置仅对在 Azure AD DS 托管域中手动创建的用户进行设置。
    * 帐户锁定设置适用于所有用户，但仅在托管域中生效，而不是在 Azure AD 本身中生效。

    ![创建自定义细粒度密码策略](./media/password-policy/custom-fgpp.png)

1. 取消选中**防止意外删除**。 如果选择了此选项，则无法保存 FGPP。
1. 在"**直接应用于"** 部分中，选择"**添加**"按钮。 在 **"选择用户或组**"对话框中，选择"**位置**"按钮。

    ![选择要将密码策略应用于](./media/password-policy/fgpp-applies-to.png)

1. 密码策略只能应用于组。 在 **"位置"** 对话框中，展开域名（如*aaddscontoso.com*，然后选择 OU，如**AADDC 用户**。 如果您有一个自定义 OU，其中包含要应用的一组用户，请选择该 OU。

    ![选择组所属的 OU](./media/password-policy/fgpp-container.png)

1. 键入要应用策略的组的名称，然后选择 **"检查名称"** 以验证该组是否存在。

    ![搜索并选择要应用 FGPP 的组](./media/password-policy/fgpp-apply-group.png)

1. 使用您现在选择的组的名称显示在 **"直接应用于"** 部分中，选择 **"确定"** 以保存自定义密码策略。

## <a name="next-steps"></a>后续步骤

有关密码策略和使用活动目录管理中心的详细信息，请参阅以下文章：

* [了解细粒度密码策略](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 管理中心配置细粒度密码策略](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
