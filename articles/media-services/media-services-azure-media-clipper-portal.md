---
title: "在门户中使用 Azure 媒体剪辑器 | Microsoft Docs"
description: "通过 Azure 门户使用 Azure 媒体剪辑器来创建剪辑"
services: media-services
keywords: "剪辑;子剪辑;编码;媒体"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: faaae7edbc2fb62ae219dd963f405e7246c982d4
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>在门户使用 Azure 媒体剪辑器创建剪辑
可以在门户中使用 Azure 媒体剪辑器通过媒体服务帐户中的资产来创建剪辑。 要开始，请在门户中导航到媒体服务帐户。 接下来，选择“子剪辑”选项卡。

在“子剪辑”选项卡上，即可以开始创作剪辑。 在门户中，剪辑器加载使用有效流式处理定位符发布的单比特率 MP4、多比特率 MP4 和实时存档。 不加载未发布的资产。

## <a name="producing-clips"></a>生成剪辑
若要创建剪辑，请将资产拖放至剪辑界面。 如果已知标记时间，可以将这些资产手动输入界面。 否则，需要播放资产或拖动播放指针，找到所需的标记切入和标记切出时间。 如未提供标记切入和标记切出时间，则剪辑从输入资产的开头开始，或者继续直至输入资产的结尾。

若要使用帧准确度/GOP 准确度进行导航，请使用“帧快进”/“GOP 快进”或“帧后退”/“GOP 后退”按钮。 要针对多个资产进行剪辑，请将多个资产从资产选择面板拖放到剪辑界面。 可以选择界面中的资产并根据所需顺序将其重新排序。 资产选择面板提供每个资产的资产持续时间、类型和分辨率元数据。 当多个资产连在一起时，请考虑每个输入文件的源分辨率。 如果源分辨率不同，则会将较低的分辨率输入升级到满足最高分辨率资产的分辨率。 若要预览剪辑作业的输出，请选择预览按钮，将从选定的标记时间播放剪辑。

## <a name="producing-dynamic-manifest-filters"></a>生成动态清单筛选器
[动态清单筛选器](https://azure.microsoft.com/blog/dynamic-manifest/)介绍了一组基于清单属性和资产时间线的规则。 这些规则确定流式处理终结点如何操作输出播放列表（清单）。 该筛选器可用于更改要进行流式传输以供播放的段。 剪辑器生成的筛选器为本地筛选器，且特定于源资产。 不同于呈现的剪辑，筛选器不是新资产且不需要生成编码作业。 可以通过 [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) 或 [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest) 快速创建筛选器，但这些只有 GOP 准确度。 通常，编码为流式处理的资产具有的 GOP 大小为两秒。

若要创建动态清单筛选器，请从“高级设置”菜单中选择动态清单筛选器作为剪辑模式。 可以按照相同的过程生成剪辑，创建筛选器。 筛选器只能针对单个资产生成。

## <a name="submitting-clipping-jobs"></a>提交剪辑作业
完成创作剪辑后，选择“提交作业”按钮，启动相应的剪辑作业或动态清单调用。