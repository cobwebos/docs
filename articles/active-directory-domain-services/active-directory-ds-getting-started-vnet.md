---
title: "Azure Active Directory 域服务：创建或选择虚拟网络 | Microsoft Docs"
description: "通过 Azure 经典门户启用 Azure Active Directory 域服务"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 13ab1608-e3d8-40de-9f7b-9b5b42199af4
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 457519b00b65b0157effe2d4aba033a1c99852e8
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017


---
# 为 Azure Active Directory 域服务创建或选择虚拟网络
<a id="create-or-select-a-virtual-network-for-azure-active-directory-domain-services" class="xliff"></a>
## 开始之前
<a id="before-you-begin" class="xliff"></a>
请参阅 [Azure Active Directory 域服务的网络注意事项](active-directory-ds-networking.md)。

## 任务 2：创建 Azure 虚拟网络
<a id="task-2-create-an-azure-virtual-network" class="xliff"></a>
下一个配置任务是创建 Azure 虚拟网络和其中的子网。 在虚拟网络的此子网中启用 Azure Active Directory 域服务。 如果已经有你想使用的现有虚拟网络，则可以跳过此步骤。

> [!NOTE]
> 确保创建或选择与 Azure Active Directory 域服务结合使用的 Azure 虚拟网络属于受 Azure Active Directory 域服务支持的 Azure 区域。 若要确定提供 Azure Active Directory 域服务的 Azure 区域，请参阅[按区域列出的 Azure 服务](https://azure.microsoft.com/regions/#services/)。
>
>记下虚拟网络的名称，确保在后续配置步骤中启用 Azure Active Directory 域服务时能够选择正确的虚拟网络。


若要创建 Azure 虚拟网络，以便在其中启用 Azure Active Directory 域服务，请按以下配置说明操作：

1. 转到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左窗格中，选择“网络”。

    ![网络节点](./media/active-directory-domain-services-getting-started/networks-node.png)  
    “虚拟网络”窗口随即打开。
3. 在窗口底部的任务窗格中单击“新建”。

    ![“虚拟网络”窗口](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. 单击“网络服务”，然后选择“虚拟网络”。

    ![虚拟网络 - 快速创建](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
5. 若要创建虚拟网络，请单击“快速创建”。

6. 为虚拟网络指定一个“名称”，并考虑执行以下操作：
    * 可以选择为此网络配置“地址空间”或“最大 VM 计数”。
    * 现在，可以将“DNS 服务器”设置保留为“无”。 启用 Azure Active Directory 域服务后，可以更新设置。
7. 在“位置”下拉列表中，选择受支持的 Azure 区域。  
    若要确定提供 Azure Active Directory 域服务的 Azure 区域，请参阅[按区域列出的 Azure 服务](https://azure.microsoft.com/regions/#services/)。
8. 若要创建虚拟网络，请单击“创建虚拟网络”。

    ![为 Azure Active Directory 域服务创建虚拟网络](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. 创建虚拟网络后，选择虚拟网络的名称，然后单击“配置”选项卡。

    ![创建子网](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. 在“虚拟网络地址空间”下，单击“添加子网”，然后将子网名称指定为“AaddsSubnet”。

    ![为 Azure Active Directory 域服务创建子网](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

11. 若要创建子网，请单击“保存”。


## 后续步骤
<a id="next-step" class="xliff"></a>
[任务 3：启用 Azure Active Directory 域服务](active-directory-ds-getting-started-enableaadds.md)

