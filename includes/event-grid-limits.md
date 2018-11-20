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
ms.openlocfilehash: 2425d9455606f5eabba4b89cfa238b7a928be30e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285750"
---
####

以下限制适用于事件网格系统主题和自定义主题，不适用于事件域。

| 资源 | 限制 |
| --- | --- |
| 每个 Azure 订阅的自定义主题数 | 100 |
| 每个主题的事件订阅数 | 500 |
| 自定义主题的发布速率（入口） | 每个主题每秒 5,000 个事件 |

####

以下限制仅适用于事件域。

| 资源 | 限制 |
| --- | --- |
| 每个事件域的主题数 | 公开预览期间为 1,000 个 |
| 域中每个主题的事件订阅数 | 公开预览期间为 50 个 |
| 域范围事件订阅数 | 公开预览期间为 50 个 |
| 事件域（入口）的发布速率 | 公开预览期间为每秒 5,000 个事件 |