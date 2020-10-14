---
title: 在团队内创建 Azure 实验室服务计划
description: 了解如何在团队内创建实验室服务计划。
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042331"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>在团队内创建和管理实验室服务计划

借助日程安排，可以配置课堂实验室，这样实验室中的 VM 就能在指定时间自动启动和关闭。 可以定义一次性日程安排，也可以定义定期日程安排。 下面的过程逐步介绍了如何创建和管理课堂实验室日程安排： 

下面是计划如何影响实验室虚拟机： 

- 计划中未包含模板虚拟机。 
- 仅启动已分配的虚拟机。 这意味着，如果某个计算机未由最终用户声称 (学生) ，则该计算机将不会在计划的时间启动。 
- 所有虚拟机都 (是否根据实验室计划停止了用户或未) 的声明。 

> [!IMPORTANT]
> VM 的计划运行时间不计入分配给用户的配额。 配额是指学生在 VM 上花费的计划外时间。 

与 [实验室网站](https://labs.azure.com)一样，用户可以在团队内创建、编辑和删除实验室计划。 请参阅 [创建和管理计划](how-to-create-schedules-within-teams.md)的文章。

## <a name="automatic-shutdown-and-disconnect-settings"></a>自动关闭和断开连接设置

您可以启用多个自动关闭成本控制功能，以便在不使用虚拟机的情况主动阻止额外成本。 以下三种自动关闭和断开连接功能的组合将捕获用户意外离开其虚拟机运行的大多数情况：
 
- 自动断开用户与 OS 认为空闲的虚拟机的连接。
- 用户断开连接时，自动关闭虚拟机。
- 自动关闭已启动但用户不连接的虚拟机。

有关更多详细信息，请参阅 [配置实验室的自动关闭设置一](how-to-enable-shutdown-disconnect.md)文。

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

- [在团队内使用 Azure 实验室服务概述](lab-services-within-teams-overview.md)
- [开始使用并在团队内创建实验室](how-to-get-started-create-lab-within-teams.md)
- [管理团队中的实验室用户列表](how-to-manage-user-lists-within-teams.md)
- [管理团队中实验室的 VM 池](how-to-manage-vm-pool-within-teams.md)
- [在团队内访问 VM –学生视图](how-to-access-vm-for-students-within-teams.md)
