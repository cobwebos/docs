---
title: 为转换设计文件布局
description: 建议如何最好地将文件放入输入容器。
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 13c4b841fe2d0b62472628d9382b5f6ee3d1fa6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318069"
---
# <a name="laying-out-files-for-conversion"></a>为转换设计文件布局

为了正确地处理资产，转换服务需要能够找到所有输入文件。
这包括正在转换的主要资产文件，以及通常由资产文件中的路径引用的其他一些文件。
为转换资产的请求提供了两个参数，用于确定转换服务查找这些文件的方式： `input.folderPath` (是可选的) 和 `input.inputAssetPath` 。
它们完全记录在 " [转换 REST API](conversion-rest-api.md) " 页中。
出于对文件进行布局的目的，要注意的重要事项是， `folderPath` 确定在处理资产时可用于转换服务的完整文件集。

## <a name="placing-files-so-they-can-be-found"></a>放置文件以便可以找到它们

当源资产使用外部文件时，这些文件的路径将存储在资产中。
转换服务必须在不同于资产的原始文件系统的文件系统中解释这些路径。
如果路径存储为相对路径，并且源资产与其引用的文件之间的相对位置不变，则转换服务可以轻松找到所引用的文件。

> [!Note]
> 建议将文件放入输入容器，使文件的相对位置与创建资产时的位置相同。

> [!Note]
> 更倾向于创建携带相对路径的资产。
> 有关 [设置3Ds 最大资料](../../tutorials/modeling/3dsmax-material-setup.md) 的教程提供了有关如何确保资产使用相对路径的最大3ds 示例。

## <a name="finding-textures"></a>查找纹理

由于有许多方法可以生成资产，转换服务必须是灵活的。
特别是，它必须处理资产中的路径和纹理位置不完全匹配的情况。
例如，如果生成的资产包含绝对路径，则这些路径将永远不会与转换服务使用的文件系统匹配。
若要处理这种情况，我们使用最大努力方法查找纹理。

用于查找纹理的算法如下所示：给定一个存储在资产中的路径，查找最长的子路径后缀，该后缀在用作源资产位置的相对路径时，针对存在的文件。
如果没有此类子路径 (包含整个路径) 以文件为目标，则将纹理视为缺失。

请考虑以下精心设计文件系统： 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
如果 myAsset 使用相对路径引用纹理 `..\Textures\MyAssetTextures\myTexture.png` ，则转换服务将使用 File E。事实上，它可以使用 A、C 和 E 这样的文件（如果存在），因为它是使用最长的后缀，所以需要使用文件 E。
永远不会使用文件 B 和 D，因为不 `Textures\myTexture.png` 是存储路径的任何后缀的组成部分。
如果资产而不是包含路径 `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` 或 `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` ，则转换服务将能够查找文件 A 和 C （如果它们存在） (首选 c 上的) 。 但是，无法通过这种方式找到 E，必须重新定位该文件。
可以通过将纹理文件夹移动到资产旁边来解决此情况。

> [!Note]
> 如果找不到纹理，则可能的补救措施是确保资产是包含纹理的部分子树的同级。

## <a name="next-steps"></a>后续步骤

- [模型转换](model-conversion.md)
