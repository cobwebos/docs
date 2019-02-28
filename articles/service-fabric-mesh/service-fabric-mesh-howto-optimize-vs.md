---
title: 针对 Azure Service Fabric 网格项目优化 Visual Studio 性能 | Microsoft Docs
description: 针对 Azure Service Fabric 网格应用优化 Visual Studio 性能
services: service-fabric-mesh
keywords: 优化调试性能
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: c7c9f9e72ae7ec2807c8fea08a8cc8d3e8a9e382
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804801"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>针对 Service Fabric 网格项目优化 Visual Studio 性能

本文展示了如何针对 Service Fabric 网格项目优化 Visual Studio 性能以便首次调试运行 (F5) 更为快速。  

## <a name="change-visual-studio-settings"></a>更改 Visual Studio 设置
 
在 Visual Studio 中，在“工具” > “选项”  > “Service Fabric 网格工具” > “常规”下，可以调整以下设置：

- “在项目打开时拉取所需的 Docker 映像”可以通过在项目加载时启动映像下载过程使首次调试运行 (F5) 更为快速。  
- “在项目打开时部署应用程序”可以通过在项目打开后启动部署过程使首次调试运行 (F5) 更为快速。  
- “在项目关闭时删除应用程序”通过在项目关闭时删除网格应用回收分配给该应用的资源（CPU、RAM）。  

当你在 Service Fabric 工具输出窗口中看到有消息指出 Visual Studio“正在拉取映像”、“正在预热”或“正在删除应用程序”时，说明它引用了上述设置。 可以关闭这些设置。

## <a name="next-steps"></a>后续步骤

通读[调试网格应用教程](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)