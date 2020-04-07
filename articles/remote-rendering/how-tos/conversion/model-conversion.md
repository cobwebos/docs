---
title: 模型转换
description: 描述转换模型进行渲染的过程
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 135c58a665779ffaad8750ffe618bdbe38639b66
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681488"
---
# <a name="convert-models"></a>转换模型

Azure 远程呈现允许您呈现非常复杂的模型。 为了实现最大的性能，必须预处理数据才能以最佳格式进行处理。 根据数据量，此步骤可能需要一段时间。 如果此时间在模型加载期间花费，则不切实际。 此外，在多个会话中重复此过程也是浪费。 由于这些原因，ARR 服务提供专用*的转换服务*，您可以提前运行。
转换后，可以从 Azure 存储帐户加载模型。

## <a name="supported-source-formats"></a>支持的源格式

转换服务支持以下格式：

- **FBX** （2011 年版及以上）
- **GLTF（** 版本 2.x）
- **GLB（** 版本 2.x）

格式在材料属性转换方面存在细微差异，如[模型格式的章节材料映射](../../reference/material-mapping.md)中列出的。

## <a name="the-conversion-process"></a>转换过程

1. [准备两个 Azure Blob 存储容器](blob-storage.md)：一个用于输入，一个用于输出
1. 将模型上载到输入容器（可选在子路径下）
1. 通过[模型转换 REST API](conversion-rest-api.md)触发转换过程
1. 轮询服务以进行转换进度
1. 完成后，加载模型
    - 从链接的存储帐户（请参阅[创建帐户](../create-an-account.md#link-storage-accounts)以链接存储帐户的"链接存储帐户"步骤）
    - 或通过提供*共享访问签名 （SAS）*。

所有模型数据（输入和输出）都存储在用户提供的 Azure Blob 存储中。 Azure 远程呈现使您能够完全控制资产管理。

## <a name="conversion-parameters"></a>转换参数

有关各种转换选项，请参阅[本章](configure-model-conversion.md)。

## <a name="examples"></a>示例

- [快速入门：转换渲染模型](../../quickstarts/convert-model.md)是如何转换模型的分步介绍。
- [示例 PowerShell 脚本](../../samples/powershell-example-scripts.md)（演示转换服务的使用情况）可以在*Scripts*文件夹中的[ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)中找到。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Blob 存储进行模型转换](blob-storage.md)
- [模型转换 REST API](conversion-rest-api.md)
- [配置模型转换](configure-model-conversion.md)
- [模型格式的材料映射](../../reference/material-mapping.md)
