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
ms.openlocfilehash: 890095718833c90a6764d0799d2081b393b1d0bd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887736"
---
以下限制适用于 Azure 事件网格系统主题和自定义主题，而*不*是事件域。

| 资源 | Limit |
| --- | --- |
| 每个 Azure 订阅的自定义主题数 | 100 |
| 每个主题的事件订阅数 | 500 |
| 自定义主题的发布速率（入口） | 每个主题每秒 5,000 个事件 |
| 发布请求 | 每秒250 |
| 事件大小 | 支持在公开上市（GA）中 64 KB。 支持 1 MB 目前处于预览阶段。 |

以下限制仅适用于事件域。

| 资源 | Limit |
| --- | --- |
| 每个事件域的主题 | 100,000 |
| 域中每个主题的事件订阅 | 500 |
| 域范围事件订阅 | 50 |
| 事件域（入口）的发布速率 | 每秒5000个事件 |
| 发布请求 | 每秒250 |
| 每个 Azure 订阅的事件域 | 100 |