---
title: 将 Unity 示例部署到桌面
description: 介绍如何将 Unity 示例部署到台式电脑的快速入门
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: dc566d2702fb24ef551e4900bed7b828d2597383
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2020
ms.locfileid: "85557066"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>快速入门：将 Unity 示例部署到桌面

本快速入门介绍如何在台式电脑上部署和运行适用于 Unity 的快速入门示例应用。

本快速入门将介绍以下操作：

> [!div class="checklist"]
>
>* 生成适用于台式机的快速入门示例应用
>* 将示例部署到电脑
>* 在电脑上运行示例

## <a name="prerequisites"></a>先决条件

在本快速入门中，我们将部署以下文章中生成的示例项目：[快速入门：使用 Unity 渲染模型](render-model.md)。

请确保在场景中正确保存了凭据，并且可以从 Unity 编辑器内部连接到某个会话。

## <a name="disable-virtual-reality-support"></a>禁用虚拟现实支持

台式机目前仅支持平面桌面应用，因此必须禁用 VR 支持。

1. 打开“编辑”>“项目设置...”
1. 在左侧选择“播放器”。
1. 选择“通用 Windows 平台设置”选项卡。
1. 展开“XR 设置”。
1. 禁用“支持虚拟现实”。
    ![播放器设置](./media/unity-disable-xr.png)
1. 在“XR 设置”上方，展开“发布设置”。
1. 在“支持的设备系列”中，确保已选中“台式机”。 

## <a name="build-the-sample-project"></a>生成示例项目

1. 打开“文件”>“生成设置”。
1. 将“平台”更改为“通用 Windows 平台”（也支持“电脑独立”，但此处不使用该平台，请参阅[平台限制](../reference/limits.md#platform-limitations)） 。
1. 将“目标设备”设置为“电脑”。
1. 将“体系结构”设置为“x86”。
1. 将“生成类型”设置为“D3D 项目”。
  ![生成设置](./media/unity-build-settings-pc.png)
1. 选择“切换到平台”。
1. 按下“生成”（或“生成并运行”）时，系统会要求你选择应在其中存储解决方案的某个文件夹。
1. 使用 Visual Studio 打开生成的 Quickstart.sln。
1. 将配置更改为“发布”和“x86”。 
1. 将调试模式切换为“本地计算机”。
  ![解决方案配置](./media/unity-deploy-config-pc.png)
1. 生成解决方案 (F7)。

> [!WARNING]
> 请确保选择“x86”。 当前不支持“UWP/x64”，请参阅[平台限制](../reference/limits.md#platform-limitations)。

## <a name="launch-the-sample-project"></a>启动示例项目

在 Visual Studio 中启动调试器 (F5)。 此时会自动将该应用部署到电脑。

示例应用会启动，然后启动新会话。 片刻之后，该会话将准备就绪，而远程渲染的模型将出现在你的面前。
如果以后想要再次启动该示例，也可以在“开始”菜单中找到它。

## <a name="next-steps"></a>后续步骤

在下一篇快速入门中，我们将了解如何转换自定义模型。

> [!div class="nextstepaction"]
> [快速入门：转换要渲染的模型](convert-model.md)
