---
title: 教程 - 在 Azure AD 域服务中创建副本集 | Microsoft Docs
description: 了解如何在 Azure 门户中创建和使用副本集以实现 Azure AD 域服务的服务复原
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: iainfou
ms.openlocfilehash: 6f166cdcb5f3764d7b264fdb4ebc082ece4c798b
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245088"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services-preview"></a>教程：在 Azure Active Directory 域服务（预览版）中创建和使用针对复原能力或地理位置的副本集

若要提高 Azure Active Directory 域服务 (Azure AD DS) 托管域的复原能力，或部署到靠近应用程序的其他地理位置，可以使用副本集。 每个 Azure AD DS 托管域命名空间（如 aaddscontoso.com）都包含一个初始副本集。 在其他 Azure 区域中创建附加副本集的功能可为托管域提供地理复原能力。

可以将副本集添加到支持 Azure AD DS 的任何 Azure 区域中的任何对等互连虚拟网络。

副本集是 Azure AD 域服务中的公共预览功能。 请注意对于仍处于预览版的功能所存在的支持差异。 有关预览版的详细信息，请参阅 [Azure Active Directory 预览版 SLA](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 了解虚拟网络要求
> * 创建副本集
> * 删除副本集

如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 使用 Azure 资源管理器部署模型创建的并在 Azure AD 租户中配置的 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务托管域][tutorial-create-instance]。

    > [!IMPORTANT]
    > 使用经典部署模型创建的托管域无法使用副本集。 此外，至少需要为托管域使用“企业版”SKU。 如果需要，请[更改托管域的 SKU][howto-change-sku]。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在本教程中，使用 Azure 门户创建和管理副本集。 若要开始操作，请登录到 [Azure 门户](https://portal.azure.com)。

## <a name="networking-considerations"></a>网络注意事项

托管副本集的虚拟网络必须能够相互通信。 依赖于 Azure AD DS 的应用程序和服务也需要与托管副本集的虚拟网络建立网络连接。 应在所有虚拟网络之间配置 Azure 虚拟网络对等互连，以创建完全的网状网络。 这些对等互连可在副本集之间实现有效的站点内复制。

在 Azure AD DS 中使用副本集之前，请查看以下 Azure 虚拟网络要求：

* 避免 IP 地址空间重叠，以便可实现虚拟网络对等互连和路由。
* 使用足够的 IP 地址创建子网，以支持你的方案。
* 确保 Azure AD DS 具有自己的子网。 不要与应用程序 VM 和服务共享此虚拟网络子网。
* 对等互连的虚拟网络不是中转性的。

> [!TIP]
> 当你在 Azure 门户中创建副本集时，系统会为你创建虚拟网络之间的网络对等互连。
>
> 如果需要，在 Azure 门户中添加副本集时可以创建虚拟网络和子网。 或者，可以为副本集选择目标区域中的现有虚拟网络资源，并让系统自动创建对等互连（如果它们尚不存在）。

## <a name="create-a-replica-set"></a>创建副本集

创建托管域（如 aaddscontoso.com）时，将创建初始副本集。 其他副本集共享相同的命名空间和配置。 对 Azure AD DS 进行的更改（包括配置、用户标识和凭据、组、组策略对象、计算机对象以及其他更改）会应用于使用 AD DS 复制的托管域中的所有副本集。

在本教程中，在 Azure 区域中创建与初始 Azure AD DS 副本集不同的附加副本集。

若要创建附加副本集，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“Azure AD 域服务”。
1. 选择你的托管域，例如 *aaddscontoso.com*。
1. 在左侧，选择“副本集(预览版)”。 每个托管域在所选区域中都包含一个初始副本集，如以下示例屏幕截图所示：

    ![在 Azure 门户中查看和添加副本集的示例屏幕截图](./media/tutorial-create-replica-set/replica-set-list.png)

    若要创建附加副本集，请选择“+ 添加”。

1. 在“添加副本集”窗口中，选择目标区域，如“美国东部”。

    选择目标区域中的虚拟网络（例如 vnet-eastus），然后选择子网（如 aadds-subnet）。 如果需要，请选择“新建”以在目标区域中添加虚拟网络，然后选择“管理”以创建 Azure AD DS 的子网。

    如果它们尚不存在，则会在现有托管域的虚拟网络与目标虚拟网络之间自动创建 Azure 虚拟网络对等互连。

    以下示例屏幕截图显示在“美国东部”创建新副本集的过程：

    ![在 Azure 门户中创建副本集的示例屏幕截图](./media/tutorial-create-replica-set/create-replica-set.png)

1. 准备就绪后，选择“保存”。

创建副本集的过程需要一段时间，因为会在目标区域中创建资源。 随后使用 AD DS 复制来复制托管域本身。

在部署继续进行时，副本集会报告为“正在预配”，如以下示例屏幕截图所示。 完成后，副本集显示为“正在运行”。

![Azure 门户中副本集部署状态的示例屏幕截图](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>删除副本集

托管域当前限制为四个副本：初始副本集以及三个附加副本集。 如果不再需要副本集，或者要在其他区域中创建副本集，则可以删除不需要的副本集。

> [!IMPORTANT]
> 无法删除托管域中的最后一个副本集。

若要删除副本集，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“Azure AD 域服务”。
1. 选择你的托管域，例如 *aaddscontoso.com*。
1. 在左侧，选择“副本集(预览版)”。 从副本集列表中，选择要删除的副本集旁的“…”上下文菜单。
1. 从上下文菜单中选择“删除”，然后确认要删除的副本集。

> [!NOTE]
> 副本集删除可能是一项耗时的操作。

如果不再需要副本集使用的虚拟网络或对等互连，也可以删除这些资源。 确保其他区域中的其他应用程序资源不需要这些网络连接，然后再删除这些连接。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 配置虚拟网络对等互连
> * 在不同的地理区域中创建副本集
> * 删除副本集

有关更多概念信息，请了解副本集在 Azure AD DS 中的工作原理。

> [!div class="nextstepaction"]
> [副本集概念和功能][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
