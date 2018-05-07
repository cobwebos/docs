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
ms.openlocfilehash: f3822386d0d16b1feaf16853424329558a18f910
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
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

下图阐释剪辑器接口：![Azure 媒体剪辑器](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>发行说明
请参阅下表获取最新版剪辑器的博客文章、各种已知问题和更改日志：
- [博客文章](https://azure.microsoft.com/blog/azure-media-clipper/)
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
