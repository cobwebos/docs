---
title: 开始使用并从团队创建 Azure 实验室服务实验室
description: 了解如何开始使用，并从团队创建 Azure 实验室服务实验室。
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: b585196fe61a09697cfa203aaa33f08afae2b427
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946604"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>开始使用并从团队创建实验室服务实验室

本文介绍如何将 Azure 实验室服务应用添加到团队。 然后，如何从团队创建实验室。

## <a name="add-a-lab-services-app-to-teams"></a>向团队添加实验室服务应用

你可以直接在你的团队渠道中添加实验室服务，然后，团队中的所有人都可以使用该应用。 执行以下三个步骤：

1. 导航到要在其中添加应用的团队频道，并选择 **+** 添加选项卡，方法是单击 "..."从右侧窗口的顶部。 
1. 在选项卡选项中搜索 " **Azure 实验室服务** " 并添加此应用。 

    > [!NOTE]
    > 只有团队 **所有者** 才能为团队创建实验室。
1. 选择要用于在此团队中创建教室实验室的实验室服务帐户。 

    Azure 实验室服务使用单一登录进入 [Azure 实验室服务网站](https://labs.azure.com) ，并拉取您有权访问的所有实验室帐户。 

    将显示与团队相同的租户中的帐户，以及你具有其 **所有者**、 **参与者**或 **创建者** 访问权限的帐户。 

   ![欢迎使用 ALS](./media/integrate-with-teams/welcome.png) 
1. 按 " **保存** " 将应用添加到团队，并将该选项卡添加到频道。 

    现在，你可以从通道中选择 " **Azure 实验室服务** " 选项卡，并开始管理实验室，如以下步骤所述。

    团队中的某位成员添加选项卡后，通道中的每位用户都可看到该选项卡。 有权访问该应用的所有用户都可以使用其用于 Microsoft Teams 的凭据进行单一登录访问。 无权访问该应用的所有用户可以在 Teams 看到该选项卡，但需要获得对本地应用和该应用的 Azure 门户已发布版本的访问权限才不会被阻止。

## <a name="create-a-classroom-lab"></a>创建课堂实验室

选择实验室帐户后，团队所有者将能够为团队创建实验室。 整个实验室创建过程以及实验室级别的所有任务都可以在团队内执行。 用户可以选择在同一团队和团队所有者内创建多个实验室，在实验室帐户级别进行适当的访问，只会看到与特定团队关联的实验室。

## <a name="giving-access-to-users-of-the-lab-account"></a>授予对实验室帐户用户的访问权限

需要在 [Azure](https://ms.portal.azure.com/) 门户中设置对实验室帐户级别的用户的访问权限。

1. 在 Azure 门户中，导航到 Azure 实验室服务帐户。 
1. 在“实验室帐户”页上，选择“访问控制(IAM)”，选择工具栏上的“添加”，然后选择工具栏上的“+ 添加角色分配”   。 

    ![“访问控制”->“添加角色分配”按钮](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在“添加角色分配”页上，选择“实验室创建者”作为“角色”，选择想要添加到“实验室创建者”角色的用户，然后选择“保存”   。 

    ![添加实验室创建者](./media/tutorial-setup-lab-account/add-lab-creator.png)

### <a name="creating-classroom-labs"></a>创建教室实验室

无论是从团队还是 [实验室服务网站](https://labs.azure.com)创建实验室，教室实验室创建过程都是相同的。 

有关设置此文章的详细信息概述实验室创建过程： [在 Azure 实验室服务中管理教室实验室](how-to-manage-classroom-labs.md)。

## <a name="deleting-classroom-labs"></a>删除教室实验室

可以通过直接删除实验室来删除在 [实验室服务网站](https://labs.azure.com) 中创建的实验室，如在 [Azure 实验室服务中管理教室实验室](how-to-manage-classroom-labs.md)中所述。 

删除团队时，也会触发实验室删除。 如果从中创建实验室的团队被删除，则在触发自动用户列表同步后，将自动删除 "实验室"。 

删除选项卡或卸载应用程序不会导致删除实验室。 如果删除该选项卡，则在 "团队成员身份" 列表中的用户仍将能够访问 [实验室服务网站](https://labs.azure.com) 上的 vm，除非通过删除网站上的实验室或删除团队显式触发了实验室删除。 

## <a name="next-steps"></a>后续步骤

在团队内创建实验室时，会自动填充实验室用户列表并与团队成员身份同步。 团队中的每个人（包括所有者、成员和来宾）都将自动添加到 lab 用户列表中。 Azure 实验室服务将维护与团队成员身份的同步，并且每24小时触发一次自动同步。 有关详细信息，请参阅：

[管理团队的实验室服务用户列表](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>另请参阅

另请参阅以下文章：

- [在团队内使用 Azure 实验室服务概述](lab-services-within-teams-overview.md)
- [管理团队实验室服务中的 VM 池](how-to-manage-vm-pool-within-teams.md)
- [从团队创建实验室服务计划](how-to-create-schedules-within-teams.md)
- [从团队中的实验室服务) 访问 VM (学生视图](how-to-access-vm-for-students-within-teams.md)

