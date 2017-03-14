---
title: "Microsoft Azure 上的应用程序体系结构 | Microsoft Docs"
description: "包括常见设计模式的体系结构概述"
services: 
documentationcenter: 
author: rboucher
manager: carmonm
editor: 
ms.assetid: 3a37bbc0-f624-46f3-bc4e-5a10560f9fbf
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: b463c363f131c3d75bbab229fe9f14495b5f3b95
ms.openlocfilehash: d2a13d5c80513a6fe80979bb97368b269a24a883
ms.lasthandoff: 03/01/2017


---
# <a name="application-architecture-on-microsoft-azure"></a>Microsoft Azure 上的应用程序体系结构
本文列出了用于构建使用 Microsoft Azure 的应用程序的资源。 这包括一些工具，用于帮助你以直观方式描绘和描述软件系统。

## <a name="design-patterns-poster"></a>设计模式海报
Microsoft 模式与实践部门已发布[云设计模式](http://msdn.microsoft.com/library/dn568099.aspx)一书，可在 MSDN 上获取，也可下载 PDF 格式的版本。 此外还提供了一张可用的大画幅海报，以直观方式列出了所有模式。

![模式与实践：云模式海报](./media/architecture-overview/PnPPatternPosterThumb.jpg)


## <a name="drawing-symbol-and-icon-sets"></a>绘制符号和图标集
[观看 Visio 和符号培训视频](http://aka.ms/CnESymbolsVideo)，然后[下载云和企业符号集](http://aka.ms/CnESymbols)以帮助创建介绍 Azure、Windows Server 和 SQL Server 等的技术资料。 你可以在以下各项（免费或用于盈利）中使用这些符号。  
- 体系结构示意图 
- 培训材料 
- 演示文稿 
- 数据表 
- 信息图 
- 白皮书 
- 第三方书籍，如果书籍用于教授人们使用 Microsoft 产品。

这些符号并未计划在用户界面中使用，但你可以请求权限。  Azure 的符号可以使用。 也就是说，你可以采用与在 Azure 门户中相同的方式使用它们来表示相同的对象。  例如，Azure 服务总线的符号仅应当用来表示和访问 Azure 服务总线对象。 第三方符号不归 Microsoft 拥有。 可以访问那些公司的网站来了解其图标的使用规则。

CnE 符号采用 Visio、SVG 和 PNG 格式。 符号集中包括了有关如何在 PowerPoint 中轻松使用符号的其他说明。 符号集按季度发布，一旦新服务发布就会更新。

[Microsoft Office Visio stencil](http://www.microsoft.com/en-us/download/details.aspx?id=35772) 中介绍了 Microsoft Office 的其他符号和相关技术，但并没有针对体系结构图像 CnE 集那样进行优化。   

有关具体的问题和反馈，请通过 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) 向我们发送电子邮件。 我们想知道您的想法，包括积极的反馈，以便我们知道可以继续对其投入时间。

![云和企业符号/图标集](./media/architecture-overview/CnESymbols.png)

## <a name="microsoft-architecture-certification-course"></a>Microsoft 体系结构认证课程
Microsoft 在 2015 年 8 月创建了支持 Microsoft 认证考试 70-534 的体系结构课程。 在 [EDX.ORG 上免费提供](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x)。  它使用 [3D Blueprint Visio Template](#3d-blueprint-visio-template)。

![Microsoft 体系结构认证课程](./media/architecture-overview/EDXCourse.png)

## <a name="microsoft-solutions"></a>Microsoft 解决方案
Microsoft 发布了一套高级别的[解决方案体系结构](http://aka.ms/azblueprints)，介绍如何使用 Microsoft 产品构建特定类型的系统。

之前，Microsoft 发布了一组展示示例体系结构的等轴 3D 蓝图。 那些蓝图已被解决方案体系结构替换。 蓝图链接已重定向为指向解决方案。 如果出于某种原因，需访问之前的蓝图材料，请向 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) 发送电子邮件来说明你的请求。   

蓝图和解决方案体系结构图都会使用部分[云和企业符号集](http://aka.ms/CnESymbols)。   

![Microsoft 体系结构蓝图 3D 图](./media/architecture-overview/BluePrintThumb.jpg)

## <a name="3d-blueprint-visio-template"></a>3D Blueprint Visio Template
3D 版 [Microsoft 体系结构蓝图](http://aka.ms/azblueprints)最初在非 Microsoft 工具中创建，现在已失效。 Visio 2013（和更高版本）模板于 2015 年 8 月 5 日发布，作为 [Microsoft 体系结构认证课程的一部分在 EDX.ORG 上分发](#microsoft-architecture-certification-course)。

该模板在本课程外也可用。

* 首先[观看视频培训](http://aka.ms/3dBlueprintTemplateVideo)了解其功能   
* 下载 [Microsoft 3d Blueprint Visio 模板](http://aka.ms/3DBlueprintTemplate)
* 下载与 3D 模板配合使用的[云和企业符号集](https://www.microsoft.com/en-us/download/details.aspx?id=41937)。 

对于提培训材料无法答复的具体问题或向我们提供反馈，请发邮件至 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com)。 该模板不再处于积极开发阶段。 但是，它仍然有用且相关，因为它可以使用任何 PNG 或更新的[云和企业符号集](http://aka.ms/CnESymbols)。  

![Microsoft 3D Blueprint Visio Template](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)

## <a name="architecture-infographics"></a>体系结构信息图
Microsoft 发布了几个与体系结构相关的海报/信息图。 它们包括[构建实际云应用程序](https://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/)和[使用云服务进行缩放](https://azure.microsoft.com/documentation/infographics/cloud-services/)。

![Azure 体系结构信息图](./media/architecture-overview/AzureArchInfographicThumb.jpg)

