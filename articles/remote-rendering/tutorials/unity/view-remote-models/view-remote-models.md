---
title: 查看远程渲染的模型
description: Azure 远程渲染的“Hello World”教程介绍了如何查看 Azure 远程渲染的模型
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 7509a17127f04220a8e8450a81627354b28bdacd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006459"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>教程：查看远程渲染的模型

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 预配 Azure 远程渲染 (ARR) 实例
> * 创建和停止渲染会话
> * 重用现有渲染会话
> * 连接会话以及从会话断开连接
> * 将模型载入渲染会话

## <a name="prerequisites"></a>先决条件

本教程需要：

* 有效的即用即付 Azure 订阅 [创建帐户](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0[（下载）](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Visual Studio 2019 最新版本[（下载）](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT[（下载）](https://git-scm.com/downloads)。
* 2019.3 的 Unity 最新版本，建议为此使用 Unity Hub[（下载）](https://unity3d.com/get-unity/download)
  * 在 Unity 中安装以下模块：
    * **UWP** - 通用 Windows 平台生成支持
    * **IL2CPP** - Windows 生成支持 (IL2CPP)
* 中级程度的 Unity 与 C# 语言知识（例如：创建脚本和对象、使用预制项、配置 Unity 事件等）

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>预配 Azure 远程渲染 (ARR) 实例

若要获取对 Azure 远程渲染服务的访问权限，首先需要[创建一个帐户](../../../how-tos/create-an-account.md#create-an-account)。

## <a name="create-a-new-unity-project"></a>创建新的 Unity 项目

> [!TIP]
> [ARR 示例存储库](https://github.com/Azure/azure-remote-rendering) 包含一个已完成了所有教程的项目，可将其用作参考。 在 Unity\Tutorial-Complete 中查找完整的 Unity 项目。

在 Unity Hub 中创建一个新项目。
本示例假设在名为 RemoteRendering 的文件夹中创建项目。

:::image type="content" source="./media/unity-new-project.PNG" alt-text="新的 Unity 项目":::

## <a name="include-the-azure-remote-rendering-package"></a>包括 Azure 远程渲染包

需要修改位于 Unity 项目文件夹中的 `Packages/manifest.json` 文件。 在文本编辑器中打开此文件，然后将以下行添加到清单顶部：

```json
{
    "scopedRegistries": [
    {
        "name": "Azure Mixed Reality Services",
        "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
        "scopes": ["com.microsoft.azure"]
    }
    ],
    "dependencies": {
        "com.unity.render-pipelines.universal": "7.3.1",
        "com.microsoft.azure.remote-rendering": "0.1.31",
        ...existing dependencies...
    }
}
```

修改并保存清单后，Unity 将自动刷新。 在“项目”窗口中确认包已加载：

:::image type="content" source="./media/confirm-packages.png" alt-text="确认包导入":::

如果包未加载，检查 Unity 控制台中是否存在错误。 如果没有任何错误，但在 Packages 文件夹下仍然看不到任何包，请检查包可见性切换按钮。
![Unity 相机属性](./media/unity-package-visibility.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>确保已安装最新版本的包

以下步骤确保项目使用的是最新版本的远程渲染包。

1. 在 Unity 编辑器的顶部菜单中，打开“窗口”->“包管理器”。
1. 选择包“Microsoft Azure 远程渲染”。
1. 在 Microsoft Azure 远程渲染包的包管理器页上，查看更新按钮是否可用 。 如果可用，请单击此按钮以将包更新到最新可用版本：
![包管理器中的 ARR 包](./media/package-manager.png)
1. 更新包有时可能会导致控制台错误。 如果出错，请尝试关闭再重新打开项目。
1. 当包为最新版本时，包管理器将显示“最新”而不是“更新”按钮。
![最新包](./media/package-up-to-date.png)
## <a name="configure-the-camera"></a>配置相机

1. 选择“主相机”节点。

1. 右键单击“转换”组件并选择“重置”选项，打开上下文菜单：

    ![重置相机变换](./media/camera-reset-transform.png)

1. 将“清除标志”设置为“纯色”

1. 将“背景”设置为“黑色”(#000000)，并将 alpha (A) 设置为完全透明 (0)

    ![色轮](./media/color-wheel-black.png)

1. 将“剪裁平面”设置为“近距 = 0.3”、“远距 = 20”。 。 这意味着，渲染功能将剪裁 30 厘米以内或者 20 米以外的几何结构。

    ![Unity 相机属性](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>调整项目设置

1. 打开“编辑”>“项目设置...”
1. 在左侧列表菜单中，选择“质量”
1. 将所有平台的“默认质量水平”更改为“低”。 此设置可以更高效地渲染本地内容，并且不会影响远程渲染内容的质量。

    ![更改项目质量设置](./media/settings-quality.png)

1. 在左侧列表菜单中，选择“图形”
1. 将“可编写脚本的渲染管道”设置更改为“HybridRenderingPipeline”。
    ![更改项目图形设置](./media/settings-graphics-render-pipeline.png)\
    有时 UI 不会填充包中可用的管道类型列表。 如果发生这种情况，需要将 HybridRenderingPipeline 资产手动拖动到该字段上：
    ![更改项目图形设置](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > 如果无法将 HybridRenderingPipeline 资产拖放到 Render Pipeline Asset 字段中（可能是因为该字段不存在！），请确保包配置包含了 `com.unity.render-pipelines.universal` 包。

1. 在左侧列表菜单中，选择“播放机”
1. 选择“通用 Windows 平台设置”选项卡，以 Windows 图标表示。
1. 更改“XR 设置”以支持 Windows 混合现实，如下所示：
    1. 启用“支持虚拟现实”
    1. 按“+”按钮添加“Windows 混合现实”
    1. 将“深度格式”设置为“16 位深度”
    1. 请确保已启用“深度缓冲区共享”
    1. 将“立体渲染模式”设置为“单通道实例化”

    ![播放机设置](./media/xr-player-settings.png)

1. 在同一窗口中，在“XR 设置”上方，展开“发布设置” 
1. 向下滚动到“功能”并选择：
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer**（可选）。 若要将 Unity 远程调试器连接到设备，请选择此选项。

1. 在“支持的设备系列”下，启用“全息”和“桌面”  
1. 关闭或停靠“项目设置”面板
1. 打开“文件”->“生成设置”
1. 选择“通用 Windows 平台”
1. 配置你的设置以匹配下面的设置
1. 按“切换平台”按钮。
![生成设置](./media/build-settings.png)
1. Unity 更改平台后，请关闭生成面板。

## <a name="validate-project-setup"></a>验证项目设置

执行以下步骤来验证项目设置是否正确。

1. 从 Unity 编辑器工具栏上的“RemoteRendering”菜单中选择“ValidateProject”菜单项 。
1. 如果需要，请查看“ValidateProject”窗口是否存在错误并修复项目设置。

    ![Unity 编辑器项目验证](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>创建一个脚本用于协调 Azure 远程渲染连接和状态

需要完成四个基本阶段才能显示远程渲染的模型，如下面的流程图所示。 必须按顺序完成每个阶段。 下一步是创建一个脚本，该脚本将管理应用程序状态并完成每个必需的阶段。

![ARR 堆栈 0](./media/remote-render-stack-0.png)

1. 在“项目”窗格中的“资产”文件夹下，创建名为“RemoteRenderingCore”的新文件夹。 然后在“RemoteRenderingCore”中，创建名为“Scripts”的文件夹 。

1. 创建一个名为 RemoteRenderingCoordinator 的[新 C# 脚本](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)。
项目应如下所示：

    ![项目层次结构](./media/project-structure.png)

    该协调器脚本将跟踪和管理远程渲染状态。 值得注意的是，其中有一些代码用于维护状态，向其他组件公开功能，触发事件以及存储不直接与 Azure 远程渲染相关的特定于应用程序的数据。 以下面的代码为起点，我们将在本教程的后面介绍和实现特定的 Azure 远程渲染代码。

1. 在代码编辑器中打开 RemoteRenderingCoordinator 并将其全部内容替换为以下代码：

```csharp
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // For most people '<region>' is either 'westus2' or 'westeurope'
    [SerializeField]
    private string accountDomain = "westus2.mixedreality.azure.com";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountKey = "<enter your account key here>";
    public string AccountKey {
        get => accountKey.Trim();
        set => accountKey = value;
    }

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    [SerializeField]
    private string sessionIDOverride;
    public string SessionIDOverride {
        get => sessionIDOverride.Trim();
        set => sessionIDOverride = value;
    }

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<AzureFrontendAccountInfo> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.Log($"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static AzureSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<AzureFrontendAccountInfo> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(SessionIDOverride))
                return SessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        var allSessions = await ARRSessionService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();
        return allSessions.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Actions.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Actions?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        var properties = await session.GetPropertiesAsync().AsTask();

        switch (properties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>创建 Azure 远程渲染 GameObject

远程渲染协调器及其所需的脚本 (ARRServiceUnity) 都是必须附加到场景中的 GameObject 上的 MonoBehaviours。 ARR 提供了 ARRServiceUnity 脚本，用于公开 ARR 的许多功能，这些功能用于连接和管理远程会话。

1. 在场景中创建一个新的 GameObject（Ctrl+Shift+N 或 GameObject -> Create Empty），并将其命名为 RemoteRenderingCoordinator。
1. 将 RemoteRenderingCoordinator 脚本添加到 RemoteRenderingCoordinator GameObject 中。
![添加 RemoteRenderingCoordinator 组件](./media/add-coordinator-script.png)
1. 确认 ARRServiceUnity 脚本（在检查器中显示为 Service）已自动添加到 GameObject 中 。 这里解释一下，这是将 `[RequireComponent(typeof(ARRServiceUnity))]` 放在 RemoteRenderingCoordinator 脚本顶部所产生的效果。
1. 将 Azure 远程渲染凭据和帐户域添加到协调器脚本：
![添加凭据](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>初始化 Azure 远程渲染

现在我们有了协调器的框架，我们将从初始化远程渲染开始，实现这四个阶段中的每个阶段。

![ARR 堆栈 1](./media/remote-render-stack-1.png)

“初始化”可指示 Azure 远程渲染使用哪个相机对象进行渲染，并促使状态机进入“NotAuthorized”状态 。 这意味着它已初始化，但尚未获得连接会话的授权。 由于启动 ARR 会话会产生费用，因此我们需要确认用户是否希望继续。

进入 NotAuthorized 状态时，将调用 CheckAuthorization，这将调用 RequestingAuthorization 事件并确定要使用的帐户凭据（AccountInfo 在类顶部附近定义，并使用你在上述步骤中通过 Unity 检查器定义的凭据）   。

   > [!NOTE]
   > ARR 不支持运行时重新编译。 在激活播放模式时修改并保存该脚本可能会导致 Unity 冻结，且需要通过任务管理器强制关闭。 在编辑脚本之前，请务必确保已停止播放模式。

1. 将 InitializeARR 和 InitializeSessionService 的内容替换为以下代码 ：

 ```csharp
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

为从 NotAuthorized 状态进展到 NoSession 状态，通常会向用户呈现一个模式对话框，以便他们可以选择（我们将在另一章中进行介绍） 。 现在，我们会在触发 RequestingAuthorization 事件时立即调用 ByPassAuthentication 来自动绕过授权检查 。

1. 选择 RemoteRenderingCoordinator GameObject 并找到在 RemoteRenderingCoordinator 组件的检查器中公开的 OnRequestingAuthorization Unity 事件  。

1. 按右下方的“+”添加新事件。
1. 将组件拖到其自己的事件上，以引用其自身。
![绕过身份验证](./media/bypass-authorization-add-event.png)\
1. 在下拉列表中，选择“RemoteRenderingCoordinator”->“BypassAuthorization”。
![绕过身份验证](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>创建或加入远程会话

第二阶段是创建或加入远程渲染会话（有关详细信息，请参阅[远程渲染会话](../../../concepts/sessions.md)）。

![ARR 堆栈 2](./media/remote-render-stack-2.png)

远程会话是渲染模型的地方。 JoinRemoteSession( ) 方法将尝试加入使用 LastUsedSessionID 属性跟踪的现有会话或是在 SessionIDOverride 上有已分配的活动会话 ID 的情况下加入  。 SessionIDOverride 仅用于调试目的，应只在知道会话存在并要显式连接到该会话时使用。

如果没有可用会话，将创建一个新的会话。 然而，创建新会话是一个相当耗时的操作。 因此，应该尝试只在需要时创建会话，并尽可能重用它们（有关管理会话的详细信息，请参阅[商业就绪：会话池、计划和最佳做法](../commercial-ready/commercial-ready.md#fast-startup-time-strategies)）。

> [!TIP]
> StopRemoteSession() 将结束活动会话。 若要避免产生不必要的费用，应在不再需要会话时将其停止。

根据可用会话，状态机现在将进展到 ConnectingToNewRemoteSession 或 ConnectingToExistingRemoteSession 。 打开现有会话或创建新会话都将触发 ARRSessionService.OnSessionStatusChanged 事件，并执行我们的 OnRemoteSessionStatusChanged 方法 。 理想情况下，这会使状态机进展到 RemoteSessionReady。

1. 若要加入新会话，请修改代码，将 JoinRemoteSession( ) 和 StopRemoteSession( ) 方法替换为以下完整示例内容 ：

```csharp
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationParams(renderingSessionVmSize, maxLeaseHours, maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == SessionIDOverride)
            SessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

如果要通过重用会话来节省时间，请确保停用 ARRServiceUnity 组件中的“自动停止会话”选项。 请记住，即使没有人连接到会话，这也会使会话保持运行状态。 会话可以持续运行 MaxLeaseTime 指定的时间长度，此时间过后，会话将被服务器关闭（可在远程渲染协调器的“新会话默认值”下修改 MaxLeaseTime 的值）  。 另一方面，如果在断开连接时自动关闭每个会话，那么每次都必须等待新会话启动，这可能是一个漫长的过程。

> [!NOTE]
> 停止会话的操作会立即生效，且无法撤消。 停止会话后，必须创建新会话，其启动开销是相同的。

## <a name="connect-the-local-runtime-to-the-remote-session"></a>将本地运行时连接到远程会话

接下来，应用程序需要将其本地运行时连接到远程会话。

![ARR 堆栈 3](./media/remote-render-stack-3.png)

应用程序还需要侦听有关运行时与当前会话之间的连接的事件；这些状态更改将在 OnLocalRuntimeStatusChanged 中进行处理。 此代码会使状态进展至 ConnectingToRuntime。 在 OnLocalRuntimeStatusChanged 中建立连接后，状态将进展至 RuntimeConnected 。 连接到运行时是与协调器本身相关的最后一种状态，这意味着应用程序已完成所有通用配置，并且已准备好开始加载和渲染模型的特定于会话的工作。

 1. 将 ConnectRuntimeToRemoteSession( ) 和 DisconnectRuntimeFromRemoteSession( ) 方法替换为以下已完成的版本 。
 1. 请务必注意 Unity 方法 LateUpdate，它正在更新当前的活动会话。 这允许当前会话发送/接收消息，并通过远程会话接收的帧更新帧缓冲区。 这对 ARR 的正常运行至关重要。

```csharp
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectToRuntime(new ConnectToRuntimeParams());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.DisconnectFromRuntime();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Actions.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Actions?.Update();
}
```

> [!NOTE]
> 将本地运行时连接到远程会话取决于在当前活动会话上调用 Update。 如果发现应用程序的进展从未超过 ConnectingToRuntime 状态，请确保在活动会话中定期调用 Update 。

## <a name="load-a-model"></a>加载模型

设置好所需的基础结构后，就可以将模型加载到远程会话并开始接收帧了。

![ARR 堆栈 4](./media/remote-render-stack-4.png)

LoadModel 方法的作用是接受模型路径、进度处理程序和父转换。 这些参数用于将模型加载到远程会话中，向用户更新加载进度，并基于父转换确定远程渲染模型的方向。

1. 将 LoadModel 方法完全替换为以下代码：

    ```csharp
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        if(progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

上述代码将执行以下步骤：

1. 创建[远程实体](../../../concepts/entities.md)。
1. 创建用于表示远程实体的本地 GameObject。
1. 配置本地 GameObject 以将其状态（即转换）同步到每个帧的远程实体。
1. 设置名称并添加一个 [WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) 以增强稳定性。
1. 将模型数据从 Blob 存储加载到远程实体。
1. 返回父实体，供以后引用。

## <a name="view-the-test-model"></a>查看测试模型

现在，我们拥有了查看远程渲染模型所需的所有代码，并完成了远程渲染必需的所有四个阶段。 现在我们需要添加一些代码来启动模型加载过程。

![ARR 堆栈 4](./media/remote-render-stack-5.png)

1. 将以下代码添加到 RemoteRenderingCoordinator 类，就在 LoadModel 方法的正下方 ：

    ```csharp
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    此代码创建一个 GameObject 作为测试模型的父项。 然后，它调用 LoadModel 方法来加载模型“builtin://Engine”，这是 Azure 远程渲染中内置的资产，用于测试渲染。

1. 保存代码。
1. 按 Unity 编辑器中的“播放”按钮以启动连接到 Azure 远程渲染并创建新会话的过程。
1. 在“游戏”视图中不会看到太多内容，但是，控制台将显示应用程序状态的变化。 可能会进展至 `ConnectingToNewRemoteSession`，并保持最多五分钟。
1. 选择 RemoteRenderingCoordinator GameObject，以在检查器中查看其附加的脚本。 观察“服务”组件在其初始化和连接步骤中的更新。
1. 监视控制台输出 - 等待状态更改为 RuntimeConnected。
1. 连接运行时后，右键单击检查器中的 RemoteRenderingCoordinator 以显示上下文菜单。 然后，单击上下文菜单中的“加载测试模型”选项，该选项由上面代码的 `[ContextMenu("Load Test Model")]` 部分添加。

    ![从上下文菜单加载](./media/load-test-model.png)

1. 监视控制台，查看传递给 LoadModel 方法的 ProgressHandler 的输出 。
1. 查看远程渲染的模型！

> [!NOTE]
> 远程模型永远不会显示在“场景”视图中，只显示在“游戏”视图中。 这是因为 ARR 专门从“游戏”视图照相机的角度远程渲染帧，而不感知编辑器照相机（用于渲染“场景”视图）。

## <a name="next-steps"></a>后续步骤

![已加载模型](./media/test-model-rendered.png)

祝贺你！ 你已经创建了一个基本应用程序，该应用程序能够使用 Azure 远程渲染来查看远程渲染的模型。 在下一教程中，我们将集成 MRTK 并导入我们自己的模型。

> [!div class="nextstepaction"]
> [下一步：接口和自定义模型](../custom-models/custom-models.md)
