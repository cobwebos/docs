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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460491"
---
向安全主体分配 RBAC 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法规定，始终最好只授予最小的可能范围。

以下列表描述了从最窄的范围开始，可以将对 Azure blob 和队列资源的访问范围的级别：

- **单个容器。** 在此范围内，角色分配适用于容器中的所有 blob，以及容器属性和元数据。
- **单个队列。** 在此范围内，角色分配适用于队列中的消息，以及队列属性和元数据。
- **存储帐户。** 在此范围内，角色分配适用于所有容器及其 blob，或者应用于所有队列及其消息。
- **资源组。** 在此范围内，角色分配适用于资源组中所有存储帐户的所有容器或队列。
- **订阅。** 在此范围内，角色分配适用于订阅中所有资源组的所有存储帐户中的所有容器或队列。

> [!IMPORTANT]
> 如果你的订阅包含 Azure DataBricks 命名空间，则作用域为该订阅的角色将不会授予对 blob 和队列数据的访问权限。 将角色作用域改为资源组、存储帐户、容器或队列。     
