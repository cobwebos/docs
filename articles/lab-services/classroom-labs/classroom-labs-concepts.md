---
title: 教室实验室概念-Azure 实验室服务 |Microsoft Docs
description: 了解实验室服务，并了解它如何使其易于创建和管理实验室的基本概念。
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
ms.openlocfilehash: 82f9d7090d7283e0b00b36e0928fffb3395ca4c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679079"
---
# <a name="classroom-labs-concepts"></a>教室实验室概念
以下列表包含密钥的实验室服务概念和定义：

## <a name="quota"></a>Quota
配额是一名教师可以设置时间限制 （以小时为单位） 的一名学生，若要使用实验室 VM。 它可以设置为 0，不受限制或特定的小时数。 如果配额设置为 0，一名学生只能使用虚拟机计划运行时或教师手动打开时在虚拟机上的学生。
 
## <a name="schedules"></a>计划
计划是一名教师可以为类创建的时间段 （一次或重复）。 在实验室中的所有虚拟机自动都启动到日程安排开始处，并计划结束时停止。 当某个计划正在运行时不使用配额小时数。

## <a name="template-virtual-machine"></a>模板虚拟机
在实验室中的模板虚拟机是从其创建所有用户的虚拟机的基础虚拟机映像。 培训师/实验室创建者设置模板的虚拟机，并将其配置与他们想要提供给训练与会者为实验室的软件。 当发布模板 VM 时，Azure 实验室服务创建或更新基于模板的 VM 的实验室 Vm。 


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

### <a name="student"></a>学生
学生执行以下任务：

- 使用实验室用户接收到的来自实验室创建者的注册链接注册实验室。 
- 连接到实验室中的虚拟机，使用它来完成课堂作业、作业和项目。 

## <a name="next-steps"></a>后续步骤
开始设置使用 Azure 实验室服务创建教室实验室所需的实验室帐户：

- [设置实验室帐户](tutorial-setup-lab-account.md)
