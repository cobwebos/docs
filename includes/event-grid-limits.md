---
title: include 文件
description: include 文件
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "66376935"
---
以下限制适用于 Azure 事件网格系统主题和自定义主题，而*不*是事件域。

| Resource | 限制 |
| --- | --- |
| 每个 Azure 订阅的自定义主题数 | 100 |
| 每个主题的事件订阅数 | 500 |
| 自定义主题的发布速率（入口） | 每个主题每秒 5,000 个事件 |
| 发布请求 | 每秒250 |
| 事件大小 | 支持在公开上市（GA）中 64 KB。 支持 1 MB 目前处于预览阶段。 |

以下限制仅适用于事件域。

| Resource | 限制 |
| --- | --- |
| 每个事件域的主题 | 公开预览期间为 1,000 个 |
| 域中每个主题的事件订阅 | 公开预览期间为 50 个 |
| 域范围事件订阅 | 公开预览期间为 50 个 |
| 事件域（入口）的发布速率 | 公开预览期间为每秒 5,000 个事件 |
| 发布请求 | 每秒250 |