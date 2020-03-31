---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460491"
---
在将 RBAC 角色分配到某个安全主体之前，请确定该安全主体应该获取的访问范围。 最佳做法指出，最好是授予尽可能小的范围。

以下列表描述了可将 Azure Blob 和队列资源访问权限限定到哪些级别，从最小的范围开始：

- **单个容器。** 在此范围内，角色分配适用于容器中的所有 Blob，以及容器属性和元数据。
- **单个队列。** 在此范围内，角色分配适用于队列中的消息，以及队列属性和元数据。
- **存储帐户。** 在此范围内，角色分配适用于所有容器及其 Blob，或者适用于所有队列及其消息。
- **资源组。** 在此范围内，角色分配适用于资源组中所有存储帐户内的所有容器或队列。
- **订阅。** 在此范围内，角色分配适用于订阅中所有资源组内的所有存储帐户中的所有容器或队列。

> [!IMPORTANT]
> 如果订阅包含 Azure DataBricks 命名空间，则受订阅范围的角色将不会授予对 Blob 和队列数据的权限。 而是将角色范围限定到资源组、存储帐户或容器或队列。     
