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
ms.openlocfilehash: 5992726893b722b0aa46c976a0167793f5ee6bb4
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82132073"
---
以下限制适用于 Azure 事件网格系统主题和自定义主题，不适用于  事件域。

| 资源 | 限制 |
| --- | --- |
| 每个 Azure 订阅的自定义主题数 | 100 |
| 每个主题的事件订阅数 | 500 |
| 自定义主题的发布速率（入口） | 每个主题每秒 5,000 个事件 |
| 发布请求数 | 每秒 250 个 |
| 事件大小 | 1 MB。 不过，按 64 KB 增量对操作进行收费。 因此，超过 64 KB 的事件将产生操作费用，就好像它们是多个事件。 例如，130 KB 的事件将产生类似于3个不同事件的操作。  |

以下限制仅适用于事件域。

| 资源 | 限制 |
| --- | --- |
| 每个事件域的主题数 | 100,000 |
| 域中每个主题的事件订阅数 | 500 |
| 域范围事件订阅数 | 50 |
| 事件域（入口）的发布速率 | 每秒5000个事件 |
| 发布请求数 | 每秒 250 个 |
| 每个 Azure 订阅的事件域 | 100 |