---
title: 开始使用并在团队中创建 Azure 实验室服务实验室
description: 了解如何开始使用并在团队中创建 Azure 实验室服务实验室。
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 4f8c0e2c5f7b75990460b0a1812d4fec3a758259
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092131"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>开始使用并在团队内创建实验室服务实验室

本文介绍如何将 **Azure 实验室服务** 应用添加到团队，以及如何在 MS 团队环境中创建实验室。

## <a name="prerequisites"></a>先决条件

在本教程中，你将为你的团队的虚拟机设置实验室。 若要在实验室帐户中设置实验室，你必须是实验室帐户中以下角色之一的成员：所有者、实验室创建者或参与者。 用来创建实验室帐户的帐户会自动添加到所有者角色。 因此，你可以使用创建实验室帐户所用的用户帐户创建实验室。

下面是在团队内使用 Azure 实验室服务时的典型工作流

1. 用户在 Azure 门户上 [创建实验室帐户](tutorial-setup-lab-account.md#create-a-lab-account) 。
1. [实验室帐户创建者将其他用户添加](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)到**实验室创建者**角色。 例如，实验室帐户创建者/管理员将教师添加到“实验室创建者”角色，这样教师就可以创建课堂实验室。
1. 然后，教师创建实验室，预先配置模板 VM，并发布实验室，将 VM 预配到团队中的每个人。
1. 一旦发布了实验室，就会在首次登录到 Azure 实验室服务时，通过单击包含团队中的 **Azure 实验室服务** 应用的选项卡，在团队成员身份列表中的每个人上分配一个 VM， (SSO) 或通过访问 [实验室网站](https://labs.azure.com)。 然后，用户可以使用 VM 来执行类工作和家庭作业。

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>将 Azure 实验室服务应用作为选项卡添加到团队

作为团队所有者，你可以直接在你的团队渠道中添加 **Azure 实验室服务** 应用，然后，团队中的每个人都可使用该应用。 遵循以下三个步骤：

1. 导航到要在其中添加应用的团队频道，然后选择 " **+** 添加选项卡"。 
1. 在选项卡选项中搜索 " **Azure 实验室服务** " 并添加此应用。 

    > [!NOTE]
    > 只有团队 **所有者** 才能为团队创建实验室。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="添加选项卡":::
1. 选择要用于在此团队中创建教室实验室的实验室服务帐户。 

    Azure 实验室服务使用单一登录进入 [Azure 实验室服务网站](https://labs.azure.com) ，并拉取您有权访问的所有实验室帐户。 

    将显示与团队相同的租户中的帐户，以及你具有其 **所有者**、 **参与者**或 **创建者** 访问权限的帐户。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="添加选项卡" **保存** "，选项卡将添加到通道。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="添加选项卡" 选项卡，并开始管理实验室，如以下文章中所述。

选择实验室帐户后，团队所有者将能够为团队创建实验室。 整个实验室创建过程以及实验室级别的所有任务都可以在团队内执行。 用户可以选择在同一团队和团队所有者内创建多个实验室，在实验室帐户级别进行适当的访问，只会看到与特定团队关联的实验室。

## <a name="next-steps"></a>后续步骤

在团队内创建实验室时，会自动填充实验室用户列表并与团队成员身份同步。 团队中的每个人（包括所有者、成员和来宾）都将自动添加到 lab 用户列表中。 Azure 实验室服务将维护与团队成员身份的同步，并且每24小时触发一次自动同步。 有关详细信息，请参阅：

[管理团队中的实验室服务用户列表](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>另请参阅

另请参阅以下文章：

- [在团队内使用 Azure 实验室服务概述](lab-services-within-teams-overview.md)
- [管理团队中实验室的 VM 池](how-to-manage-vm-pool-within-teams.md)
- [在团队内创建和管理实验室计划](how-to-create-schedules-within-teams.md)
- [在团队内访问 VM –学生视图](how-to-access-vm-for-students-within-teams.md)
- [删除团队中的实验室](how-to-delete-lab-within-teams.md)
