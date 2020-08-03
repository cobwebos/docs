---
title: 副本集 Azure AD 域服务的概念 |Microsoft Docs
description: 了解 Azure Active Directory 域服务中包含哪些副本集，以及如何为需要标识服务的应用程序提供冗余。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: iainfou
ms.openlocfilehash: 698009ee8a57ed5d30e01376b4f2c63b0a27ead8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505726"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services-preview"></a>副本集 Azure Active Directory 域服务（预览版）的概念和功能

创建 Azure Active Directory 域服务（Azure AD DS）托管域时，可以定义唯一的命名空间。 此命名空间是域名（如*aaddscontoso.com*），并将两个域控制器（dc）部署到所选的 Azure 区域。 此 Dc 的部署称为副本集。

可以扩展托管域，使每个 Azure AD 租户具有多个副本集。 可以将副本集添加到任何支持 Azure AD DS 的 Azure 区域中的任何对等互连虚拟网络。 不同 Azure 区域中的其他副本集为旧版应用程序提供地理灾难恢复（如果 Azure 区域处于脱机状态）。

副本集目前处于预览阶段。

> [!NOTE]
> 副本集不允许在单个 Azure 租户中部署多个唯一托管域。 每个副本集包含相同的数据。

## <a name="how-replica-sets-work"></a>副本集的工作方式

创建托管域（如*aaddscontoso.com*）时，将创建一个初始副本集。 其他副本集共享相同的命名空间和配置。 对 Azure AD DS 的更改（包括配置、用户标识和凭据、组、组策略对象、计算机对象以及其他更改）将应用到使用 AD DS 复制的托管域中的所有副本集。

在虚拟网络中创建每个副本集。 每个虚拟网络都必须对等互连到托管域的副本集的每个其他虚拟网络。 此配置将创建支持目录复制的网格网络拓扑。 如果每个副本集位于不同的虚拟子网中，则虚拟网络可以支持多个副本集。

所有副本集都放置在同一个 Active Directory 站点中。 因此，所有更改都将使用站点内复制传播以实现快速聚合。

> [!NOTE]
> 不能在副本集之间定义单独的站点并定义复制设置。

下图显示了包含两个副本集的托管域。 第一个副本集通过域命名空间创建。 在此之后创建第二个副本集：

![包含两个副本集的托管域示例示意图](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> 副本集确保配置副本集的区域中的身份验证服务的可用性。 若要使应用程序在发生区域性服务中断时具有地理冗余，则依赖于托管域的应用程序平台还必须位于另一个区域。
>
> 副本集不会提供应用程序正常运行所需的其他服务（如 Azure Vm 或 Azure 应用服务）的复原能力。 其他应用程序组件的可用性设计需要考虑构成应用程序的服务的复原功能。

下面的示例显示了一个具有三个副本集的托管域，以便进一步提供复原并确保身份验证服务的可用性。 在这两个示例中，应用程序工作负载与托管域副本集位于同一区域中：

![包含三个副本集的托管域示例示意图](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>部署注意事项

托管域的默认 SKU 是*企业*SKU，它支持多个副本集。 若要创建其他副本集，如果更改为*标准*SKU，请将[托管域升级](change-sku.md)到*企业版*或*高级版*。

预览期间支持的副本集的最大数目为四，包括创建托管域时创建的第一个副本。

每个副本集的计费基于域配置 SKU。 例如，如果你有一个使用*企业*SKU 的托管域并且有三个副本集，则对于三个副本集的每个副本，你的订阅将按小时计费。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="can-i-use-my-production-managed-domain-with-this-preview"></a>能否在此预览版中使用生产托管域？

副本集是 Azure AD 域服务中的公共预览功能。 您可以使用生产托管域，但请注意仍处于预览功能中的功能的支持差异。 有关预览的详细信息，请[Azure Active Directory 预览版 SLA](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>是否可以在订阅中创建与托管域不同的副本集？

否。 副本集必须与托管域位于同一订阅中。

### <a name="how-many-replica-sets-can-i-create"></a>可以创建多少个副本集？

预览限制为最多四个副本集-托管域的初始副本集，以及三个额外的副本集。

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>如何将用户和组信息同步到我的副本集？

使用网格虚拟网络对等互连将所有副本集彼此连接。 一个副本集接收来自 Azure AD 的用户和组更新。 然后，使用站点内 AD DS 通过对等互连网络进行复制，将这些更改复制到其他副本集。

与本地 AD DS 一样，扩展断开状态可能导致复制中断。 由于对等互连虚拟网络不可传递，因此副本集的设计要求需要完全网状的网络拓扑。

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>在建立副本集后，如何实现在托管域中进行更改？

托管域中的更改会像以前一样工作。 使用[已加入托管域的 RSAT 工具创建和使用管理 VM](tutorial-create-management-vm.md)。 你可以根据需要将任意数量的管理 Vm 加入托管域。

## <a name="next-steps"></a>后续步骤

若要开始使用副本集，请[创建并配置 AZURE AD DS 托管域][tutorial-create-advanced]。 部署后，[创建并使用其他副本集][create-replica-set]。

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md
