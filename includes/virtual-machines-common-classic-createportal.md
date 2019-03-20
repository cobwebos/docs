---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 361d0ce5091d80198d47e4ad164f7cba8e21a55d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58114919"
---
简单地来说，自定义虚拟机是指使用“市场”中的“特别推荐的应用”创建的虚拟机，因为它可为你完成大部分的工作。 但是，用户仍然可以进行配置选择，可以选择的项目如下：

* 将虚拟机连接到虚拟网络。
* 安装 Azure 虚拟机代理和 Azure 虚拟机扩展，如反恶意软件。
* 将虚拟机添加到现有云服务。
* 将虚拟机添加到现有存储帐户。
* 将虚拟机添加到可用性集。

<!--
> [!IMPORTANT]
> If you want your virtual machine to use a virtual network so you can connect to it directly by host name or set up cross-premises connections, make sure that you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For details on virtual networks, see [Azure Virtual Network overview](../articles/virtual-network/virtual-networks-overview.md).
>
>
 -->

> [!IMPORTANT]
> 如果希望虚拟机使用虚拟网络，请确保在创建虚拟机时指定虚拟网络。
> 
> * 使用虚拟网络有两个好处：直接连接到虚拟机和设置跨界连接。
> 
> * 仅当创建虚拟机后，才能将该虚拟机配置为加入虚拟网络。 有关虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../articles/virtual-network/virtual-networks-overview.md)。

## <a name="to-create-the-virtual-machine"></a>创建虚拟机
