---
title: 使用课堂实验室培训-Azure 实验室服务
description: 本文介绍如何使用 Azure 开发测试实验室在 Azure 上创建实验室，以实现培训方案。
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 155806222f9e11fec177487b7147d81054ac06ed
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717979"
---
# <a name="use-classroom-labs-for-trainings"></a>使用课堂实验室培训
可以为培训设置实验室。 使用 Azure 实验室服务的教室实验室，您可以为您的培训创建实验室，其中每个学员都使用完全相同的隔离环境进行定型。 可应用策略以确保为每个学员提供培训环境（需要时）并包含培训所需的足够资源（例如虚拟机）。 

![课堂实验室](../media/classroom-labs-scenarios/classroom.png)

课堂实验室满足在任何虚拟环境中进行培训所需的以下要求： 

- 学员可快速设置其培训环境
- 每台培训计算机都应相同
- 学员无法看到由其他学员创建的 VM
- 通过确保学员无法获取多于培训所需的资源和在不使用时关闭虚拟机来控制成本
- 轻松与学员共享培训实验室
- 反复重新使用培训实验室

本文介绍了各种 Azure 实验室服务功能，这些功能可用于满足前面所述的培训要求，并详细介绍了如何设置实验室进行培训。  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>创建实验室帐户作为实验室帐户管理员
使用 Azure 实验室服务的第一步是在 Azure 门户中创建实验室帐户。 实验室帐户管理员创建实验室帐户后，管理员会将想要创建实验室的用户添加到实验室创建**者**角色。 讲师通过虚拟机创建实验室，以便学生为其讲授课程做练习。 有关详细信息，请参阅[创建和管理实验室帐户](how-to-manage-lab-accounts.md)。

## <a name="create-and-manage-classroom-labs"></a>创建和管理教室实验室
作为实验室帐户中的实验室创建者角色成员的培训人员可以在实验室帐户中创建一个或多个实验室。 你可以创建和配置模板 VM，其中包含在你的课程中执行练习所需的所有软件。 从可用映像中选择现成的映像，以创建教室实验室，然后通过安装实验室所需的软件对其进行自定义。 有关详细信息，请参阅[创建和管理教室实验室](how-to-manage-classroom-labs.md)。

## <a name="configure-usage-settings-and-policies"></a>配置使用设置和策略
实验室创建者可以向实验室添加用户或从中删除用户，获取注册链接以发送到实验室用户，设置策略，例如设置每个用户的单个配额、更新实验室中可用的 Vm 数量等。 有关详细信息，请参阅[配置使用设置和策略](how-to-configure-student-usage.md)。

## <a name="create-and-manage-schedules"></a>创建并管理计划
借助日程安排，可以配置课堂实验室，这样实验室中的 VM 就能在指定时间自动启动和关闭。 可以定义一次性日程安排，也可以定义定期日程安排。 有关详细信息，请参阅[创建和管理教室实验室计划](how-to-create-schedules.md)。

## <a name="set-up-and-publish-a-template-vm"></a>设置和发布模板 VM
实验室中的模板是基础虚拟机映像，用户的所有虚拟机均依据其创建。 设置模板 VM，使其完全按你要提供给培训参与者的内容进行配置。 你可以提供实验室用户看到的模板的名称和描述。 然后发布此模板，让模板 VM 的实例可供实验室用户使用。 发布模板时，Azure 实验室服务会使用该模板在实验室中创建 VM。 在此过程中创建的 VM 数等于允许进入实验室的最大用户数，这可以在实验室的使用策略中设置。 所有虚拟机的配置都与模板相同。 有关详细信息，请参阅[设置和发布模板虚拟机](how-to-create-manage-template.md)。 

## <a name="use-vms-in-the-classroom-lab"></a>使用课堂实验室中的 Vm
学生或培训与会者注册到实验室，并连接到 VM，以便在课程中完成练习。 有关详细信息，请参阅[如何访问教室实验室](how-to-use-classroom-lab.md)。

## <a name="next-steps"></a>后续步骤
按照文章：[教程：使用 Azure 实验室服务设置实验室帐户](tutorial-setup-lab-account.md)中的说明，开始在课堂实验室中创建实验室帐户。