---
title: 使用教室实验室进行培训-Azure 实验室服务 |Microsoft Docs
description: 了解如何使用 Azure 开发测试实验室培训的方案。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 4d2ba11181977f1976b5ae933e8b93a92424fa96
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2019
ms.locfileid: "56809160"
---
# <a name="use-classroom-labs-for-trainings"></a>使用教室实验室进行培训
您可以设置为培训实验室。 Azure 实验室服务的教室实验室，可创建您的培训实验室其中每个学员都用于定型的相同和隔绝的环境。 可应用策略以确保为每个学员提供培训环境（需要时）并包含培训所需的足够资源（例如虚拟机）。 

![教室实验室](../media/classroom-labs-scenarios/classroom.png)

教室实验室满足以下要求所需进行任何虚拟环境中的训练： 

- 学员可快速设置其培训环境
- 每台培训计算机都应相同
- 学员无法看到由其他学员创建的 VM
- 通过确保学员无法获取多于培训所需的资源和在不使用时关闭虚拟机来控制成本
- 轻松与学员共享培训实验室
- 反复重新使用培训实验室

在本文中，您还需要了解各种 Azure 实验室服务功能可用于满足前面所述的培训要求和详细的步骤，你可以遵循培训实验室进行设置。  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>作为实验室帐户管理员创建的实验室帐户
使用 Azure 实验室服务的第一步是在 Azure 门户中创建实验室帐户。 实验室帐户管理员创建实验室帐户后，管理员添加用户想要创建到实验室**实验室创建者**角色。 培训师使用面向学生为他们讲授的课程练习的虚拟机创建实验室。 有关详细信息，请参阅[创建和管理实验室帐户](how-to-manage-lab-accounts.md)。

## <a name="create-and-manage-classroom-labs"></a>创建和管理教室实验室
培训师，作为实验室帐户中的实验室创建者角色的成员可以创建一个或多个实验室的实验室帐户中。 创建并配置模板 VM 与执行您的课程中练习所需的所有软件。 选择从可用映像用于创建教室实验室的现成映像，然后安装实验室所需的软件将其自定义。 有关详细信息，请参阅[创建和管理教室实验室](how-to-manage-classroom-labs.md)。

## <a name="configure-usage-settings-and-policies"></a>配置使用设置和策略
实验室创建者可以添加或删除用户到实验室，获取注册链接发送给实验室用户，设置策略，如每个用户，设置单独的配额更新实验室，和的详细信息中提供的 Vm 的数目。 有关详细信息，请参阅[配置使用情况设置和策略](how-to-configure-student-usage.md)。

## <a name="create-and-manage-schedules"></a>创建并管理计划
借助日程安排，可以配置课堂实验室，这样实验室中的 VM 就能在指定时间自动启动和关闭。 可以定义一次性日程安排，也可以定义定期日程安排。 有关详细信息，请参阅[创建和管理计划，教室实验室](how-to-create-schedules.md)。

## <a name="set-up-and-publish-a-template-vm"></a>设置和发布模板 VM
实验室中的模板是基础虚拟机映像，用户的所有虚拟机均依据其创建。 设置的模板 VM，以便配置有完全要提供给训练与会者。 你可以提供实验室用户看到的模板的名称和描述。 然后发布此模板，让模板 VM 的实例可供实验室用户使用。 发布模板时，Azure 实验室服务会使用该模板在实验室中创建 VM。 在此过程中创建的 VM 数等于允许进入实验室的最大用户数，这可以在实验室的使用策略中设置。 所有虚拟机的配置都与模板相同。 有关详细信息，请参阅[设置并发布模板的虚拟机](how-to-create-manage-template.md)。 

## <a name="use-vms-in-the-classroom-lab"></a>在教室实验室中使用 Vm
学生或培训与会者到本实验中，注册并连接到 VM 来执行操作过程中的练习。 有关详细信息，请参阅[如何访问教室实验室](how-to-use-classroom-lab.md)。

## <a name="next-steps"></a>后续步骤
开始按照本文中的说明在教室实验室中创建实验室帐户：[教程：设置实验室帐户通过 Azure 实验室服务](tutorial-setup-lab-account.md)。