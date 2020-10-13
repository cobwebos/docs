---
title: 在 Azure AD 域服务中创建和使用密码策略 | Microsoft Docs
description: 了解如何以及为何使用细化的密码策略来保护和控制 Azure AD DS 托管域中的帐户密码。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: e3e524df2e98229698a86a721b7312a4d054ff70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86040038"
---
# <a name="password-and-account-lockout-policies-on-active-directory-domain-services-managed-domains"></a>Active Directory 域服务托管域上的密码和帐户锁定策略

若要管理 Azure Active Directory 域服务 (Azure AD DS) 中的用户安全，可以定义细化的密码策略来控制帐户锁定设置或最短密码长度和复杂性。 将创建一个默认的细化密码策略，并将其应用于 Azure AD DS 托管域中的所有用户。 为了提供细化控制并满足特定的业务需求或合规性需求，可以创建更多的策略并将其应用于特定的用户组。

本文说明了如何使用 Active Directory 管理中心在 Azure AD DS 中创建和配置细化的密码策略。

> [!NOTE]
> 密码策略仅适用于使用资源管理器部署模型创建的托管域。 对于使用经典部署模型创建的旧托管域，请[从经典虚拟网络模型迁移到资源管理器][migrate-from-classic]。

## <a name="before-you-begin"></a>准备阶段

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
  * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
  * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
  * 如果需要，请完成[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]的教程。
  * 必须已使用资源管理器部署模型创建托管域。 如果需要，请[从经典虚拟网络模型迁移到资源管理器][migrate-from-classic]。
* 已加入托管域的 Windows Server 管理 VM。
  * 如果需要，请完成[创建管理 VM][tutorial-create-management-vm] 的教程。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。

## <a name="default-password-policy-settings"></a>默认密码策略设置

通过细化的密码策略 (FGPP)，可以对域中的不同用户应用特定的密码和帐户锁定策略限制。 例如，若要保护特权帐户，你可以应用比常规的非特权帐户更严格的帐户锁定设置。 可以在托管域中创建多个 FGPP，并指定将其应用于用户时的优先级顺序。

若要详细了解密码策略以及如何使用 Active Directory 管理中心，请参阅以下文章：

* [了解细化的密码策略](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 管理中心配置细化的密码策略](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

策略通过托管域中的组关联进行分发，并在用户下次登录时应用你所做的任何更改。 更改策略不会解锁已锁定的用户帐户。

根据应用密码策略的用户帐户的创建方式，密码策略的行为方式稍有不同。 可以通过两种方式在 Azure AD DS 中创建用户帐户：

* 可以从 Azure AD 将用户帐户同步进来。 这包括直接在 Azure 中创建的纯云用户帐户，以及使用 Azure AD Connect 从本地 AD DS 环境同步的混合用户帐户。
    * Azure AD DS 中的大部分用户帐户是通过同步过程从 Azure AD 创建的。
* 对于 Azure AD 中不存在的用户帐户，可以在托管域中手动创建该帐户。

无论创建方式如何，所有用户都会通过 Azure AD DS 中的默认密码策略应用以下帐户锁定策略：

* **帐户锁定持续时间：** 30
* **允许的登录尝试失败次数：** 5
* **多长时间后重置登录尝试失败计数：** 30 分钟
* **最长密码期限（生存期）：** 90 天

使用这些默认设置时，如果在 2 分钟内使用了 5 个无效密码，则会将用户帐户锁定 30 分钟。 帐户将在 30 分钟后自动解锁。

帐户锁定仅发生在托管域中。 用户帐户仅在 Azure AD DS 中锁定，并且仅在对托管域进行登录尝试失败的情况下锁定。 已从 Azure AD 或本地同步的用户帐户不会在其源目录中锁定，只会在 Azure AD DS 中锁定。

如果 Azure AD 密码策略指定的最长密码期限超过 90 天，则该密码期限会应用于 Azure AD DS 中的默认策略。 你可以配置自定义密码策略，以在 Azure AD DS 中定义不同的最长密码期限。 如果你在 Azure AD DS 密码策略中配置的密码最长期限比在 Azure AD 或本地 AD DS 环境中配置的时间更短，则需要小心。 在这种情况下，系统在 Azure AD 或本地 AD DS 环境中提示用户更改密码之前，该密码可能已在 Azure AD DS 中过期。

对于在托管域中手动创建的用户帐户，还会通过默认策略应用下述的其他密码设置。 这些设置不适用于从 Azure AD 同步进来的用户帐户，因为用户无法直接在 Azure AD DS 中更新其密码。

* **最短密码长度（字符数）：** 7
* **密码必须符合复杂性要求**

你无法修改默认密码策略中的帐户锁定或密码设置， 只能让“AAD DC 管理员”组的成员创建自定义密码策略，并将其配置为替代（优先于）默认的内置策略，如下一部分所示。

## <a name="create-a-custom-password-policy"></a>创建自定义密码策略

在 Azure 中生成并运行应用程序时，需要配置自定义密码策略。 例如，你可以创建一个策略来设置不同的帐户锁定策略设置。

自定义密码策略应用于托管域中的组。 此配置可有效地替代默认策略。

若要创建自定义密码策略，请使用已加入域的 VM 中的 Active Directory 管理工具。 使用 Active Directory 管理中心，你可以在托管域中查看、编辑和创建资源，包括 OU。

> [!NOTE]
> 若要在托管域中创建自定义密码策略，必须登录到 AAD DC 管理员组成员的用户帐户。

1. 在“开始”屏幕中选择“管理工具”。 其中显示了可用管理工具的列表，这些工具是在[创建管理 VM][tutorial-create-management-vm] 的教程中安装的。
1. 若要创建和管理 OU，请从管理工具列表中选择“Active Directory 管理中心”。
1. 在左窗格中选择你的托管域，例如 aaddscontoso.com。
1. 打开“系统”容器，然后打开“密码设置”容器。

    此时会显示托管域的内置密码策略。 你无法修改此内置策略， 只能创建一个自定义密码策略来替代默认策略。

    ![在 Active Directory 管理中心内创建密码策略](./media/password-policy/create-password-policy-adac.png)

1. 在右侧的“任务”面板中，选择“新建”>“密码设置”。 
1. 在“创建密码设置”对话框中，输入策略的名称，例如 MyCustomFGPP。
1. 存在多个密码策略时，优先级最高的策略将应用于用户。 编号越低，优先级越高。 默认密码策略的优先级为 200。

    设置自定义密码策略的优先级（例如 1）来替代默认值。

1. 根据需要编辑其他密码策略设置。 请记住以下要点：

    * 密码复杂性、期限或过期时间等设置仅适用于在托管域中手动创建的用户。
    * 帐户锁定设置适用于所有用户，但仅在托管域中生效，而不会在 Azure AD 本身中生效。

    ![创建自定义的细化密码策略](./media/password-policy/custom-fgpp.png)

1. 取消选中“防止意外删除”。 如果选中此选项，则不能保存 FGPP。
1. 在“直接应用到”部分中，选择“添加”按钮。  在“选择用户或组”对话框中，选择“位置”按钮。 

    ![选择要对其应用密码策略的用户和组](./media/password-policy/fgpp-applies-to.png)

1. 密码策略只能应用于组。 在“位置”对话框中展开域名（例如 aaddscontoso.com），然后选择一个 OU（例如“AADDC 用户”）。 如果你有一个自定义 OU，其中包含要应用的用户组，请选择该 OU。

    ![选择该组所属的 OU](./media/password-policy/fgpp-container.png)

1. 键入要向其应用策略的组的名称，然后选择“检查名称”以验证该组是否存在。

    ![搜索并选择要对其应用 FGPP 的组](./media/password-policy/fgpp-apply-group.png)

1. 在“直接应用到”部分中显示所选组名称后，即可选择“确定”以保存自定义密码策略。 

## <a name="next-steps"></a>后续步骤

若要详细了解密码策略以及如何使用 Active Directory 管理中心，请参阅以下文章：

* [了解细化的密码策略](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 管理中心配置细化的密码策略](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
