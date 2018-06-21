---
title: include 文件
description: include 文件
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 04/05/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 6b6e4afa7c8b18c8ce9af8c6abd371b4321e3343
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852035"
---
| 资源 | 最大限制 |说明|
| --- | --- |---|
| 每个自动化帐户每 30 秒可以提交的新作业的最大数量（非计划的作业） |100 |达到此限制时，后续作业创建请求会失败。 客户端会收到错误响应。|
| 每个自动化帐户相同时间实例并发运行的作业的最大数量（非计划的作业） |200 |达到此限制时，后续作业创建请求会失败。 客户端会收到错误响应。|
| 每个自动化帐户每 30 秒可以导入的模块的最大数量 |5 ||
| 一个模块的最大大小 |100 MB ||
| 作业运行时间 - 免费层 |每个订阅每个日历月 500 分钟 ||
| 每个沙盒允许的最大磁盘空间 <sup>1</sup> |1 GB |仅适用于 Azure 沙盒|
| 沙盒的最大内存量 <sup>1</sup> |400 MB |仅适用于 Azure 沙盒|
| 每个沙盒允许的最大网络套接字数量 <sup>1</sup> |1000 |仅适用于 Azure 沙盒|
| 订阅中自动化帐户的最大数目 |无限制 ||
|单个混合 Runbook 辅助角色上可运行的最大并发作业数|50 ||

<sup>1</sup> 沙盒是可由多个作业使用的共享环境，使用相同沙盒的作业受到该沙盒的资源限制的约束。
