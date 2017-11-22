---
title: "使用 Azure 媒体剪辑器创建剪辑 | Microsoft Docs"
description: "概述 Azure 媒体剪辑器 - 基于资产生成媒体剪辑的工具"
services: media-services
keywords: "剪辑;子剪辑;编码;媒体"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a5bb66db889dc1c52252773e3f129ba2c3d55f1
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>使用 Azure 媒体剪辑器创建剪辑
Azure 媒体剪辑器是免费的 JavaScript 库，能让 Web 开发人员为其用户提供创建媒体剪辑的界面。 此工具可以集成到任何 Web 页面，并提供用于加载资产和提交剪辑作业的 API。

Azure 媒体剪辑器能够执行：
- 从实时存档剪裁前盖板和后盖板 
- 编辑 AMS 实时事件、实时存档或 fMP4 VOD 文件中的视频重点 
- 将多个源的视频相连 
- 从 AMS 媒体资产中生成摘要剪辑 
- 剪辑具有帧准确度的视频 
- 基于具有帧组 (GOP) 准确度的现有实时和 VOD 资产生成动态清单筛选器 
- 根据媒体服务帐户中的资产生成编码作业

若要请求新功能，请将意见或反馈提交至 [Azure 媒体服务 UserVoice](http://aka.ms/amsvoice/)。 如有具体问题、疑问或发现任何 bug，请通过 amcinfo@microsoft.com 与媒体服务团队联系。

## <a name="release-notes"></a>发行说明
以下列表介绍剪辑器最新版本的各种已知问题以及更改日志：
- [已知问题列表](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [更改日志](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>浏览器支持
Azure 媒体剪辑器使用现代 HTML5 技术构建并支持以下浏览器：

- Microsoft Edge 13+
- Internet Explorer 11+
- Chrome 54+
- Safari 10+
- Firefox 50+

> [!NOTE]
> 当前仅支持 Azure 媒体服务的 HTML5 播放流。

## <a name="language-support"></a>语言支持
剪辑器小组件支持以下 18 种语言：
- 中文(简体)
- 中文(繁体)
- 捷克语
- 荷兰语；佛兰德语
- 英语
- 法语
- 德语
- 匈牙利语
- 意大利语
- 日语
- 朝鲜语
- 波兰语
- 葡萄牙语(巴西)
- 葡萄牙语(葡萄牙)
- 俄语
- 西班牙语
- 瑞典语
- 土耳其语

## <a name="next-steps"></a>后续步骤
若要开始使用 Azure 媒体剪辑器，请阅读[入门](media-services-azure-media-clipper-getting-started.md)文章，了解有关如何部署小组件的详细信息。