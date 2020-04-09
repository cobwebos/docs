---
title: Azure 实验室服务的加速实验室帐户设置指南
description: 本指南可帮助管理员快速设置实验室帐户，供其学校使用。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: 8fcc46487e7f7c2d075639f10a30cae9950ff31b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879575"
---
# <a name="lab-account-setup-guide"></a>实验室帐户设置指南

作为第一步，管理员应在 Azure 订阅中设置实验室帐户。 实验室帐户是教室实验室的容器，只需几分钟即可设置。

## <a name="understand-your-schools-lab-account-requirements"></a>了解您学校的实验室帐户要求

要了解如何根据学校的需求配置实验室帐户，应考虑这些问题。

### <a name="do-i-have-access-to-an-azure-subscription"></a>我有权访问 Azure 订阅吗？

要创建实验室帐户，您需要访问为学校配置的 Azure 订阅。 您的学校可能有一个或多个订阅。 您可以使用订阅来管理所有 Azure 资源和服务的计费和安全，包括实验室帐户。

### <a name="how-many-lab-accounts-need-to-be-created"></a>需要创建多少个实验室帐户？

要快速入门，请创建单个实验室帐户，然后根据需要创建其他实验室帐户。 例如，您可能最终每个部门有一个实验室帐户。

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>谁应该是实验室帐户的所有者和贡献者？

管理员通常是实验室帐户的所有者和参与者。 他们负责管理适用于实验室帐户中包含的所有实验室的策略。 创建实验室帐户的人员将自动成为所有者。 您可以添加其他所有者和参与者，通常来自与订阅关联的 Azure 活动目录 （Azure AD） 租户。 通过在实验室帐户级别分配所有者或参与者角色来帮助管理实验室帐户非常有用。

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>谁将被允许创建和管理实验室？

您可以选择让管理员和教职员工创建和管理实验室。 这些用户（通常来自与您的订阅关联的 Azure AD 租户）被分配到实验室帐户中的实验室创建者角色。

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>是否要使实验室创建者能够保存可在实验室之间共享的图像？

共享映像库是可用于保存和共享映像的存储库。 如果您有多个类需要相同的图像，实验室创建者可以创建映像一次，并在实验室之间共享它。 但是，要开始，您不一定需要共享图像库，因为您以后可以随时添加一个。

如果您对此问题回答"是"，则需要创建共享图像库或将共享图像库附加到实验室帐户。 如果你回答"我不知道"，你可以推迟这个决定，直到以后。

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>您的教室应用商店将使用哪些映像？

Azure 应用商店提供了数百个映像，您可以启用这些映像，以便实验室创建者可以使用该映像创建其实验室。 某些图像可能包含实验室已经需要的所有内容。 在其他情况下，您可以使用图像作为起点，然后实验室创建者可以通过安装其他应用程序或工具对其进行自定义。

如果您不知道需要使用哪些图像，则以后可以随时返回以启用它们。 此外，查看哪些图像可用的最佳方式是首先创建实验室帐户。 这为您提供了访问权限，以便您可以查看可用图像及其内容的列表。
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>实验室的虚拟机是否需要访问其他 Azure 或本地资源？

设置实验室帐户时，还可以选择与虚拟网络对等。 要确定是否需要这样做，请考虑以下问题：

- **您是否需要提供对许可服务器的访问权限？**
  
   如果计划使用 Azure 应用商店映像，操作系统许可证的成本将捆绑到实验室服务的定价中。 因此，您无需为操作系统本身提供许可证。 但是，对于已安装的其他软件和应用程序，您确实需要提供相应的许可证。

- **实验室 VM 是否需要访问其他本地资源（如文件共享或数据库）？**

   创建虚拟网络以提供对本地资源的访问权限，通常通过使用站点到站点的虚拟网络网关。 如果未配置虚拟网络，则需要为此投入更多时间。

- **实验室 VM 是否需要访问位于虚拟网络内的其他 Azure 资源？**

   如果需要访问虚拟网络中*未*受保护的 Azure 资源，则可以通过公共 Internet 访问这些资源，而无需进行任何对等互连。

如果您对一个或多个问题回答"是"，则需要将实验室帐户与虚拟网络对等。 如果你回答"我不知道"，那么你可以推迟这个决定，直到以后。 创建实验室帐户后，始终可以选择对虚拟网络进行对等。

## <a name="set-up-your-lab-account"></a>设置实验室帐户

了解实验室帐户的要求后，即可将其设置。

1. **创建实验室帐户。** 有关[创建实验室帐户](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account)的说明，请参阅教程。

   创建实验室帐户时，您可能会发现熟悉所涉及的 Azure 资源会很有帮助。 有关详细信息，请参阅以下文章：

   - [订阅](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [资源组](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [实验室帐户](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [课堂实验室](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [选择区域和位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [命名资源指南](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **将用户添加到实验室创建者角色。** 有关说明，请参阅[将用户添加到实验室创建者角色](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)。

   此外，有关可以分配给将管理实验室帐户和实验室的用户的不同角色的详细信息，请参阅[有关管理标识的指南](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)。

3. **连接到对等虚拟网络。** 有关说明，请参阅[将实验室网络与对等虚拟网络连接](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)。

   您可能还需要参考有关[配置实验室 VM 地址范围](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)的说明。

4. **启用和查看图像。** 有关说明，请参阅[为实验室创建者启用 Azure 应用商店映像](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

   要查看每个 Azure 应用商店映像的内容，请选择映像名称。 例如，以下屏幕截图显示了 Ubuntu 数据科学 VM 映像的详细信息：

   ![审核 Azure 应用商店映像的屏幕截图](../media/setup-guide/review-marketplace-images.png)

   如果您有一个共享图像库附加到实验室帐户，并且希望允许自定义图像由实验室创建者共享，请完成类似于以下屏幕截图中所示的步骤：

   ![在共享图像库中启用自定义图像的屏幕截图](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>后续步骤

- [管理实验室帐户](how-to-manage-lab-accounts.md)

- [课堂实验室设置指南](setup-guide.md)
