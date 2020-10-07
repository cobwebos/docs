---
title: 将 Unity 示例部署到 HoloLens
description: 介绍如何将 Unity 示例部署到 HoloLens 的快速入门
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 3eec935d0a25f9510cd9a2f6e00b7ac22756e697
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88796793"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>快速入门：将 Unity 示例部署到 HoloLens

本快速入门介绍如何在 HoloLens 2 中部署和运行适用于 Unity 的快速入门示例应用。

本快速入门将介绍以下操作：

> [!div class="checklist"]
>
>* 生成适用于 HoloLens 的快速入门示例应用
>* 将示例部署到设备
>* 在设备上运行示例

## <a name="prerequisites"></a>先决条件

在本快速入门中，我们将部署以下文章中生成的示例项目：[快速入门：使用 Unity 渲染模型](render-model.md)。

请确保在场景中正确保存了凭据，并且可以从 Unity 编辑器内部连接到某个会话。

## <a name="build-the-sample-project"></a>生成示例项目

1. 打开“文件”>“生成设置”。
1. 将“平台”更改为“通用 Windows 平台”
1. 将“目标设备”设置为“HoloLens”
1. 将“体系结构”设置为“ARM64”
1. 将“生成类型”设置为“D3D 项目”\
    ![生成设置](./media/unity-build-settings.png)
1. 选择“切换到平台”
1. 按下“生成”（或“生成并运行”）时，系统会要求你选择应在其中存储解决方案的某个文件夹
1. 使用 Visual Studio 打开生成的 Quickstart.sln
1. 将配置更改为“发布”和“ARM64” 
1. 将调试模式切换为“远程计算机”\
    ![解决方案配置](media/unity-deploy-config.png)
1. 生成解决方案
1. 在项目的“快速启动”页中，转到“属性”>“调试”
    1. 确保“发布”配置处于活动状态
    1. 将“要启动的调试器”设置为“远程计算机”
    1. 将“计算机名”更改为“HoleLens 的 IP”

## <a name="launch-the-sample-project"></a>启动示例项目

1. 使用 USB 数据线将 HoloLens 连接到电脑。
1. 在 Visual Studio 中启动调试器 (F5)。 此时会自动将该应用部署到设备。

示例应用会启动，然后启动新会话。 片刻之后，该会话将准备就绪，而远程渲染的模型将出现在你的面前。
如果以后想要再次启动该示例，也可以在 HoloLens 开始菜单中找到它。

## <a name="next-steps"></a>后续步骤

在下一篇快速入门中，我们将了解如何转换自定义模型。

> [!div class="nextstepaction"]
> [快速入门：转换要渲染的模型](convert-model.md)
