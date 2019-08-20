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
ms.date: 08/08/2019
ms.author: iainfou
ms.openlocfilehash: 45fb2daaeaf9ee788207d43d805e070320372ca0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617129"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>托管域中的密码和帐户锁定策略

若要管理 Azure Active Directory 域服务 (Azure AD DS) 中的帐户安全, 可以定义严格的密码策略来控制设置, 如最小密码长度、密码过期时间或密码复杂性。 默认密码策略将应用到 Azure AD DS 托管域中的所有用户。 为了提供具体的控制并满足特定的业务或符合性需求, 可以创建更多的策略并将其应用于特定的用户组。

本文介绍如何使用 Active Directory 管理中心创建和配置细化密码策略。

## <a name="before-you-begin"></a>开始之前

若要完成本文, 需要具备以下资源和权限:

* 一个有效的 Azure 订阅。
  * 如果没有 Azure 订阅, 请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与你的订阅关联的 Azure Active Directory 租户, 可与本地目录或仅限云的目录同步。
  * 如果需要, 请[创建 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅与你的帐户相关联][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
  * 如果需要, 请完成[创建和配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]的教程。
* 已加入到 Azure AD DS 托管域的 Windows Server 管理 VM。
  * 如果需要, 请完成[创建管理 VM][tutorial-create-management-vm]教程。
* 作为 Azure AD 租户中*AZURE AD DC administrators*组的成员的用户帐户。

## <a name="fine-grained-password-policies-fgpp-overview"></a>细化密码策略 (FGPP) 概述

细化密码策略 (Fgpp) 允许您将对密码和帐户锁定策略的特定限制应用于域中的不同用户。 例如, 若要保护特权帐户, 你可以应用比常规非特权帐户更严格的密码设置。 可以创建多个 Fgpp 来指定 Azure AD DS 托管域中的密码策略。

可以使用 FGPP 配置以下密码设置:

* 最短密码长度
* 密码历史记录
* 密码必须符合复杂性要求
* 最短密码期限
* 最长密码期限
* 帐户锁定策略
  * 帐户锁定持续时间
  * 允许的失败登录尝试次数
  * 多长时间后重置失败登录尝试计数

FGPP 仅影响在 Azure AD DS 中创建的用户。 从 Azure AD 同步到 Azure AD DS 托管域的云用户和域用户不受密码策略影响。

策略通过 Azure AD DS 托管域中的组关联进行分发, 你所做的任何更改将在下次用户登录时应用。 更改策略不会解除对已锁定的用户帐户的锁定。

## <a name="default-fine-grained-password-policy-settings"></a>默认细化密码策略设置

在 Azure AD DS 托管域中, 默认情况下会配置以下密码策略并将其应用于所有用户:

* **最短密码长度 (字符):** 7
* **最长密码期限 (生存期):** 90 天
* **密码必须满足复杂性要求**

默认情况下, 将配置以下帐户锁定策略:

* **帐户锁定持续时间:** 30
* **允许登录尝试失败的次数:** 5
* **重置失败的登录尝试次数:** 30 分钟

使用这些默认设置, 如果2分钟内使用5个无效密码, 用户帐户将被锁定30分钟。 帐户将在 30 分钟后自动解锁。

不能修改或删除默认的内置细化密码策略。 相反, *AAD DC Administrators*组的成员可以创建自定义 FGPP 并将其配置为覆盖默认内置 FGPP (优先于), 如下一节所示。

## <a name="create-a-custom-fine-grained-password-policy"></a>创建自定义细化密码策略

在 Azure 中生成和应用程序时, 可能需要配置自定义 FGPP。 需要创建自定义 FGPP 的一些示例包括设置不同的帐户锁定策略, 或配置托管域的默认密码生存期设置。

你可以创建自定义 FGPP 并将其应用于 Azure AD DS 托管域中的特定组。 此配置有效地覆盖默认 FGPP。 你还可以创建自定义的细化密码策略, 并将其应用于在 Azure AD DS 托管域中创建的任何自定义 Ou。

若要创建细化密码策略, 请使用已加入域的 VM 中的 Active Directory 管理工具。 使用 Active Directory 管理中心可以查看、编辑和创建 Azure AD DS 托管域中的资源, 包括 Ou。

> [!NOTE]
> 若要在 Azure AD DS 托管域中创建细化密码策略, 你必须登录到作为*AAD DC 管理员*组成员的用户帐户。

1. 从 "开始" 屏幕中, 选择 "**管理工具**"。 其中显示了在[创建管理 VM][tutorial-create-management-vm]教程中安装的可用管理工具列表。
1. 若要创建和管理 Ou, 请从管理工具列表中选择 " **Active Directory 管理中心**"。
1. 在左窗格中, 选择 Azure AD DS 托管域, 如*contoso.com*。
1. 在右侧的 "**任务**" 面板中, 选择 "**新建 > 密码设置**"。
1. 在 "**创建密码设置**" 对话框中, 输入策略的名称, 例如 " *MyCustomFGPP*"。 将优先顺序设置为适当的, 以覆盖默认的 FGPP (即*200*), 如*1*。

    根据需要编辑其他密码策略设置, 例如**强制密码历史记录**要求用户创建的密码与以前的*24*个密码不同。

    ![创建自定义细化密码策略](./media/how-to/custom-fgpp.png)

1. 取消选中 "**防止意外删除**"。 如果选择此选项, 则不能保存 FGPP。
1. 在 "**直接应用**于" 部分中, 选择 "**添加**" 按钮。 在“选择用户或组”对话框中，单击“位置”按钮。

    ![选择要对其应用密码策略的用户和组](./media/how-to/fgpp-applies-to.png)

1. 细化密码策略仅可应用于组。 在 "**位置**" 对话框中, 展开域名 (如*contoso.com*), 然后选择 " **AADDC 用户**" 之类的 OU。 如果你有一个自定义 OU, 其中包含要应用的用户组, 请选择该 OU。

    ![选择该组所属的 OU](./media/how-to/fgpp-container.png)

1. 键入要向其应用策略的组的名称, 然后选择 "**检查名称**" 以验证该组是否存在。

    ![搜索并选择要应用的组 FGPP](./media/how-to/fgpp-apply-group.png)

1. 将所选组的名称显示在 "**直接应用到**" 部分后, 选择 **"确定"** 以保存自定义密码策略。

## <a name="next-steps"></a>后续步骤

有关细化密码策略和使用 Active Directory 管理中心的详细信息, 请参阅以下文章:

* [了解细化密码策略](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 管理中心配置细化密码策略](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
