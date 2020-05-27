---
title: 课堂实验室概念 - Azure 实验室服务 | Microsoft Docs
description: 了解实验室服务的基本概念，以及它是如何简化实验室创建和管理的。
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 548cacfb76aba9093a59a5c87525d038558bf353
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592621"
---
# <a name="classroom-labs-concepts"></a>教室实验室概念

下面列出了关键的实验室服务概念和定义：

## <a name="quota"></a>Quota

配额是教师为学生设置的使用实验室 VM 的时间限制（以小时为单位）。 可以将它设置为 0 或特定的小时数。 如果配额设置为 0，则学生只能在计划正在运行或教师手动为学生启用虚拟机时使用虚拟机。  

当学生自己启动实验室 VM 时，配额小时数就开始计算。  如果教师手动为学生启动实验室 VM，则不会使用相应学生的配额小时数。

## <a name="schedules"></a>计划

计划是教师可以为课堂创建的时间段，以便学生 VM 可以在课堂时间使用。  计划可以是一次性的，也可以是周期性的。  当计划运行时，不会使用配额小时数。

有三种类型的计划：标准、只启动和只停止。

- **标准**。  此计划在指定的开始时间启动所有学生 VM，并在指定的停止时间关闭所有学生 VM。
- 只启动。   此计划在指定时间启动所有学生 VM。  学生 VM 不会停止，除非学生通过 Azure 实验室服务门户停止 VM 或“只停止”计划运行。
- 只停止。  此计划在指定时间停止所有学生 VM。  

## <a name="template-virtual-machine"></a>模板虚拟机

实验室中的模板虚拟机是基础虚拟机映像，用户的所有虚拟机都是依据其创建的。 培训师/实验室创建者创建模板虚拟机，并为它配置要向受训学员提供的用于在实验室中完成课程练习的软件。 在你发布模板 VM 后，Azure 实验室服务会根据模板 VM 来创建或更新实验室 VM。

## <a name="user-profiles"></a>用户配置文件

本文介绍 Azure 实验室服务中的不同用户配置文件。

### <a name="lab-account-owner"></a>实验室帐户所有者

通常情况下，组织云资源的 IT 管理员拥有 Azure 订阅，不仅是实验室帐户所有者，还执行以下任务：

- 为组织设置实验室帐户。
- 管理并配置所有实验室的策略。
- 向组织中的人员授权，以便这些人员在此实验室帐户下创建实验室。

### <a name="educator"></a>教师

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
