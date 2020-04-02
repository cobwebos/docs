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
ms.openlocfilehash: 88b37ea4ff717689f05afbb41d33a56a8cbb2c22
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547618"
---
# <a name="lab-account-setup-guide"></a>实验室帐户设置指南

管理员需要完成的第一步是在 Azure 订阅中设置实验室帐户。  实验室帐户是教室实验室的容器，只需几分钟即可设置。

## <a name="understand-your-schools-lab-account-requirements"></a>了解您学校的实验室帐户要求

要了解如何根据学校的需求配置实验室帐户，应考虑以下问题：

**我有权访问 Azure 订阅吗？**

要创建实验室帐户，您需要访问为学校配置的 Azure 订阅;因此，需要访问为学校配置的 Azure 订阅。您的学校可能有一个或多个订阅。  订阅用于管理其中使用的所有 Azure 资源（包括实验室帐户）的计费和安全。

**需要创建多少个实验室帐户？**

要快速入门，一种合理的方法是创建单个实验室帐户，然后根据需要创建其他实验室帐户。  例如，您最终可能会演变为每个部门有一个实验室帐户。

**谁应该是实验室帐户的所有者和贡献者？**

管理员通常是实验室帐户的所有者/贡献者，因为他们负责管理适用于实验室帐户中包含的所有实验室的策略。  创建实验室帐户的人员将自动成为所有者。  您可以添加其他所有者/参与者（通常来自与您的订阅关联的 AAD 租户），通过在实验室帐户级别分配"所有者/参与者"角色来帮助管理实验室帐户。

**谁将被允许创建和管理实验室？**

您可以选择让管理员和\或教职员工创建和管理实验室;这些用户（通常来自与您的订阅关联的 AAD 租户）被分配到实验室帐户中的实验室创建者角色。

**是否要使实验室创建者能够保存可在实验室之间共享的图像？**

共享映像库是可用于保存和共享图像的存储库。  这样做的好处是，如果您有多个类需要相同的图像，实验室创建者可以创建映像一次，并在实验室之间共享它。  但是，要开始，在没有共享图像库的情况下开始是完全合理的;并且，您始终可以选择稍后添加一个。

如果您对此问题回答"是"，则需要创建和\或将共享图像库附加到实验室帐户。  如果你回答，'我不知道'，那么你可以推迟这个决定，直到以后。

将共享图像库附加到实验室帐户时

**教室实验室将使用 Azure 应用商店中的哪些映像？**

Azure 应用商店提供了数百个映像，您可以启用这些映像，以便实验室创建者可以使用该映像创建其实验室。  某些图像可能包含实验室已经需要的所有内容。  在其他情况下，您可以使用图像作为起点，然后实验室创建者可以通过安装其他应用程序、工具等对其进行自定义。

如果您不知道需要使用哪些图像，则以后可以随时返回以启用它们。  此外，查看哪些图像可用的最佳方式是首先创建一个实验室帐户 - 这将为您提供访问权限，以便您可以查看可用图像及其内容的列表。  下面提供了详细信息。
  
**实验室的虚拟机 （VM） 是否需要访问其他 Azure 或预置资源？**

设置实验室帐户时，还可以选择与虚拟网络 （VNet） 对等。  要确定是否需要与 VNet 对等，请考虑以下问题：

- **您是否需要提供对许可服务器的访问权限？**
  
   如果计划使用 Azure 应用商店映像，操作系统许可证的成本将捆绑到实验室服务的定价中，*因此无需为*操作系统本身提供许可证。  但是，对于已安装的其他软件\应用程序，您需要根据需要提供许可证。

- **实验室 VM 是否需要访问其他预置资源，如文件共享、数据库等？**

   需要创建 VNet 以提供对站点到站点虚拟网络网关的访问。  如果您未配置 VNet，则需要为此投入更多时间。  有关如何设置此功能的详细信息，请参阅下文。

- **实验室 VM 是否需要访问位于 VNet 中的其他 Azure 资源？**

    如果需要访问 VNet 中*未*保护的 Azure 资源，则可以通过公共 Internet 访问这些资源，而无需进行任何对等互连。

    如果您对一个或多个问题回答"是"，则需要将实验室帐户与 VNet 对等。  如果您回答"我不知道"，则可以将此决定推迟到以后，因为在创建实验室帐户后，您始终可以选择对 VNet 进行对等。

## <a name="set-up-your-lab-account"></a>设置实验室帐户

了解实验室帐户的要求后，即可将其设置。  请按照本节中的链接了解如何设置实验室帐户：

1. **创建实验室帐户**

   有关[创建实验室帐户](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account)的说明，请参阅教程。

   创建实验室帐户时，您可能会发现熟悉所涉及的 Azure 资源会很有帮助;有关创建这些资源的详细信息和指导，请参阅以下列表：

   - [订阅](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   -  [资源组](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [实验室帐户](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [课堂实验室](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [选择区域\位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [资源命名指南](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **将用户添加到实验室创建者角色**

   有关[将用户添加到实验室创建者角色的](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)教程，请参阅有关说明的教程。

   此外，有关可以分配给将管理实验室帐户和实验室的用户的不同角色的详细信息，请参阅[有关管理标识的指南](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)。

3. **连接到对等 VNet**

   有关[将实验室网络与对等 VNet 连接](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)的操作指南，请参阅说明。

   您可能还需要参考有关[配置实验室 VM 地址范围](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)的说明。

4. **启用和查看图像**

    有关[实验室创建者启用应用商店映像](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)的说明，请参阅操作指南。

    要查看每个应用商店图像的内容，请单击图像名称。  例如，请参阅以下屏幕截图，其中显示了 Ubuntu 数据科学 VM 映像的详细信息：

    ![查看应用商店映像](../media/setup-guide/review-marketplace-images.png)

    如果您有一个共享图像库附加到实验室帐户，并且希望允许自定义图像由实验室创建者共享，则需要完成类似步骤，如以下屏幕截图所示：

    ![在共享图像库中启用自定义图像](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

- [管理实验室帐户](how-to-manage-lab-accounts.md)

- [课堂实验室设置指南](setup-guide.md)
