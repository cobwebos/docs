---
title: Microsoft 团队内的 Azure 实验室服务
description: 概述了如何在 Microsoft 团队中使用 Azure 实验室服务。
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 0c7defdb6f801a10c5fadfdca805381a31f39378
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047822"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Microsoft 团队内的 Azure 实验室服务

可以在 Microsoft 团队中使用 **Azure 实验室服务** 团队应用来利用 Azure 实验室服务。 拥有实验室/参与者/创建者访问权限的任何团队所有者都将能够创建实验室并将虚拟机预配到团队中的每个人。

本文概述在团队内使用 Azure 实验室服务的优点，并提供指向其他文章的链接，以获取有关如何在团队内创建和管理实验室的说明。 

> [!NOTE]
>**Azure 实验室服务** 团队应用只能添加到团队，不能将其添加到个人聊天或群聊天。

## <a name="benefits"></a>优点

Azure 实验室服务与 Microsoft 团队的集成将帮助教师设置课堂环境并在团队内提供虚拟实验室环境 (类) ： 

* 教师可以设置课堂实验室，使学生可以从团队内访问其 Vm，而无需离开团队并导航到 [Azure 实验室服务网站](https://labs.azure.com)。
* 单一登录 (从团队到 Azure 实验室服务的 SSO) 。
* 团队和实验室所有者不需要在两个不同的系统中维护类花名册-实验室用户列表会自动填充于团队成员身份，每24小时自动执行一次同步。 
* 首次发布模板 VM 后，实验室容量 (即，实验室中的 Vm 数) 会根据团队成员身份添加/删除用户自动进行调整。 
* 团队和实验室所有者将仅查看与团队相关的实验室，并且学生只会查看为特定团队预配的 Vm。 
* 用户将自动注册到实验室，并将在发布实验室后首次登录时自动分配 Vm。 教师无需发送邀请，学生无需单独注册实验室。  

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

- [开始使用并在团队内创建实验室](how-to-get-started-create-lab-within-teams.md)
- [管理团队中的实验室用户列表](how-to-manage-user-lists-within-teams.md)
- [管理团队中实验室的 VM 池](how-to-manage-vm-pool-within-teams.md)
- [在团队内创建和管理实验室计划](how-to-create-schedules-within-teams.md)
- [在团队内访问 VM –学生视图](how-to-access-vm-for-students-within-teams.md)
