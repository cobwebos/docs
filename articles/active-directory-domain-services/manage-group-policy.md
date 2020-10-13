---
title: 在 Azure AD 域服务中创建和管理组策略 | Microsoft Docs
description: 了解如何在 Azure Active Directory 域服务托管域中编辑内置的组策略对象 (GPO) 并创建自己的自定义策略。
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: deefcb66e05199896e8997d707a06e45f397adec
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963731"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>管理 Azure Active Directory 域服务托管域中的组策略

Azure Active Directory 域服务 (Azure AD DS) 中的用户和计算机对象的设置通常使用组策略对象 (GPO) 来管理。 Azure AD DS 包括 AADDC 用户和 AADDC 计算机容器的内置 GPO 。 可以自定义这些内置 GPO，以根据环境的需要配置组策略。 Azure AD DC 管理员组的成员在 Azure AD DS 域中具有组策略管理特权，还可以创建自定义 GPO 和组织单位 (OU)。 有关组策略的定义及其工作原理的详细信息，请参阅[组策略概述][group-policy-overview]。

在混合环境中，本地 AD DS 环境中配置的组策略不会同步到 Azure AD DS。 若要为 Azure AD DS 中的用户或计算机定义配置设置，请编辑其中一个默认 GPO 或创建一个自定义 GPO。

本文介绍如何安装组策略管理工具，然后编辑内置 GPO 并创建自定义 GPO。

## <a name="before-you-begin"></a>准备阶段

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]的教程。
* 已加入 Azure AD DS 托管域的 Windows Server 管理 VM。
    * 如果需要，请完成[创建 Windows Server VM 并将其加入到托管域][create-join-windows-vm]的教程。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。

> [!NOTE]
> 可以通过将新模板复制到管理工作站，来使用组策略管理模板。 将 .admx 文件复制到 `%SYSTEMROOT%\PolicyDefinitions` 并将特定于区域设置的 .adml 文件复制到 `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`，其中 `Language-CountryRegion` 与 .adml 文件的语言和区域相匹配  。
>
> 例如，将 .adml 文件的美国英语版本复制到 `\en-us` 文件夹中。
>
> 或者，你可以在托管域中的域控制器上集中存储组策略管理模板。 有关详细信息，请参阅[如何为 Windows 中的组策略管理模板创建和管理中心存储](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)。

## <a name="install-group-policy-management-tools"></a>安装组策略管理工具

若要创建和配置组策略对象 (GPO)，需要安装组策略管理工具。 这些工具可以作为 Windows Server 中的一项功能进行安装。 有关如何在 Windows 客户端上安装管理工具的详细信息，请参阅安装[远程服务器管理工具 (RSAT)][install-rsat]。

1. 登录到管理 VM。 有关如何使用 Azure 门户进行连接的步骤，请参阅[连接到 Windows Server VM][connect-windows-server-vm]。
1. 登录到 VM 时，系统默认会打开“服务器管理器”。 如果未打开，请在“开始”菜单中选择“服务器管理器” 。
1. 在“服务器管理器”窗口的“仪表板”窗格中，选择“添加角色和功能”。
1. 在“添加角色和功能向导”的“准备工作”页上，选择“下一步”。
1. 对于“安装类型”，请保留选中“基于角色或基于功能的安装”选项，然后选择“下一步”。
1. 在“服务器选择”页上，从服务器池中选择当前的 VM（例如 *myvm.aaddscontoso.com*），然后选择“下一步”。
1. 在“服务器角色”页上，单击“下一步”。
1. 在“功能”页上，选择“组策略管理”功能。 

    ![从“功能”页安装“组策略管理”](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. 在“确认”页上选择“安装”。  安装组策略管理工具可能需要一两分钟时间。
1. 功能安装完成后，选择“关闭”退出“添加角色和功能”向导。 

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>打开组策略管理控制台并编辑对象

托管域中的用户和计算机有默认的组策略对象 (GPO)。 在上一部分安装了组策略管理功能后，让我们来查看和编辑现有 GPO。 在下一部分，你将创建自定义 GPO。

> [!NOTE]
> 若要管理托管域中的组策略，必须登录到 AAD DC 管理员组成员的用户帐户。

1. 在“开始”屏幕中选择“管理工具”。 显示了可用的管理工具列表，其中包括上一节中安装的“组策略管理”。
1. 若要打开组策略管理控制台 (GPMC)，请选择“组策略管理”。

    ![组策略管理控制台打开可以编辑的组策略对象](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

托管域中有两种内置组策略对象 (GPO)：一种用于 AADDC 计算机容器，另一种用于 AADDC 用户容器 。 可以自定义这些 GPO，以在托管域中根据需要配置组策略。

1. 在“组策略管理”控制台中，展开“林: aaddscontoso.com”节点 。 接下来，展开“域”节点。

    有两个适用于 AADDC 计算机和 AADDC 用户的内置容器 。 其中每个容器都应用了一个默认 GPO。

    ![应用于默认“AADDC 计算机”和“AADDC 用户”容器的内置 GPO](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. 可以自定义这些内置 GPO，以在托管域上配置特定组策略。 右键选择其中一个 GPO，如 AADDC 计算机 GPO，然后选择“编辑...”。

    ![选择选项以“编辑”其中一个内置 GPO](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. 将打开组策略管理编辑器工具，以自定义 GPO，如帐户策略：

    ![组策略管理编辑器的屏幕截图。](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    完成后，选择“文件”>“保存”以保存策略。 默认情况下，计算机每 90 分钟刷新一次组策略，并应用更改。

## <a name="create-a-custom-group-policy-object"></a>创建自定义组策略对象

若要对类似的策略设置进行分组，通常会创建其他 GPO，而不是在单个默认 GPO 中应用所有必需的设置。 使用 Azure AD DS，可以创建或导入自己的自定义组策略对象，并将其链接到自定义 OU。 如果需要首先创建自定义 OU，请参阅[在托管域中创建自定义 OU](create-ou.md)。

1. 在“组策略管理”控制台中，选择自定义组织单位 (OU)，如 MyCustomOU。 右键选择 OU，然后选择“在此域中创建 GPO 并在此处链接...”：

    ![在组策略管理控制台中创建自定义 GPO](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. 为新 GPO 指定名称，例如自定义 GPO，然后选择“确定”。 可以选择将此自定义 GPO 基于现有 GPO 和策略选项集。

    ![为新的自定义 GPO 指定名称](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. 将创建自定义 GPO 并将其链接到自定义 OU。 现在若要配置策略设置，请右键选择自定义 GPO，然后选择“编辑...”：

    ![选择选项以“编辑”自定义 GPO](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. 将打开组策略管理编辑器，以自定义 GPO：

    ![自定义 GPO 以根据需要配置设置](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    完成后，选择“文件”>“保存”以保存策略。 默认情况下，计算机每 90 分钟刷新一次组策略，并应用更改。

## <a name="next-steps"></a>后续步骤

有关可以使用组策略管理控制台配置的可用组策略设置的详细信息，请参阅[使用组策略首选项][group-policy-console]。

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
