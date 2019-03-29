---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a50eb45291ada23f55057f3c440c5b8b23cc4bce
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622631"
---
在将 RBAC 角色分配到某个安全主体之前，请确定该安全主体应该获取的访问范围。 最佳做法指出，最好是授予尽可能小的范围。

以下列表描述了可将 Azure Blob 和队列资源访问权限限定到哪些级别，从最小的范围开始：

- **为单个容器中。** 在此作用域的安全主体有权访问所有的容器，以及容器属性和元数据中的 blob。
- **单个队列。** 在此作用域的安全主体有权访问队列，以及队列属性和元数据中的消息。
- **存储帐户。** 安全主体可在此范围访问所有容器及其 Blob，或者访问所有队列及其消息。
- **资源组。** 安全主体可在此范围访问资源组中所有存储帐户内的所有容器或队列。
- **订阅。** 安全主体可在此范围访问订阅中所有资源组内的所有存储帐户中的所有容器或队列。

> [!IMPORTANT]
> 如果你的订阅包括 Azure DataBricks 命名空间，将会阻止在订阅范围内分配角色授予对 blob 和队列数据的访问。