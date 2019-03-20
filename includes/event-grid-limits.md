---
title: include 文件
description: include 文件
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 04/30/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 443db1b4609e62fb7c57de417e42a2b4d0737ada
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553312"
---
以下限制适用于 Azure 事件网格系统主题和自定义主题*不*事件域。

| 资源 | 限制 |
| --- | --- |
| 每个 Azure 订阅的自定义主题数 | 100 |
| 每个主题的事件订阅数 | 500 |
| 自定义主题的发布速率（入口） | 每个主题每秒 5,000 个事件 |

以下限制适用于事件的域。

| 资源 | 限制 |
| --- | --- |
| 每个事件域的主题 | 公开预览期间为 1,000 个 |
| 每个域中的主题的事件订阅 | 公开预览期间为 50 个 |
| 域作用域的事件订阅 | 公开预览期间为 50 个 |
| 发布事件域 （入口） 的速率 | 公开预览期间为每秒 5,000 个事件 |