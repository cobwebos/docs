---
title: 课堂实验室概念-Azure 实验室服务 |Microsoft Docs
description: 了解实验室服务的基本概念，以及如何轻松地创建和管理实验室。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 348340516f9332f5492c7ce60c3d164da44a008c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120895"
---
# <a name="classroom-labs-concepts"></a>教室实验室概念

以下列表包含重要的实验室服务概念和定义：

## <a name="quota"></a>“配额”

配额是指教师可以设置为学生使用实验室 VM 的时间限制（以小时为单位）。 它可以设置为0或指定的小时数。 如果配额设置为0，则学生只能在计划运行时或当教师手动打开学生的虚拟机时使用虚拟机。  

当学生自行启动实验室 VM 时，将对配额小时计数。  如果教师手动启动了学生的实验室 VM，则不会对该学生使用配额时间。

## <a name="schedules"></a>计划

计划是教师可以为类创建的时间段（一次或定期）。 实验室中的所有虚拟机都将在计划开始时自动启动，并在计划结束时停止。 在计划运行时不使用配额时间。

## <a name="template-virtual-machine"></a>模板虚拟机

实验室中的模板虚拟机是创建所有用户虚拟机的基本虚拟机映像。 讲师/实验室创建者设置模板虚拟机，并对其进行配置，使其与他们要提供给培训与会者进行实验室的软件。 发布模板 VM 时，Azure 实验室服务将基于模板 VM 创建或更新实验室 Vm。

## <a name="user-profiles"></a>用户配置文件

本文介绍 Azure 实验室服务中的不同用户配置文件。

### <a name="lab-account-owner"></a>实验室帐户所有者

通常情况下，组织云资源的 IT 管理员（其拥有 Azure 订阅）充当实验室帐户所有者并执行以下任务：

- 为组织设置实验室帐户。
- 管理并配置所有实验室的策略。
- 向组织中的人员授权，以便这些人员在此实验室帐户下创建实验室。

### <a name="professor"></a>教授

通常，用户（例如教师或在线培训师）会在实验室帐户下创建教室实验室。 教师执行以下任务：

- 创建教室实验室。
- 创建实验室中的虚拟机。
- 在虚拟机上安装合适的软件。
- 指定能够访问实验室的人员。
- 向学生提供实验室的注册链接。

### <a name="student"></a>Student

学生执行以下任务：

- 使用实验室用户接收到的来自实验室创建者的注册链接注册实验室。
- 连接到实验室中的虚拟机，使用它来完成课堂作业、作业和项目。

## <a name="next-steps"></a>后续步骤

开始设置使用 Azure 实验室服务创建教室实验室所需的实验室帐户：

- [设置实验室帐户](tutorial-setup-lab-account.md)
