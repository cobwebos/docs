---
title: 从团队管理 Azure 实验室服务中的 VM 池
description: 了解如何从团队管理 Azure 实验室服务中的 VM 池。
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946627"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>管理团队实验室服务中的 VM 池

首次发布模板 VM 时，将立即启动虚拟机 (VM) 创建。 Vm 等于将创建实验室用户列表中的用户数。 首次登录到 Azure 实验室服务时，会自动将 Vm 分配给学生。 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>发布模板并管理 VM 池

若要发布模板，请依次单击 "团队" "实验室服务" 窗口、"**模板**选项卡-> **...**  ->  "**发布**。

一旦配置了模板 VM，并且当教师选择发布模板时，将创建与实验室的用户列表中的用户数等效的 Vm 数量。 一旦发布了实验室并创建了 Vm 后，用户将自动注册到实验室，在首次登录到 Azure 实验室服务时，会将 Vm 分配给他们，即，当他们首次访问具有 **Azure 实验室服务** 应用程序的选项卡时。 

触发用户列表同步时，实验室) 的实验室容量 (会根据对团队成员身份的更改自动更新。 新的 Vm 将在添加新用户时创建，分配给从团队中删除的用户的 Vm 也会被删除。 有关详细信息，请参阅 [如何管理团队中的用户](how-to-manage-user-lists-within-teams.md)。 

教师可以继续直接从 "VM 池" 选项卡访问学生 Vm。并且，教师可以从 " **虚拟机池** " 选项卡或单击屏幕) 的 "我的 **虚拟机** " 按钮 (的 "我的虚拟机" 按钮访问自己的 vm。 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="VM 池":::

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

- [在团队内使用 Azure 实验室服务概述](lab-services-within-teams-overview.md)
- [开始使用并从团队创建实验室服务实验室](how-to-get-started-create-lab-within-teams.md)
- [管理团队的实验室服务用户列表](how-to-manage-user-lists-within-teams.md)
- [从团队创建实验室服务计划](how-to-create-schedules-within-teams.md)
- [从团队中的实验室服务) 访问 VM (学生视图](how-to-access-vm-for-students-within-teams.md)


