---
title: 并发控制 | Microsoft Docs
description: 云合作伙伴门户发布 API 的并发控制策略。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ecf0bb6ac7fc77e804c9fc8d62aba52810de5640
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805776"
---
<a name="concurrency-control"></a>并发控制
===================

对云合作伙伴门户发布 API 的每个调用都必须显式指定要使用哪个并发控制策略。 如果未提供 **If-Match** 标头，则会导致 HTTP 400 错误响应。 我们提供了两种并发控制策略。

-   **乐观** - 执行更新的客户端将验证自上次读取数据以来数据是否已发生更改。
-   **最后一个胜出** - 客户端直接更新数据，不管自上次读取以来其他应用程序是否已修改了数据。

<a name="optimistic-concurrency-workflow"></a>乐观并发工作流
-------------------------------

我们建议使用乐观并发策略以及以下工作流，以确保不会对你的资源造成意外编辑。

1.  使用 API 检索实体。 响应包括一个 ETag 值，它标识当前所存储的实体版本（在响应时）。
2.  在更新时，在必需的 **If-Match** 请求标头中包括此相同的 ETag 值。
3.  API 会在一个原子事务中将在请求中收到的 ETag 值与实体的当前 ETag 值进行比较。
    *   如果两个 ETag 值不同，则 API 将返回一个 `412 Precondition Failed` HTTP 响应。 此错误表明，自客户端上次检索该实体以来，有其他进程已更新了实体，或者在请求中指定的 ETag 值不正确。
    *  如果两个 ETag 值相同，或者 **If-Match** 标头包含通配符星号字符 (`*`)，则 API 将执行所请求的操作。 API 操作还会更新实体的已存储 ETag 值。


> [!NOTE]
> 在 **If-Match** 标头中指定通配符 (*) 将导致 API 使用“最后一个胜出”并发策略。 在这种情况下，不会进行 ETag 比较，并且会在不执行任何检查的情况下更新资源。 
