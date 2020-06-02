---
title: 使用 Unity 渲染模型
description: 此快速入门引导用户完成渲染模型的步骤
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: be5bc8ec4e8f363d9b6079d22f3bba1af2d4d5fb
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682568"
---
# <a name="quickstart-render-a-model-with-unity"></a>快速入门：使用 Unity 渲染模型

本快速入门介绍如何运行一个 Unity 示例，该示例使用 Azure 远程渲染 (ARR) 服务来远程渲染内置模型。

其中没有详细介绍 ARR API 本身，也没有说明如何设置新的 Unity 项目。 有关这些主题，请参阅[教程：从头开始设置 Unity 项目](../tutorials/unity/project-setup.md)。

本快速入门将介绍以下操作：
> [!div class="checklist"]
>
>* 设置本地开发环境
>* 获取和构建适用于 Unity 的 ARR 快速入门示例应用
>* 在 ARR 快速入门示例应用中渲染模型

## <a name="prerequisites"></a>先决条件

若要获取对 Azure 远程渲染服务的访问权限，首先需要[创建一个帐户](../how-tos/create-an-account.md)。

必须安装以下软件：

* Windows SDK 10.0.18362.0[（下载）](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Visual Studio 2019 最新版本[（下载）](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT[（下载）](https://git-scm.com/downloads)。
* Unity 2019.3.1[（下载）](https://unity3d.com/get-unity/download)
  * 在 Unity 中安装以下模块：
    * **UWP** - 通用 Windows 平台生成支持
    * **IL2CPP** - Windows 生成支持 (IL2CPP)

## <a name="clone-the-sample-app"></a>克隆示例应用

打开命令提示符（在 Windows 开始菜单中键入 `cmd`），并切换到要在其中存储 ARR 示例项目的目录。

运行以下命令：

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

最后一个命令会在 ARR 目录中创建一个子目录，其中包含 Azure 远程渲染的各个示例项目。

可在子目录 Unity/Quickstart 中找到 Unity 的快速入门示例应用。

## <a name="rendering-a-model-with-the-unity-sample-project"></a>使用 Unity 示例项目渲染模型

打开 Unity Hub，添加位于“ARR\azure-remote-rendering\Unity\Quickstart”文件夹中的示例项目。
打开项目。 如果需要，请允许 Unity 将该项目升级到已安装的版本。

要渲染的默认模型是一个[内置示例模型](../samples/sample-model.md)。 [下一篇快速入门](convert-model.md)将介绍如何使用 ARR 转换服务来转换自定义模型。

### <a name="enter-your-account-info"></a>输入帐户信息

1. 在 Unity 资产浏览器中，导航到“场景”文件夹并打开“快速入门”场景。
1. 从“层次结构”中选择“RemoteRendering”游戏对象。
1. 在“检查器”中输入你的[帐户凭据](../how-tos/create-an-account.md)。 如果还没有帐户，请[创建一个](../how-tos/create-an-account.md)。

![ARR 帐户信息](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Azure 门户只会将你的帐户的域显示为 mixedreality.azure.com。 这不足以成功建立连接。
> 请将 AccountDomain 设置为 `<region>.mixedreality.azure.com`，其中，`<region>` 是[你附近的可用区域之一](../reference/regions.md)。

稍后我们需要将此项目部署到 HoloLens，并从该设备连接到远程渲染服务。 由于没有便捷的方法在设备上输入凭据，因此快速入门示例会将凭据保存在 Unity 场景中。

> [!WARNING]
> 务必不要将含有所保存凭据的项目签入到会泄露机密登录信息的某个存储库！

### <a name="create-a-session-and-view-the-default-model"></a>创建会话并查看默认模型

按下 Unity 的“播放”按钮以启动会话。 在“游戏”面板中的视区底部，应会看到一个包含状态文本的叠加层。 该会话将经历一系列状态转换。 处于“正在启动”状态时，表示远程 VM 已运转，启动过程需要几分钟时间。 成功后，会话将转换为“就绪”状态。 现在，会话已进入“正在连接”状态，此时它会尝试访问该 VM 上的渲染运行时。 成功后，示例将转换为“已连接”状态。 此时，它会开始下载要渲染的模型。 由于模型较大，下载过程可能需要额外的几分钟时间。 然后，远程渲染的模型将会显示。

![示例的输出](media/arr-sample-output.png)

祝贺你！ 现在你已看到了一个远程渲染的模型！

## <a name="inspecting-the-scene"></a>检查场景

远程渲染连接开始运行后，“检查器”面板将会更新并显示其他状态信息：

![Unity 示例播放](./media/arr-sample-configure-session-running.png)

现在，可以通过选择新节点并单击“检查器”中的“显示子级”来浏览场景图。

![Unity 层次结构](./media/unity-hierarchy.png)

场景中有一个[裁切平面](../overview/features/cut-planes.md)对象。 请尝试在该对象的属性中将其启用，然后将其四处移动：

![更改裁切平面](media/arr-sample-unity-cutplane.png)

若要同步变换，请单击“立即同步”或选中“同步每个帧”选项。  对于组件属性，只需更改它们就已足够。

## <a name="next-steps"></a>后续步骤

在下一篇快速入门中，我们会将该示例部署到 HoloLens，以查看原始大小的远程渲染的模型。

> [!div class="nextstepaction"]
> [快速入门：将 Unity 示例部署到 HoloLens](deploy-to-hololens.md)

另外，还可以将该示例部署到台式电脑。

> [!div class="nextstepaction"]
> [快速入门：将 Unity 示例部署到台式机](deploy-to-desktop.md)