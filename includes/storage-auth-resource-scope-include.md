---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 518c57bc3327511b70deef143826f2a1b9df8639
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172620"
---
在将 RBAC 角色分配到某个安全主体之前，请确定该安全主体应该获取的访问范围。 最佳做法指出，最好是授予尽可能小的范围。

以下列表描述了可将 Azure Blob 和队列资源访问权限限定到哪些级别，从最小的范围开始：

- **单个容器。** 在此范围内，角色分配适用于容器中的所有 Blob，以及容器属性和元数据。
- **单个队列。** 在此范围内，角色分配适用于队列中的消息，以及队列属性和元数据。
- **存储帐户。** 在此范围内，角色分配适用于所有容器及其 Blob，或者适用于所有队列及其消息。
- **资源组。** 在此范围内，角色分配适用于资源组中所有存储帐户内的所有容器或队列。
- **订阅。** 在此范围内，角色分配适用于订阅中所有资源组内的所有存储帐户中的所有容器或队列。

> [!IMPORTANT]
> 如果你的订阅包括 Azure DataBricks 命名空间，将会阻止在订阅范围内分配角色授予对 blob 和队列数据的访问。
