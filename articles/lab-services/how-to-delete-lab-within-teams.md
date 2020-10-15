---
title: 从团队中删除 Azure 实验室服务实验室
description: 了解如何从团队中删除 Azure 实验室服务实验室。
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 1d2fe73d33a88e595f42b47c1e7122dcbdfe1063
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094272"
---
# <a name="delete-labs-within-teams"></a>删除团队中的实验室

本文介绍如何从 **Azure 实验室服务** 应用删除实验室。

## <a name="prerequisites"></a>先决条件

* 在 Azure 门户中[创建实验室服务帐户](tutorial-setup-lab-account.md#create-a-lab-account)。
* [开始使用并在团队内创建实验室服务实验室](how-to-get-started-create-lab-within-teams.md)。

## <a name="delete-labs"></a>删除实验室

可以通过直接删除实验室来删除在 [实验室服务网站](https://labs.azure.com) 中创建的实验室，如在 [Azure 实验室服务中管理教室实验室](how-to-manage-classroom-labs.md)中所述。 

删除团队时，也会触发实验室删除。 如果从中创建实验室的团队被删除，则在触发自动用户列表同步后，将自动删除 "实验室"。 

> [!IMPORTANT]
> 删除选项卡或卸载应用程序不会导致删除实验室。 

如果删除该选项卡，则在 "团队成员身份" 列表中的用户仍将能够访问 [实验室服务网站](https://labs.azure.com) 上的 vm，除非通过删除网站上的实验室或删除团队显式触发了实验室删除。 

## <a name="next-steps"></a>后续步骤

- [在团队内使用 Azure 实验室服务概述](lab-services-within-teams-overview.md)
- [管理团队中的实验室用户列表](how-to-manage-user-lists-within-teams.md)
- [管理团队中实验室的 VM 池](how-to-manage-vm-pool-within-teams.md)
- [在团队内创建和管理实验室计划](how-to-create-schedules-within-teams.md)
- [在团队内访问 VM –学生视图](how-to-access-vm-for-students-within-teams.md)

