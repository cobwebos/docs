---
title: 加速了 Azure 实验室服务的实验室帐户设置指南
description: 本指南可帮助管理员快速设置实验室帐户，以便在学校内使用。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c186560b27ebcb543a23785dc5fbc556614f64b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445417"
---
# <a name="lab-account-setup-guide"></a>实验室帐户设置指南

作为第一步，管理员应在 Azure 订阅中设置实验室帐户。 实验室帐户是教室实验室的容器，只需几分钟即可完成设置。

## <a name="understand-your-schools-lab-account-requirements"></a>了解学校的实验室帐户要求

若要了解如何根据你的学校需求配置实验室帐户，你应考虑这些问题。

### <a name="do-i-have-access-to-an-azure-subscription"></a>我是否有权访问 Azure 订阅？

若要创建实验室帐户，你需要访问为你的学校配置的 Azure 订阅。 你的学校可能有一个或多个订阅。 使用订阅来管理所有 Azure 资源和服务（包括实验室帐户）的计费和安全性。

### <a name="how-many-lab-accounts-need-to-be-created"></a>需要创建多少个实验室帐户？

若要快速开始，请创建一个实验室帐户，然后根据需要创建其他实验室帐户。 例如，可能最终每个部门有一个实验室帐户。

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>谁应该是实验室帐户的所有者和参与者？

管理员通常是实验室帐户的所有者和参与者。 它们负责管理适用于实验室帐户中包含的所有实验室的策略。 创建实验室帐户的人员将自动成为所有者。 你可以添加其他所有者和参与者，通常来自与你的订阅关联的 Azure Active Directory （Azure AD）租户。 这对于通过在实验室帐户级别分配 "所有者" 或 "参与者" 角色来帮助管理实验室帐户很有用。

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>允许谁创建和管理实验室？

您可以选择让您的管理员和教职员成员创建和管理实验室。 这些用户（通常来自与你的订阅关联的 Azure AD 租户）被分配到实验室帐户中的实验室创建者角色。

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>是否要为实验室创建者保存可跨实验室共享的映像的能力？

共享映像库是一个存储库，可用于保存和共享图像。 如果有多个需要相同映像的类，则实验室创建者可以创建映像一次，并跨实验室共享。 但是，若要开始使用，你不一定需要共享的映像库，因为你随时可以添加一个。

如果对此问题回答了 "是"，则需要创建或将共享映像库附加到实验室帐户。 如果你回答 "我不知道"，则可以将此决定推迟到之后。

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>你的教室实验室将使用 Azure Marketplace 中的哪些映像？

Azure Marketplace 提供了数百个可启用的映像，使实验室创建者可以使用该映像创建其实验室。 某些映像可能包含实验室已经需要的所有内容。 在其他情况下，你可以使用映像作为起点，然后，实验室创建者可以通过安装其他应用程序或工具对其进行自定义。

如果你不知道需要使用哪些映像，则可以随时返回到此以启用它们。 此外，查看可用映像的最佳方式是先创建实验室帐户。 这会提供访问权限，以便查看可用映像及其内容的列表。
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>实验室的虚拟机是否需要有权访问其他 Azure 或本地资源？

设置实验室帐户时，还可以选择与虚拟网络对等。 若要确定是否需要此操作，请考虑以下问题：

- **是否需要提供对授权服务器的访问权限？**
  
   如果计划使用 Azure Marketplace 映像，则会将操作系统许可证的成本捆绑到实验室服务的定价中。 因此，不需要为操作系统本身提供许可证。 但是，对于安装的其他软件和应用程序，你需要提供相应的许可证。

- **实验室 Vm 是否需要访问其他本地资源，如文件共享或数据库？**

   你创建一个虚拟网络来提供对本地资源的访问，通常通过使用站点到站点虚拟网络网关。 如果尚未配置虚拟网络，则需要花费额外的时间。

- **实验室 Vm 是否需要访问位于虚拟网络中的其他 Azure 资源？**

   如果需要访问*未*在虚拟网络中保护的 Azure 资源，则可以通过公共 internet 访问这些资源，而无需进行任何对等互连。

如果对一个或多个问题回答 "是"，则需要将实验室帐户对等互连到虚拟网络。 如果你回答 "我不知道"，则可以将此决定推迟到之后。 创建实验室帐户后，你始终可以选择对虚拟网络进行对等互连。

## <a name="set-up-your-lab-account"></a>设置实验室帐户

了解实验室帐户的要求后，就可以对其进行设置了。

1. **创建实验室帐户。** 有关说明，请参阅[创建实验室帐户](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account)教程。

   创建实验室帐户时，您可能会发现，熟悉所涉及的 Azure 资源会很有帮助。 有关详细信息，请参阅以下文章：

   - [订阅](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [资源组](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [实验室帐户](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [课堂实验室](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [选择区域和位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [资源的命名指南](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **将用户添加到实验室创建者角色。** 有关说明，请参阅[将用户添加到 lab creator 角色](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)。

   此外，有关可以分配给将管理实验室帐户和实验室的用户的不同角色的详细信息，请参阅[管理标识指南](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)。

3. **连接到对等虚拟网络。** 有关说明，请参阅[将实验室网络与对等虚拟网络连接](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)。

   你可能还需要参阅[配置实验室 vm 地址范围的](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)说明。

4. **启用和查看映像。** 有关说明，请参阅[启用实验室创建者的 Azure Marketplace 映像](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

   若要查看每个 Azure Marketplace 映像的内容，请选择映像名称。 例如，以下屏幕截图显示了 Ubuntu Data Science VM 映像的详细信息：

   ![查看 Azure Marketplace 映像的屏幕截图](./media/setup-guide/review-marketplace-images.png)

   如果你有附加到实验室帐户的共享映像库，并且想要启用实验室创建者共享的自定义映像，请完成与以下屏幕截图中所示类似的步骤：

   ![在共享映像库中启用自定义映像的屏幕截图](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>后续步骤

- [管理实验室帐户](how-to-manage-lab-accounts.md)

- [课堂实验室设置指南](setup-guide.md)
