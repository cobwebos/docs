---
title: Azure 实验室服务 | Microsoft Docs
description: 了解实验室服务如何轻松创建、管理以及保护包含由开发者、测试人员、教育工作者、学生等人群使用的虚拟机的实验室。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: e9c3cae7c7129cc489ddd38b5b2de18dd6f52e58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660479"
---
# <a name="introduction-to-classroom-labs"></a>教室实验室简介
使用 Azure 实验室服务，可在云中快速设置教室实验室环境。 教师创建教室实验室、预配 Windows 或 Linux 虚拟机、在课堂上安装必要的软件和工具实验室，并将其提供给学生使用。 课堂上的学生连接到实验室中的虚拟机 (VM)，并使用虚拟机完成项目、作业和课堂练习。 

教室实验室是由 Azure 管理的托管实验室。 从启动虚拟机 (VMs) 到处理错误及扩展基础结构，该服务本身可以处理托管实验室的所有基础结构管理。 可以指定所需的基础结构类型，并安装课程所需的任何工具或软件。 托管实验室目前提供预览版。  

## <a name="scenarios"></a>方案
下面是 Azure 实验室服务的教室实验室支持的主要方案： 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>在云中为教室设置可调整大小的计算机实验室  

- 创建托管的教室实验室。 只需向该服务告知你的需求，它便会为你创建和管理实验室的基础结构，以便你专注于教学而不是实验室的技术详细信息。 
- 为学生提供虚拟机实验室，这些虚拟机完全根据课程所需进行配置。 向每位学生提供使用 VM 的有限时间，用于完成课程作业。  
- 将学校的物理计算机实验室移动到云。 自动缩放 VM 数量，缩放时不会超过实验室设置的最大用量和成本阈值。 
- 完成后单击即可删除实验室。 

## <a name="user-profiles"></a>用户配置文件
本文介绍 Azure 实验室服务中的不同用户配置文件。 

### <a name="lab-account-owner"></a>实验室帐户所有者
通常情况下，组织云资源的 IT 管理员（其拥有 Azure 订阅）充当实验室帐户所有者并执行以下任务：   

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
