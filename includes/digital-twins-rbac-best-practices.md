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
ms.openlocfilehash: a03f2b4e8d216db3764af03dc06b5188289ffc92
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50964197"
---
基于角色的访问控制是一种继承驱动的安全策略，用于管理访问权限、权限和角色。 子代角色从父角色继承权限。 也可以在不从父角色继承的情况下分配权限。 还可以指定权限以根据需要自定义角色。

例如，空间管理员可能需要全局访问权限，才能运行指定空间的所有操作。 访问涉及空间下面或空间内的所有节点。 设备安装程序可能只需要设备和传感器的*读取*和*更新*权限。

根据最小特权原则，在任何情况下向角色授予的访问权限都应*准确且不超出其完成任务所需的访问权限*。 根据此原则，只向某个身份授予：

* 完成其作业所需的访问权限。
* 合适的角色，且仅限于执行其作业。

>[!IMPORTANT]
> 始终遵循最小特权原则。

需要遵循的其他两个重要的基于角色的访问控制做法：

> [!div class="checklist"]
> * 定期审核角色分配，以验证每个角色均拥有正确的权限。
> * 当个人更改角色或分配时，应清除原有角色和分配。