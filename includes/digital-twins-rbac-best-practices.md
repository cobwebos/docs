---
title: include 文件
description: include 文件
services: azure-digital-twins
author: adamgerard
ms.service: azure-digital-twins
ms.topic: include
ms.date: 09/27/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 075587df445b46c8b03c5448cebf8cd8b12f1179
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323685"
---
基于角色的访问控制是一种继承驱动的安全性策略，用于管理访问权限、权限和角色。 子代角色从父角色继承权限。 在不通过父角色继承权限的情况下，也可以通过分配的方式授予权限。 还可以分配权限，根据需要自定义角色。

例如，“空间管理员”可能需要全局访问权限来运行针对指定空间（包括其下或其内的所有节点）的所有操作，而“设备安装员”可能只需要针对设备和传感器的“读取”和“更新”权限。

根据**最低权限原则**，在任何情况下向角色授予的访问权限都应**准确且不超出其完成任务所需的访问权限**，且仅向某个身份授予：

* 完成其作业所需的访问权限。
* 合适的角色，且仅限于执行其作业。

>[!IMPORTANT]
> **始终遵循最低权限原则**。

需要遵循的其他两个重要的基于角色的访问控制做法：

> [!div class="checklist"]
> * 定期审核角色分配，以验证每个角色均拥有正确的权限。
> * 当相关人员更改角色或分配时，应相应清除原有角色或分配。