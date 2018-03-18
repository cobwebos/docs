---
title: "include 文件"
description: "include 文件"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 13aa40331849c775898913129f8048a06a1f8456
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
可以将标记应用到 Azure 资源，以逻辑方式按类别对其进行组织。 每个标记包含一个名称和一个值。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

应用标记以后，即可使用该标记名称和值检索订阅中的所有资源。 使用标记可以从不同资源组中检索相关资源。 需要为计费或管理目的组织资源时，此方法十分有用。

以下限制适用于标记：

* 每个资源或资源组最多可以有 15 个标记名称值对。 此限制仅适用于直接应用到资源组或资源的标记。 资源组可以包含多个资源，这些资源每个都有 15 个标记名称值对。 如果有超过 15 个需要与资源关联的值，请将 JSON 字符串用于标记值。 JSON 字符串可以包含多个应用于单个标记名称的值。 本文介绍了一个将 JSON 字符串分配给标记的示例。
* 标记名称不能超过 512 个字符，标记值不能超过 256 个字符。 对于存储帐户，标记名称不能超过 128 个字符，标记值不能超过 256 个字符。
* 应用于资源组的标记不会被该资源组中的资源继承。
* 不能将标记应用到云服务等经典资源。
* 不支持这些字符：`<`、`>`、`%`、`&`、`\\`、`?`、`/`
