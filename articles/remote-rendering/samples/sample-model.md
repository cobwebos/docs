---
title: 示例模型
description: 列出示例模型的源。
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 8e5dcb6c9dfa08efc0889fcab779d6cb333d2330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88507529"
---
# <a name="sample-models"></a>示例模型

本文列出了一些可用于测试 Azure 远程渲染服务的示例数据资源。

## <a name="built-in-sample-model"></a>内置示例模型

我们提供了一个内置的示例模型，该模型始终可以通过使用 URL **builtin://Engine** 进行加载

![示例模型](./media/sample-model.png "示例模型")

模型统计信息：

| 名称 | 值 |
|-----------|:-----------|
| [所需的服务器大小](../how-tos/session-rest-api.md#create-a-session) | standard |
| 三角形数目 | 1 千 8 百万 |
| 可移动部件数 | 2073 |
| 材料数 | 94 |

## <a name="third-party-data"></a>第三方数据

Khronos Group 维护一组用于测试的 glTF 示例模型。 ARR 支持文本形式 ( *.gltf*) 和二进制形式 ( *.glb*) 的 glTF 格式。 建议使用 PBR 模型以获得最佳视觉效果：

* [glTF 示例模型](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Unity 来渲染模型](../quickstarts/render-model.md)
* [快速入门：转换用于渲染的模型](../quickstarts/convert-model.md)
