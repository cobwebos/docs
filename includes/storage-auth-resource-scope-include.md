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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115567"
---
在将 RBAC 角色分配到某个安全主体之前，请确定该安全主体应该获取的访问范围。 最佳做法指出，最好是授予尽可能小的范围。

以下列表描述了可将 Azure Blob 和队列资源访问权限限定到哪些级别，从最小的范围开始：

- **为单个容器中。** 在此范围中，角色分配适用于所有容器，以及容器属性和元数据中的 blob。
- **单个队列。** 在此范围中，角色分配适用于队列，以及队列属性和元数据中的消息。
- **存储帐户。** 在此范围中，角色分配适用于所有容器和其 blob，或对所有队列和其消息。
- **资源组。** 在此范围中，角色分配适用于所有的容器或中的所有存储帐户的资源组中的队列。
- **订阅。** 在此范围中，角色分配适用于所有的容器或队列中的所有存储帐户中的所有订阅中资源组。

> [!IMPORTANT]
> 如果你的订阅包括 Azure DataBricks 命名空间，将会阻止在订阅范围内分配角色授予对 blob 和队列数据的访问。
