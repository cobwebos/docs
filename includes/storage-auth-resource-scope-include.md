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
ms.openlocfilehash: ad4b244b58d741ad45463297df5bd358f3ae9918
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449907"
---
将 RBAC 角色分配给安全主体之前，确定安全主体应具有的访问作用的域。 最佳做法要求，最好是始终授予仅尽可能小的范围。

下面列出的您可以将访问权限限制为 Azure blob 和队列资源，最小范围的开始的级别：

- **为单个容器中。** 在此作用域的安全主体有权访问所有的容器，以及容器属性和元数据中的 blob。
- **单个队列。** 在此作用域的安全主体有权访问队列，以及队列属性和元数据中的消息。
- **存储帐户中。** 在此作用域，安全主体具有对所有容器和其 blob，或对所有队列和其消息的访问权限。
- **资源组中。** 在此作用域的安全主体有权访问的所有容器或中的所有存储帐户的资源组中的队列。
- **订阅。** 在此作用域的安全主体有权访问的所有容器或队列中的所有存储帐户中的所有订阅中资源组。
