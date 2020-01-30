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
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845977"
---
以下限制适用于 Azure 事件网格系统主题和自定义主题，而*不*是事件域。

| 资源 | 限制 |
| --- | --- |
| 每个 Azure 订阅的自定义主题数 | 100 |
| 每个主题的事件订阅数 | 500 |
| 自定义主题的发布速率（入口） | 每个主题每秒 5,000 个事件 |
| 发布请求 | 每秒250 |
| 事件大小 | 1 MB （作为多个 64 KB 事件收费） |

以下限制仅适用于事件域。

| 资源 | 限制 |
| --- | --- |
| 每个事件域的主题 | 100,000 |
| 域中每个主题的事件订阅 | 500 |
| 域范围事件订阅 | 50 |
| 事件域（入口）的发布速率 | 每秒5000个事件 |
| 发布请求 | 每秒250 |
| 每个 Azure 订阅的事件域 | 100 |