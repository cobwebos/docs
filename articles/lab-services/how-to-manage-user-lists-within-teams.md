---
title: 管理团队的 Azure 实验室服务用户列表
description: 了解如何从团队管理 Azure 实验室服务用户列表。
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946599"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>管理团队的实验室服务用户列表

在团队内创建实验室时 (参阅 [开始使用并从团队创建实验室服务实验室](how-to-get-started-create-lab-within-teams.md)) ，将自动填充实验室用户列表并与团队成员身份同步。 团队中的每个人（包括所有者、成员和来宾）都将自动添加到 lab 用户列表中。 Azure 实验室服务维护与团队成员身份的同步，每24小时触发一次自动同步。 

## <a name="sync-users"></a>同步用户

团队成员身份更新后，教师可以使用 " **同步** " 按钮来触发手动同步。 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="同步用户":::

自动或手动同步完成后，根据实验室是否已发布，满足以下条件。

* 如果实验室至少发布一次：
    * 每次对团队成员身份进行更改时，都会在实验室用户列表中添加或删除用户。 
* 如果实验室至少已发布一次，则除了添加或删除用户外，实验室容量也会自动更新。
    * 如果对团队有任何新的添加内容，则会创建新的 Vm。
    * 如果已从团队中删除任何用户，则也将删除关联的 VM。

## <a name="next-steps"></a>后续步骤

一旦配置了模板 VM，并且当教师选择发布模板时，将创建与实验室的用户列表中的用户数等效的 Vm 数量。 一旦发布了实验室并创建了 Vm 后，用户将自动注册到实验室，在首次登录到 Azure 实验室服务时，会将 Vm 分配给他们，即，当他们首次访问具有 **Azure 实验室服务** 应用程序的选项卡时。 

若要发布模板 VM，请参阅 "团队实验室服务" 窗口，选择 "**模板**选项卡-> **...**  ->  "**发布**。

若要管理 VM 池，请参阅 [在实验室服务中从团队管理 vm 池](how-to-manage-vm-pool-within-teams.md)。

### <a name="also-review"></a>另请查看

请参阅下列文章：

- [在团队内使用 Azure 实验室服务概述](lab-services-within-teams-overview.md)
- [开始使用并从团队创建实验室服务实验室](how-to-get-started-create-lab-within-teams.md)
- [从团队创建实验室服务计划](how-to-create-schedules-within-teams.md)
- [从团队中的实验室服务) 访问 VM (学生视图](how-to-access-vm-for-students-within-teams.md)

