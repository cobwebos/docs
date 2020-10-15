---
title: 接口和自定义模型
description: 添加视图控制器并引入要由 Azure 远程渲染呈现的自定义模型
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: ebadaf51a7dfbb286dac0bbdb0c3c8437ae2356f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022218"
---
# <a name="tutorial-interfaces-and-custom-models"></a>教程：接口和自定义模型

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 将混合现实工具包添加到项目
> * 管理模型状态
> * 为 Azure Blob 存储配置模型转换
> * 上传和处理模型以实现渲染

## <a name="prerequisites"></a>先决条件

* 本教程以[教程：查看远程渲染的模型](../view-remote-models/view-remote-models.md)为基础。

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>混合现实工具包 (MRTK) 入门

混合现实工具包 (MRTK) 是用于构建混合现实体验的跨平台工具包。 我们将使用 MRTK 2.3 实现其交互和可视化功能。

若要添加 MRTK，请按照 [MRTK 入门](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html)中列出的[所需步骤](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#required)进行操作。

这些步骤如下：
 - [获取最新的 MRTK Unity 包](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#get-the-latest-mrtk-unity-packages)
     - 即使显示它是“最新”版本，但本教程适用于版本 2.3。
     - 在本教程中，我们只使用 Foundation 包。 不需要 Extensions、Tools 和 Examples 包  。
 - [将 MRTK 包导入 Unity 项目](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#import-mrtk-packages-into-your-unity-project)
 - [将 Unity 项目切换到目标平台](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#switch-your-unity-project-to-the-target-platform)
     - 你应在第一章中已执行过此步骤，但现在正适合再次检验学习成果。
 - [将 MRTK 添加到新场景或新项目中](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#add-mrtk-to-a-new-scene-or-new-project)
     - 可以将 MRTK 添加到新场景并重新添加协调器和模型对象/脚本，也可以使用“混合现实工具包 -> 添加到场景并配置”菜单命令将 MRTK 添加到现有场景。

## <a name="import-assets-used-by-this-tutorial"></a>导入本教程要使用的资产

从本章开始，我们将为涉及到的大部分材料实现一种简单的[“模型-视图-控制器”模式](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)。 该模式的“模型”部分是特定于 Azure 远程渲染的代码以及与 Azure 远程渲染相关的状态管理。 该模式的“视图”和“控制器”部分是使用 MRTK 资产和某些自定义脚本实现的 。 在本教程中，可以只使用模型，而无需实现“视图-控制器” 。 通过这种分离，你可以将本教程中的代码轻松集成到自己的应用程序中，在那里，代码将接管设计模式中的“视图-控制器”部分。

通过引入 MRTK，现在可以将许多脚本、预制项和资产添加到项目中，用于支持交互和视觉反馈。 这些“教程资产”会捆绑到 [Azure 远程渲染 GitHub](https://github.com/Azure/azure-remote-rendering) 中“\Unity\TutorialAssets\TutorialAssets.unitypackage”路径下的一个 [Unity 资产包中](https://docs.unity3d.com/Manual/AssetPackages.html)。

1. 如果下载操作会将 zip 提取到已知位置，请克隆或下载 git 存储库 [Azure 远程渲染](https://github.com/Azure/azure-remote-rendering)。
1. 在 Unity 项目中，选择“资产”->“导入包”->“自定义包”。
1. 在文件资源管理器中，导航到克隆或解压缩的 Azure 远程渲染存储库的目录，然后选择“Unity”->“TutorialAssets”->“TutorialAssets.unitypackage”中的 .unitypackage
1. 选择“导入”按钮，将包内容导入到项目中。
1. 在 Unity 编辑器中，从顶部菜单栏选择“混合现实工具包”->“实用工具”->“升级轻量级渲染管道的 MRTK 标准着色器”，并按照提示升级着色器。

在项目中包括 MRTK 和教程资产后，我们会将 MRTK 配置文件切换到一个更适合教程的配置文件。

1. 选择场景层次结构中的 MixedRealityToolkit GameObject。
1. 在检查器的 MixedRealityToolkit 组件下，将配置文件切换为 ARRMixedRealityToolkitConfigurationProfile。
1. 按 Ctrl+S 保存所做的更改。

随即将主要使用默认的 HoloLens 2 配置文件配置 MRTK。 提供的配置文件按以下方式预配置：
 - 关闭探查器（在设备上可按 9 打开/关闭，或说“显示/隐藏探查器”）。
 - 关闭“眼睛凝视”光标。
 - 启用 Unity 鼠标单击，这样可使用鼠标而不是模拟手势来单击 MRTK UI 元素。

## <a name="add-the-app-menu"></a>添加应用菜单

本教程中的大部分视图控制器操作是针对抽象基类而不是具体基类。 此模式提供了更大的灵活性，使我们能够为你提供视图控制器，同时还有助于你了解 Azure 远程渲染代码。 为简单起见，RemoteRenderingCoordinator 类未提供抽象类，并且其视图控制器直接针对具体类进行操作。

现在可以将预制项 AppMenu 添加到场景中来获取当前会话状态的视觉反馈了。 此视图控制器将在实现更多 ARR 功能并将其集成到场景中时“解锁”更多子菜单视图控制器。 现在，AppMenu 将以可视方式指示 ARR 状态，并显示用户用于授权应用程序连接 ARR 的模式面板。

1. 在 Assets/RemoteRenderingTutorial/Prefabs/AppMenu 中找到 AppMenu 预制项
1. 将 AppMenu 预制项拖到场景中。
1. 你可能会看到“TMP 导入工具”对话框，因为这是第一次在场景中加入“文本网格 Pro”资产。 按照“导入 TMP 基本要素”的提示操作。 然后关闭导入工具对话框，这里不需要示例和其他功能。
1. AppMenu 配置为自动挂钩，并提供“同意连接到会话”的模式，以便能够删除之前放置的旁路。 在 RemoteRenderingCoordinator GameObject 上，按“请求授权时”事件上的“-”按钮，删除先前实现的授权旁路 。
 ![删除旁路](./media/remove-bypass-event.png)。
1. 在 Unity 编辑器中按“播放”来测试视图控制器。
1. 在编辑器中，已配置了 MRTK，现在可以使用 WASD 键更改视图的位置，然后按住鼠标右键同时移动鼠标来更改视图方向。 尝试在各方向上稍微移动场景，感受控制的感觉。
1. 在设备上，可以通过举起手掌来召唤 AppMenu，在 Unity 编辑器中，可以使用热键“M”来实现此目的。
1. 如果看不到菜单，可按“M”键召唤菜单。 菜单将位于相机附近，便于交互。
1. 授权现在显示为 AppMenu右侧的请求，从现在开始，使用授权应用管理远程渲染会话。
 ![UI 授权](./media/authorize-request-ui.png)
1. 让 Unity 停止播放，以继续学习本教程。

## <a name="manage-model-state"></a>管理模型状态

现在，我们将实现一个新的脚本 RemoteRenderedModel，用于跟踪状态、响应事件、引发事件和进行配置。 实质上，RemoteRenderedModel 在 `modelPath` 中存储模型数据的远程路径。 它侦听 **RemoteRenderingCoordinator** 中的状态更改，以确认自己是否应自动加载或卸载定义的模型。 附加了 RemoteRenderedModel 的 GameObject 是远程内容的本地父级。

请注意，RemoteRenderedModel 脚本实现所包含的、教程资产中的 BaseRemoteRenderedModel  。 这使远程模型视图控制器能够与脚本绑定。

1. 在与 RemoteRenderingCoordinator 相同的文件夹中创建一个名为 RemoteRenderedModel 的新脚本 。 将整个内容替换为以下代码：

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

在最基本的条件下，RemoteRenderedModel 保存模型（在此例中为 SAS 或 builtin:// URI）加载所需的数据并跟踪远程模型状态。 在加载时，在 RemoteRenderingCoordinator 上调用 `LoadModel` 方法，并返回包含模型的实体以供引用和卸载。

## <a name="load-the-test-model"></a>加载测试模型

现在通过再次加载测试模型来测试新脚本。 我们将创建一个包含脚本的游戏对象，并将其作为测试模型的父级。

1. 在场景中创建新的空游戏对象并将其命名为 TestModel。
1. 将 RemoteRenderedModel 脚本添加到 TestModel。
![添加 RemoteRenderedModel 组件](./media/add-remote-rendered-model-script.png)
1. 分别用“TestModel”和“builtin://Engine”填写 `Model Display Name` 和 `Model Path` 。
![指定模型详细信息](./media/add-model-script.png)
1. 将 TestModel 对象放置在相机前面，位置为 x = 0, y = 0, z = 3 。
![位置对象](./media/test-model-position.png)
1. 确保启用 AutomaticallyLoad。
1. 在 Unity 编辑器中按“播放”来测试应用程序。
1. 单击“连接”按钮授予权限，允许应用创建会话，然后它会连接到会话并自动加载模型。

当应用程序通过其状态进行监视时，请监视控制台。 请记住，某些状态可能需要一些时间才能完成，并且不会显示进度。 最终，你会看到模型加载中的日志，然后场景中会呈现测试模型。

尝试在检查器或场景视图中通过转换功能移动和旋转 TestModel GameObject。 你会看到模型在游戏视图中移动和旋转。

![Unity 日志](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>预配 Azure 中的 Blob 存储和自定义模型引入

现在，我们可以尝试加载自己的模型。 要执行此操作，你需要配置 Blob 存储，并在 Azure 上上传和转换模型，然后我们会使用 RemoteRenderedModel 脚本加载模型。 如果此时你没有要加载的自己的模型，则可以安全地跳过自定义模型加载步骤。

按照 [快速入门：转换要渲染的模型](../../../quickstarts/convert-model.md)中指定的步骤进行操作。 对于本教程的目的，请跳过“将新模型插入快速入门示例应用”部分。 引入模型的共享访问签名 (SAS) URI 后，请继续执行下面的下一步。

## <a name="load-and-rendering-a-custom-model"></a>加载和渲染自定义模型

1. 在场景中创建新的空 GameObject，并将其命名为类似于自定义模型。
1. 将 RemoteRenderedModel 脚本添加到新创建的 GameObject。
 ![添加 RemoteRenderedModel 组件](./media/add-remote-rendered-model-script.png)
1. 使用适当的模型名称填充 `Model Display Name`。
1. 使用在上面的引入步骤中创建的模型共享访问签名 (SAS) URI 填充 `Model Path`。
1. 将 GameObject 放置在相机前面，位置为 x = 0, y = 0, z = 3。
1. 确保启用 AutomaticallyLoad。
1. 在 Unity 编辑器中按“播放”来测试应用程序。

    你会看到，控制台开始填充当前状态，最后会看到模型加载进度消息。 然后，场景中载入自定义模型。

1. 从场景中删除自定义模型对象。 本教程中的最佳实践将使用测试模型。 虽然 ARR 中支持多个模型，但在按照本教程操作时，最好一次支持一个远程模型。

## <a name="next-steps"></a>后续步骤

现在你可以将自己的模型加载到 Azure 远程渲染并在应用程序中进行查看了！ 接下来介绍如何操作模型。

> [!div class="nextstepaction"]
> [下一步：操作模型](../manipulate-models/manipulate-models.md)
