---
title: 在 Azure AD 域服务中创建和使用密码策略 |Microsoft Docs
description: 了解如何以及为什么使用细化密码策略来保护和控制 Azure AD DS 托管域中的帐户密码。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: 3876c6f80e9f18059ab4abac67732cdbf2ca24fa
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248296"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>托管域中的密码和帐户锁定策略

若要管理 Azure Active Directory 域服务（Azure AD DS）中的用户安全，可以定义严格的密码策略来控制帐户锁定设置或最小密码长度和复杂性。 将创建默认的细化密码策略，并将其应用到 Azure AD DS 托管域中的所有用户。 为了提供具体的控制并满足特定的业务或符合性需求，可以创建更多的策略并将其应用于特定的用户组。

本文说明如何使用 Active Directory 管理中心在 Azure AD DS 中创建和配置细化密码策略。

## <a name="before-you-begin"></a>开始之前

若要完成本文，需要具备以下资源和权限：

* 一个有效的 Azure 订阅。
  * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
  * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
  * 如果需要，请完成[创建和配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]的教程。
* 已加入到 Azure AD DS 托管域的 Windows Server 管理 VM。
  * 如果需要，请完成[创建管理 VM][tutorial-create-management-vm]教程。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。

## <a name="default-password-policy-settings"></a>默认密码策略设置

细化密码策略（Fgpp）允许您将对密码和帐户锁定策略的特定限制应用于域中的不同用户。 例如，若要保护特权帐户，你可以应用更严格的帐户锁定设置，而不是常规的非特权帐户。 可以在 Azure AD DS 托管域中创建多个 Fgpp，并指定优先级顺序以应用于用户。

策略通过 Azure AD DS 托管域中的组关联进行分发，你所做的任何更改将在下次用户登录时应用。 更改策略不会解除对已锁定的用户帐户的锁定。

密码策略的行为方式稍有不同，具体取决于创建用户帐户的方式。 可以通过两种方式在 Azure AD DS 中创建用户帐户：

* 可以从 Azure AD 同步用户帐户。 这包括直接在 Azure 中创建的仅限云的用户帐户，以及使用 Azure AD Connect 从本地 AD DS 环境同步的混合用户帐户。
    * Azure AD DS 中的大部分用户帐户是通过 Azure AD 中的同步过程创建的。
* 可以在 Azure AD DS 托管域中手动创建用户帐户，并且 Azure AD 中不存在该帐户。

无论创建方式如何，所有用户都会在 Azure AD DS 中通过默认密码策略应用以下帐户锁定策略：

* **帐户锁定持续时间：** 30
* **允许登录尝试失败的次数：** 5
* **重置失败的登录尝试次数：** 30 分钟
* **最长密码期限（生存期）：** 90 天

使用这些默认设置，如果2分钟内使用5个无效密码，用户帐户将被锁定30分钟。 帐户将在 30 分钟后自动解锁。

帐户锁定仅出现在托管域中。 仅在 Azure AD DS 中锁定用户帐户，并且仅由于对托管域进行的登录尝试失败。 从 Azure AD 或本地同步的用户帐户不会在其源目录中被锁定，只在 Azure AD DS 中被锁定。

如果你的 Azure AD 密码策略指定超过90天的最长密码期限，则密码期限将应用到 Azure AD DS 中的默认策略。 你可以配置自定义密码策略，以在 Azure AD DS 中定义不同的最长密码期限。 请注意，在 Azure AD DS 密码策略中配置的密码最长期限比 Azure AD 或本地 AD DS 环境中的时间更短。 在这种情况下，在本地 AD DS 环境的 Azure AD 中，用户的密码可能会在 Azure AD DS 后过期。

对于在 Azure AD DS 托管域中手动创建的用户帐户，还会通过默认策略应用以下其他密码设置。 这些设置不适用于从 Azure AD 同步的用户帐户，因为用户无法直接在 Azure AD DS 中更新其密码。

* **最短密码长度（字符）：** 7
* **密码必须满足复杂性要求**

你无法修改默认密码策略中的帐户锁定或密码设置。 相反， *AAD DC Administrators*组的成员可以创建自定义密码策略，并将其配置为替代（优先于）默认内置策略，如下一节所示。

## <a name="create-a-custom-password-policy"></a>创建自定义密码策略

在 Azure 中生成和运行应用程序时，可能需要配置自定义密码策略。 例如，你可以创建一个策略来设置不同的帐户锁定策略设置。

自定义密码策略将应用于 Azure AD DS 托管域中的组。 此配置有效地覆盖默认策略。

若要创建自定义密码策略，请使用已加入域的 VM 中的 Active Directory 管理工具。 使用 Active Directory 管理中心可以查看、编辑和创建 Azure AD DS 托管域中的资源，包括 Ou。

> [!NOTE]
> 若要在 Azure AD DS 托管域中创建自定义密码策略，你必须登录到作为*AAD DC 管理员*组成员的用户帐户。

1. 从 "开始" 屏幕中，选择 "**管理工具**"。 其中显示了在[创建管理 VM][tutorial-create-management-vm]教程中安装的可用管理工具列表。
1. 若要创建和管理 Ou，请从管理工具列表中选择 " **Active Directory 管理中心**"。
1. 在左窗格中，选择 Azure AD DS 托管域，如*contoso.com*。
1. 打开 "**系统**" 容器，然后**密码设置容器**"。

    将显示 Azure AD DS 托管域的内置密码策略。 你无法修改此内置策略。 而是创建一个自定义密码策略来替代默认策略。

    ![在 Active Directory 管理中心中创建密码策略](./media/password-policy/create-password-policy-adac.png)

1. 在右侧的 "**任务**" 面板中，选择 "**新建 > 密码设置**"。
1. 在 "**创建密码设置**" 对话框中，输入策略的名称，例如 " *MyCustomFGPP*"。
1. 存在多个密码策略时，具有最高优先级的策略（或优先级）将应用于用户。 编号越低，优先级越高。 默认密码策略的优先级为*200*。

    将自定义密码策略的优先级设置为替代默认值（例如*1*）。

1. 根据需要编辑其他密码策略设置。 请记住以下要点：

    * 仅限在 Azure AD DS 托管域中手动创建的用户的设置（例如，密码复杂性、年龄或过期时间）。
    * 帐户锁定设置适用于所有用户，但仅在托管域中生效。

    ![创建自定义细化密码策略](./media/how-to/custom-fgpp.png)

1. 取消选中 "**防止意外删除**"。 如果选择此选项，则不能保存 FGPP。
1. 在 "**直接应用**于" 部分中，选择 "**添加**" 按钮。 在“选择用户或组”对话框中，单击“位置”按钮。

    ![选择要对其应用密码策略的用户和组](./media/how-to/fgpp-applies-to.png)

1. 密码策略只能应用于组。 在 "**位置**" 对话框中，展开域名（如*contoso.com*），然后选择 " **AADDC 用户**" 之类的 OU。 如果你有一个自定义 OU，其中包含要应用的用户组，请选择该 OU。

    ![选择该组所属的 OU](./media/how-to/fgpp-container.png)

1. 键入要向其应用策略的组的名称，然后选择 "**检查名称**" 以验证该组是否存在。

    ![搜索并选择要应用的组 FGPP](./media/how-to/fgpp-apply-group.png)

1. 将所选组的名称显示在 "**直接应用到**" 部分后，选择 **"确定"** 以保存自定义密码策略。

## <a name="next-steps"></a>后续步骤

有关密码策略和使用 Active Directory 管理中心的详细信息，请参阅以下文章：

* [了解细化密码策略](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 管理中心配置细化密码策略](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
