---
title: 在 Azure AD 域服务中创建和管理组策略 |Microsoft Docs
description: 了解如何在 Azure Active Directory 域服务托管域中编辑内置的组策略对象 (Gpo) 并创建自己的自定义策略。
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: iainfou
ms.openlocfilehash: 5c6d7b3403209710c9086b90abcb0e2ce61a0e8a
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612695"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>管理 Azure AD 域服务托管域中的组策略

Azure Active Directory 域服务 (Azure AD DS) 中的用户和计算机对象的设置通常是使用组策略对象 (Gpo) 来管理的。 Azure AD DS 包括*AADDC Users*和*AADDC 计算机*容器的内置 gpo。 你可以根据环境的需要自定义这些内置 Gpo 来配置组策略。 *AZURE AD DC administrators*组的成员具有 Azure AD DS 域中组策略的管理权限, 还可以创建自定义 gpo 和组织单位 (ou)。 有关组策略的定义及其工作原理的详细信息, 请参阅[组策略概述][group-policy-overview]。

本文介绍如何安装组策略管理工具, 然后编辑内置 Gpo 并创建自定义 Gpo。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>开始之前

若要完成本文, 需要具备以下资源和权限:

* 一个有效的 Azure 订阅。
    * 如果没有 Azure 订阅, 请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与你的订阅关联的 Azure Active Directory 租户, 可与本地目录或仅限云的目录同步。
    * 如果需要, 请[创建 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅与你的帐户相关联][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要, 请完成[创建和配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]的教程。
* 已加入到 Azure AD DS 托管域的 Windows Server 管理 VM。
    * 如果需要, 请完成[创建 Windows SERVER VM 并将其加入托管域][create-join-windows-vm]的教程。
* 作为 Azure AD 租户中*AZURE AD DC administrators*组的成员的用户帐户。

## <a name="install-group-policy-management-tools"></a>安装组策略管理工具

若要创建和配置组策略对象 (Gpo), 需要安装组策略管理工具。 这些工具可以作为一项功能安装在 Windows Server 中。 有关如何在 Windows 客户端上安装管理工具的详细信息, 请参阅 install[远程服务器管理工具 (RSAT)][install-rsat]。

1. 登录到管理 VM。 有关如何使用 Azure 门户进行连接的步骤, 请参阅[连接到 Windows SERVER VM][connect-windows-server-vm]。
1. 登录到 VM 时, 默认情况下应打开**服务器管理器**。 否则, 请在 "**开始**" 菜单中选择 "**服务器管理器**"。
1. 在 "**服务器管理器**" 窗口的 "*仪表板*" 窗格中, 选择 "**添加角色和功能**"。
1. 在 "*添加角色和功能向导*" 的 "**开始之前**" 页上, 选择 "**下一步**"。
1. 对于*安装类型*, 请选中 "**基于角色或基于功能的安装**" 选项, 并选择 "**下一步**"。
1. 在 "**服务器选择**" 页上, 从服务器池中选择当前 VM (如*myvm.contoso.com*), 然后选择 "**下一步**"。
1. 在“服务器角色”页上，单击“下一步”。
1. 在“功能”页上，选择“组策略管理”功能。

    ![从功能页安装 "组策略管理"](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. 在 "**确认**" 页上, 选择 "**安装**"。 可能需要一到两分钟的时间来安装组策略管理工具。
1. 功能安装完成后, 选择 "**关闭**" 以退出 "**添加角色和功能**向导"。

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>打开组策略管理控制台并编辑对象

Azure AD DS 托管域中的用户和计算机存在默认组策略对象 (Gpo)。 在上一部分中安装组策略管理功能后, 让我们来查看和编辑现有 GPO。 在下一部分中, 将创建一个自定义 GPO。

> [!NOTE]
> 若要管理 Azure AD DS 托管域中的组策略, 你必须登录到作为*AAD DC 管理员*组成员的用户帐户。

1. 从 "开始" 屏幕中, 选择 "**管理工具**"。 其中显示了可用管理工具列表, 其中包括上一节中安装**组策略管理**。
1. 若要打开组策略管理控制台 (GPMC), 请选择 "**组策略管理**"。

    ![组策略管理控制台打开可以编辑组策略对象](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Azure AD DS 托管域中有两个内置组策略对象 (Gpo)-一个用于*AADDC 计算机*容器, 另一个用于*AADDC 用户*容器。 你可以根据需要自定义这些 Gpo, 以便在 Azure AD DS 托管域中配置组策略。

1. 在**组策略管理**控制台中, 展开 "**林: contoso.com** " 节点。 接下来, 展开 "**域**" 节点。

    *AADDC 计算机*和*AADDC 用户*有两个内置容器。 其中每个容器都应用了默认 GPO。

    ![应用于默认 "AADDC 计算机" 和 "AADDC 用户" 容器的内置 Gpo](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. 可以自定义这些内置 Gpo, 以便在 Azure AD DS 托管域上配置特定组策略。 右键选择其中一个 Gpo, 如 " *AADDC 计算机" GPO*, 然后选择 "**编辑 ...** "。

    ![选择 "编辑" 其中一个内置 Gpo 的选项](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. 将打开组策略管理编辑器工具, 以便自定义 GPO, 如*帐户策略*:

    ![自定义 GPO 以根据需要配置设置](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    完成后, 选择 "**文件" > "保存**" 以保存策略。 默认情况下, 计算机会每隔90分钟刷新组策略, 并应用所做的更改。

## <a name="create-a-custom-group-policy-object"></a>创建自定义组策略对象

若要对类似的策略设置进行分组, 你通常会创建其他 Gpo, 而不是在单个默认 GPO 中应用所有必需的设置。 使用 Azure AD DS, 您可以创建或导入您自己的自定义组策略对象, 并将其链接到自定义 OU。 如果需要首先创建自定义 OU, 请参阅[在 AZURE AD DS 托管域中创建自定义 ou](create-ou.md)。

1. 在**组策略管理**控制台中, 选择自定义组织单位 (OU), 如*MyCustomOU*。 右键选择 OU, 然后选择 "**在此域中创建 GPO 并在此处链接 ..."** :

    ![在组策略管理控制台中创建自定义 GPO](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. 指定新 GPO 的名称, 如 *"我的自定义 gpo*", 然后选择 **"确定"** 。 你可以根据需要将此自定义 GPO 基于现有 GPO 和策略选项集。

    ![指定新自定义 GPO 的名称](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. 将创建自定义 GPO 并将其链接到自定义 OU。 若要现在配置策略设置, 请右键选择自定义 GPO, 然后选择 "**编辑 ...** ":

    ![选择 "编辑" 自定义 GPO 的选项](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. 此时将打开**组策略管理编辑器**以便自定义 GPO:

    ![自定义 GPO 以根据需要配置设置](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    完成后, 选择 "**文件" > "保存**" 以保存策略。 默认情况下, 计算机会每隔90分钟刷新组策略, 并应用所做的更改。

## <a name="next-steps"></a>后续步骤

有关可以使用组策略管理控制台配置的可用组策略设置的详细信息, 请参阅[使用组策略首选项][group-policy-console]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
