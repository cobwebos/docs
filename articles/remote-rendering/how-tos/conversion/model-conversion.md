---
title: 模型转换
description: 描述转换用于呈现的模型的过程
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e899b249261ea3238695a2e2be6001cb6a9bc763
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318052"
---
# <a name="convert-models"></a>转换模型

Azure 远程呈现允许您呈现非常复杂的模型。 若要获得最大性能，必须将数据预处理为最佳格式。 此步骤可能需要一些时间，具体取决于数据量。 如果在模型加载过程中花费了此时间，则会不切实际。 而且，为多个会话重复此过程会浪费时间。 由于这些原因，ARR 服务提供了一个专用的 *转换服务*，您可以提前运行该服务。
转换后，可以从 Azure 存储帐户中加载模型。

## <a name="supported-source-formats"></a>支持的源格式

转换服务支持以下格式：

- **FBX**  (版本2011到版本 2020) 
- **GLTF** /**GLB** (版本 2.x) 

与材料属性转换有关的格式之间存在细微差异，如 [模型格式的章节材料映射](../../reference/material-mapping.md)中所述。

## <a name="the-conversion-process"></a>转换过程

1. [准备两个 Azure Blob 存储容器](blob-storage.md)：一个用于输入，一个用于输出
1. 将模型上传到输入容器 (可选的子路径下) 
1. 通过[模型转换](conversion-rest-api.md)触发转换过程 REST API
1. 轮询服务以查找转换进度
1. 完成后，加载模型
    - 从链接的存储帐户 (参阅 [创建帐户](../create-an-account.md#link-storage-accounts) 以链接存储帐户中的 "链接存储帐户" 步骤) 
    - 或者通过提供 *共享访问签名 (SAS) *。

所有模型数据 (输入和输出) 存储在用户提供的 Azure blob 存储中。 Azure 远程呈现使你可以完全控制资产管理。

## <a name="pricing"></a>定价

有关转换定价的信息，请参阅 [远程呈现定价](https://azure.microsoft.com/pricing/details/remote-rendering) 页。


## <a name="conversion-parameters"></a>转换参数

有关各种转换选项，请参阅 [此章节](configure-model-conversion.md)。

## <a name="examples"></a>示例

- [快速入门：转换用于呈现的模型](../../quickstarts/convert-model.md) 是分步介绍如何转换模型的分步说明。
- 演示如何使用转换服务的[示例 PowerShell 脚本](../../samples/powershell-example-scripts.md)可以在 "*脚本*" 文件夹中的 " [ARR 示例" 存储库](https://github.com/Azure/azure-remote-rendering)中找到。

## <a name="next-steps"></a>后续步骤

- [将 Azure Blob 存储用于模型转换](blob-storage.md)
- [模型转换 REST API](conversion-rest-api.md)
- [配置模型转换](configure-model-conversion.md)
- [为转换设计文件布局](layout-files-for-conversion.md)
- [模型格式的材料映射](../../reference/material-mapping.md)
