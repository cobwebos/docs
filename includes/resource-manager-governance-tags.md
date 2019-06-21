---
title: include 文件
description: include 文件
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 06/18/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 5358fe387d2a371d96d46d8546ce0f20b47ca54b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206495"
---
可以将标记应用于 Azure 资源，从而将元数据按逻辑组织到分类中。 每个标记由名称和值对组成。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

应用标记以后，即可使用该标记名称和值检索订阅中的所有资源。 使用标记可以从不同资源组中检索相关资源。 需要为计费或管理目的组织资源时，此方法十分有用。

除了自动标记策略之外，你的分类还应考虑自助式元数据标记策略，以减轻用户负担并提高准确性。

以下限制适用于标记：

* 并非所有资源类型都支持标记。 若要确定是否可以将标记应用到资源类型，请参阅 [Azure 资源的标记支持](../articles/azure-resource-manager/tag-support.md)。
* 每个资源或资源组最多可以有 15 个标记名称值对。 此限制仅适用于直接应用到资源组或资源的标记。 资源组可以包含多个资源，这些资源每个都有 15 个标记名称值对。 如果有超过 15 个需要与资源关联的值，请将 JSON 字符串用于标记值。 JSON 字符串可以包含多个应用于单个标记名称的值。 本文介绍了一个将 JSON 字符串分配给标记的示例。
* 标记名称不能超过 512 个字符，标记值不能超过 256 个字符。 对于存储帐户，标记名称不能超过 128 个字符，标记值不能超过 256 个字符。
* 对于所有标记名称和值，虚拟机和虚拟机规模集限制总共为 2048 个字符。 通用化的 Vm 不支持标记。
* 应用于资源组的标记不会被该资源组中的资源继承。
* 不能将标记应用到云服务等经典资源。
* 标记名称不能包含以下字符：`<`、`>`、`%`、`&`、`\`、`?`、`/`
