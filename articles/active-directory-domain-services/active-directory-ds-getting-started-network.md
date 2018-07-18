---
title: Azure Active Directory 域服务：入门 | Microsoft Docs
description: 使用 Azure 门户启用 Azure Active Directory 域服务
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: maheshu
ms.openlocfilehash: c6c5762a460fadb04f940742bed759ea17f74aad
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215729"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>使用 Azure 门户启用 Azure Active Directory 域服务


## <a name="before-you-begin"></a>开始之前
请参阅 [Azure Active Directory 域服务的网络注意事项](active-directory-ds-networking.md)。


## <a name="task-2-configure-network-settings"></a>任务 2：配置网络设置
下一个配置任务是创建 Azure 虚拟网络和其中的专用子网。 在虚拟网络的此子网中启用 Azure Active Directory 域服务。 还可以选取现有虚拟网络并在其中创建专用子网。

1. 单击“虚拟网络”以选择一个虚拟网络。
    > [!NOTE]
    > **新部署不支持经典虚拟网络。** 新部署不支持经典虚拟网络。 将继续支持经典虚拟网络中部署的现有托管域。 在不久的将来 Microsoft 将提供相应功能，使你能够将现有托管域从经典虚拟网络迁移到资源管理器虚拟网络。
    >

2. 在“选择虚拟网络”页上，会看到所有现有虚拟网络。 只会看到属于在“基本信息”向导页上选择的资源组和 Azure 位置的虚拟网络。
3. 选择应在其中启用 Azure AD 域服务的虚拟网络。 可以选取现有虚拟网络，也可以创建新的虚拟网络。

  > [!TIP]
  > **启用 Azure AD 域服务后，无法将托管域迁移到另一个虚拟网络。** 选取相应的虚拟网络以启用托管域。 创建托管域后，无法在不删除托管域的情况下将其迁移到另一个虚拟网络。 我们建议在继续操作之前查看 [Azure Active Directory 域服务的网络注意事项](active-directory-ds-networking.md)。  
  >

4. 创建虚拟网络：单击“新建”来创建新的虚拟网络。 对 Azure AD 域服务使用专用子网。 例如，创建具有名称“DomainServices”的子网，从而使其他管理员可以方便地了解在该子网内部署的内容。 完成后，单击“确定”。

    ![选取虚拟网络](./media/getting-started/domain-services-blade-network-pick-vnet.png)

  > [!WARNING]
  > 请确保选取专用 IP 地址空间内的地址空间。 公共地址空间中你未拥有的 IP 地址会导致 Azure AD 域服务内出错。

5. 现有虚拟网络：如果计划选取现有虚拟网络，则[使用虚拟网络扩展创建专用子网](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)，然后选取该子网。 单击“虚拟网络”，以选择一个现有虚拟网络。 单击“子网”，以在现有虚拟网络中选取要在其中启用新托管域的专用子网。 完成后，单击“确定”。

    ![在虚拟网络中选取子网](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **有关选择子网的指导**
  > 1. 对 Azure AD 域服务使用专用子网。 不向此子网部署任何其他虚拟机。 此配置使你可以为工作负荷/虚拟机配置网络安全组 (NSG)，而不会中断托管域。 有关详细信息，请参阅 [Azure Active Directory 域服务的网络注意事项](active-directory-ds-networking.md)。
  2. 不要选择网关子网用于部署 Azure AD 域服务，因为它不是受支持的配置。
  3. 已选择的子网在其地址空间中必须具有至少 3-5 个可用的 IP 地址。
  >

6. 完成后，单击“确定”以转到向导的“管理员组”页。


## <a name="next-step"></a>后续步骤
[任务 3：配置管理组并启用 Azure AD 域服务](active-directory-ds-getting-started-admingroup.md)
