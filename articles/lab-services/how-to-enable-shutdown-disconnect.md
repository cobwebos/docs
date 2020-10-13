---
title: 在 Azure 实验室服务中配置实验室的 Vm 自动关闭
description: 了解如何启用或禁用在断开远程桌面连接时自动关闭 VM。
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 67b51941207fdd4913df9a92362959bbd468d336
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91649875"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>配置实验室的 Vm 自动关闭

本文介绍如何配置实验室的 Vm 自动关闭。

可以启用多个自动关闭成本控制功能，以便在不使用虚拟机的情况主动阻止额外成本。 以下三种自动关闭和断开连接功能的组合将捕获用户意外离开其虚拟机运行的大多数情况：
 
* 自动断开用户与 OS 认为空闲的虚拟机的连接。
* 用户断开连接时，自动关闭虚拟机。
* 自动关闭已启动但用户不连接的虚拟机。

若要详细了解自动关闭功能，请参阅 [自动关闭设置的最大化成本控制](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) 部分。

实验室帐户管理员可以为你在其中创建实验室的实验室帐户配置此设置。 有关详细信息，请参阅 [配置实验室帐户的 vm 自动关闭](how-to-configure-lab-accounts.md)。 作为实验室所有者，你可以在创建实验室时或在创建实验室后重写该设置。 

## <a name="configure-for-the-lab-level"></a>为实验室级别配置

可以在 [Azure 实验室服务](https://labs.azure.com/)中配置自动关闭设置。

* 在 **实验室策略**) 创建实验室 (时，或
* 创建实验室后 (**设置**) 

> [!div class="mx-imgBorder"]
> ![在创建实验室时配置](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

请确保在 " [利用自动关闭设置最大化成本控制](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) " 部分中查看有关自动关闭的详细信息。

> [!WARNING]
> 如果在将 RDP 会话与 VM 断开连接之前，在 VM 上关闭 Linux 或 Windows 操作系统 (操作系统) ，自动关闭功能将无法正常工作。  
## <a name="next-steps"></a>后续步骤

[用于课堂实验室的仪表板](use-dashboard.md)
