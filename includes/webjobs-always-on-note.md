---
title: include 文件
description: include 文件
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424152"
---
> [!NOTE]
> Web 应用可在进入非活动状态 20 分钟后超时。 只有向实际 Web 应用发出的请求才会重置计时器。 在 Azure 门户中查看应用的配置或向高级工具站点 (`https://<app_name>.scm.azurewebsites.net`) 发出请求不会重置计时器。 如果应用运行连续或计划 (Timer 触发器) Web 作业, 请启用**Always On**以确保 web 作业可靠运行。 此功能仅在基本、标准和高级[定价层](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)中提供。
