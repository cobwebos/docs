---
title: 从头开始设置 Unity 项目
description: 介绍如何配置一个用于 Azure 远程渲染的空白 Unity 项目。
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 7901f12763cb97fa76c0908e76755247ae934a20
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300583"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>教程：从头开始设置 Unity 项目

本教程的内容：

> [!div class="checklist"]
>
> * 配置用于 ARR 的全新 Unity 项目。
> * 创建和停止渲染会话。
> * 重用现有的会话。
> * 连接会话以及从会话断开连接。
> * 将模型载入渲染会话。
> * 显示连接统计信息。

## <a name="prerequisites"></a>先决条件

本教程需要：

* 帐户信息（帐户 ID、帐户密钥、订阅 ID）。 如果你没有帐户，请[创建一个帐户](../../how-tos/create-an-account.md)。
* Windows SDK 10.0.18362.0[（下载）](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Visual Studio 2019 最新版本[（下载）](https://visualstudio.microsoft.com/vs/older-downloads/)。 
* [适用于混合现实的 Visual Studio tools](https://docs.microsoft.com/windows/mixed-reality/install-the-tools)。 具体来说，必须安装以下工作负载：
  * **使用 C++ 的桌面开发**
  * **通用 Windows 平台 (UWP) 开发**
* GIT[（下载）](https://git-scm.com/downloads)。
* Unity 2019.3.1[（下载）](https://unity3d.com/get-unity/download)
  * 在 Unity 中安装以下模块：
    * **UWP** - 通用 Windows 平台生成支持
    * **IL2CPP** - Windows 生成支持 (IL2CPP)

> [!TIP]
> [ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)包含为所有教程准备的 Unity 项目。 可以参考这些项目。

## <a name="create-a-new-unity-project"></a>创建新的 Unity 项目

在 Unity Hub 中创建一个新项目。
本示例假设在名为 `RemoteRendering` 的文件夹中创建项目。

![“新建项目”窗口](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>配置项目的清单

需要修改位于 Unity 项目文件夹中的 `Packages/manifest.json` 文件。 在文本编辑器中打开该文件，并追加下面列出的行：

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
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

通用渲染管道包是可选的，但出于性能原因，我们建议安装此包。
修改并保存清单后，Unity 将自动刷新。 在“项目”窗口中确认包已加载：

![确认包导入](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>确保已安装最新版本的包

以下步骤确保项目使用的是最新版本的远程渲染包。
1. 在“项目”窗口中选择该包，然后单击 :::no-loc text="package"::: 图标：![选择包图标](media/package-icons.png)
1. 在“检查器”中，单击“在包管理器中查看”：![包检查器](media/package-properties.png)
1. 在远程渲染包的包管理器页上，查看更新按钮是否可用。 如果可用，单击此按钮会将包更新到最新可用版本：![包管理器中的 ARR 包](media/package-manager.png)
1. 有时，更新包可能会导致控制台中出错。 如果出错，请尝试关闭再重新打开项目。

## <a name="configure-the-camera"></a>配置相机

选择“主相机”节点。

1. 将其重置为“变换”：

    ![重置相机变换](media/camera-reset-transform.png)

1. 将 :::no-loc text="Clear flags"::: 设置为 :::no-loc text="Solid Color":::

1. 将 :::no-loc text="Background"::: 设置为 :::no-loc text="Black":::

1. 将 :::no-loc text="Clipping Planes"::: 设置为“Near = 0.3”和“Far = 20” 。 这意味着，渲染功能将剪裁 30 厘米以内或者 20 米以外的几何结构。

    ![Unity 相机属性](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>调整项目设置

1. 打开“编辑”>“项目设置...”
1. 在左侧列表中，选择“质量”。
1. 将“默认质量水平”更改为“低”

    ![更改项目质量设置](media/settings-quality.png)

1. 在左侧选择“图形”。
1. 将“可编写脚本的渲染管道”设置更改为“HybridRenderingPipeline”。 如果不使用通用渲染管道，请跳过此步骤。

    ![更改项目图形设置](media/settings-graphics-lwrp.png) 有时，UI 不会基于包填充可用管道类型的列表，在这种情况下，必须手动将“HybridRenderingPipeline”资产拖放到字段中：![更改项目图形设置](media/hybrid-rendering-pipeline.png)
1. 在左侧选择“播放器”。
1. 选择“通用 Windows 平台设置”选项卡
1. 更改“XR 设置”以支持 Windows Mixed Reality：![播放器设置](media/xr-player-settings.png)
1. 按以上屏幕截图中所示选择设置：
    1. 启用“支持虚拟现实”
    1. 将“深度格式”设置为“16 位深度”
    1. 启用“深度缓冲区共享”
    1. 将“立体渲染模式”设置为“单通道实例化”

1. 在同一窗口中，在“XR 设置”上方，展开“发布设置”
1. 向下滚动到“功能”并选择：
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * 在开发中是可选的：**PrivateNetworkClientServer**

      若要将 Unity 远程调试器连接到设备，需要选择此选项。

1. 在“支持的设备系列”中，启用“全息”和“桌面”  

1. 若要使用混合现实工具包，请参阅 [MRTK 文档](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview)来了解有关建议的设置和功能的详细信息。

## <a name="validate-project-setup"></a>验证项目设置

执行以下步骤来验证项目设置是否正确。

1. 从 Unity 编辑器工具栏上的“RemoteRendering”菜单中选择“ValidateProject”菜单项。
1. 如果需要，请使用“ValidateProject”窗口检查并修复项目设置。

    ![Unity 编辑器项目验证](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>创建一个脚本用于初始化 Azure 远程渲染

创建一个[新脚本](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)并将其命名为“RemoteRendering”。 打开该脚本文件，并将其整个内容替换为以下代码：

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

此脚本初始化 Azure 远程渲染，告诉 Azure 远程渲染要使用哪个相机对象进行渲染，并且当激活了“播放模式”时，在视区中放置一个“创建会话”按钮。

> [!CAUTION]
> 在 Unity 中激活了播放模式时，修改并保存该脚本可能会导致 Unity 冻结，使你被迫通过任务管理器关闭 Unity。 因此，在编辑 RemoteRendering 脚本之前，请始终停止播放模式。

## <a name="test-azure-remote-rendering-session-creation"></a>测试 Azure 远程渲染会话的创建

在场景中创建新的 GameObject，并在其中添加 RemoteRendering 组件。 填写你的远程渲染帐户的相应“帐户域”、“帐户 ID”和“帐户密钥”：  

![远程渲染组件属性](media/remote-rendering-component.png)

在编辑器中启动应用程序（按“播放”或 CTRL+P）。 应会看到“创建会话”按钮显示在视区中。 单击该按钮启动你的第一个 ARR 会话：

![创建第一个会话](media/test-create.png)

如果此操作失败，请确保在 RemoteRendering 组件属性中正确输入了帐户详细信息。 否则，控制台窗口中会出现一条消息，其中显示了分配给你的会话 ID，并指出会话当前处于“正在启动”状态：

![“会话正在启动”输出消息](media/create-session-output.png)

此时，Azure 正在预配服务器，并且正在启动远程渲染虚拟机。 此过程通常需要 3 到 5 分钟。 VM 准备就绪后，将执行 Unity 脚本的 `OnSessionStatusChanged` 回调，并输出新会话状态：

![“会话准备就绪”输出消息](media/create-session-output-2.png)

这就是所有的设置！ 现在，不会再发生其他情况。 若要防止产生费用，请始终停止不再需要的会话。 在此示例中，若要停止会话，可以单击“停止会话”按钮，或者停止 Unity 模拟。 由于“ARRServiceUnity”组件中使用了默认处于打开状态的“自动停止会话”属性，该会话将自动停止。 如果由于故障或连接问题而有任何操作失败，会话可以持续运行 MaxLeaseTime 指定的时间，此时间过后，会话将被服务器关闭。

> [!NOTE]
> 停止会话的操作会立即生效，且无法撤消。 停止会话后，必须创建新会话，其启动开销是相同的。

## <a name="reusing-sessions"></a>重用会话

遗憾的是，创建新会话是一个相当耗时的操作。 因此，请只是在一些罕见的情况下才尝试创建会话，应尽可能地重用会话。

将以下代码插入到 RemoteRendering 脚本中，并删除重复函数的旧版本：

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> 在运行此代码之前，请确保在 RemoteRendering 组件中停用选项“自动停止会话”。 否则，在停止模拟时，创建的每个会话都会自动停止，并且无法重用该会话。

按下“播放”时，你会在视区中看到三个按钮：“创建会话”、“查询活动会话”和“使用现有会话”。   第一个按钮始终创建新会话。 第二个按钮查询存在哪些活动会话。 如果你未手动指定要尝试使用的会话 ID，此操作会自动选择该会话 ID 供将来使用。 第三个按钮尝试连接到现有会话。 该会话是通过“会话 ID”组件属性手动指定的会话，或者是“查询活动会话”找到的会话。 

AutoStartSessionAsync 函数用来模拟编辑器外部的按下按钮动作。

> [!TIP]
> 可以打开已停止、已过期或处于错误状态的会话。 尽管这些会话不再可用于渲染，但打开某个非活动的会话后，就可以查询这些会话的详细信息。 以上代码在 `ARRService_OnSessionStarted` 中检查会话的状态，以便在该会话不可用时自动将其停止。

凭借此功能，现在可以创建和重用会话，这应该会显著改善开发工作流。

由于运转服务器需要花费一些时间，创建会话的操作通常在客户端应用程序外部触发。

## <a name="connect-to-an-active-session"></a>连接到活动会话

到目前为止，我们已创建或打开了会话。 下一步是连接会话。 连接后，渲染服务器将生成图像，并将视频流发送到应用程序。

将以下代码插入到 RemoteRendering 脚本中，并删除重复函数的旧版本：

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

要测试此功能，请执行以下操作：

1. 在 Unity 中按下“播放”。
1. 打开会话：
    1. 如果已有一个会话，请依次按下“查询活动会话”、“使用现有会话”。 
    1. 否则，请按下“创建会话”。
1. 按下“连接”。
1. 几秒钟后，控制台输出中应会指出你已连接。
1. 现在，应该不会再发生其他情况。
1. 按下“断开连接”，或停止 Unity 的播放模式。

>[!NOTE]
> 多个用户可以打开一个会话来查询其信息，但每次只能有一个用户连接到一个会话。  如果另一用户已建立连接，则连接将会失败并出现握手错误。

## <a name="load-a-model"></a>加载模型

将以下代码插入到 RemoteRendering 脚本中，并删除重复函数的旧版本：

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

现在，当按下“播放”、打开一个会话并连接到该会话时，会显示“加载模型”按钮。 单击该按钮后，控制台输出将显示加载进度。当进度达到 100% 时，应会显示引擎模型：

![编辑器中加载的模型](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) 是用于实现[全息影像稳定性](https://docs.microsoft.com/windows/mixed-reality/hologram-stability)的重要组件。 但是，它只是在部署到混合现实设备时才起作用。

> [!TIP]
> 如果你已学习[快速入门：转换要渲染的模型](../../quickstarts/convert-model.md)，则已经知道如何转换自己的模型。 若要渲染该模型，只需将转换后的模型的 URI 放入“模型名称”属性中即可。

## <a name="display-frame-statistics"></a>显示帧统计信息

Azure 远程渲染会跟踪有关连接质量的各种信息。 若要快速显示此信息，请执行以下操作：

创建一个[新脚本](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)并将其命名为“RemoteFrameStats”。 打开该脚本文件，并将其整个内容替换为以下代码：

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

创建一个 GameObject 并将其命名为“FrameStats”。 将其作为子节点附加到“主相机”对象，并将其位置设置为 x = 0，y = 0，z = 0.325。 将“RemoteFrameStats”组件添加到该对象。

将“UI > 画布”子对象添加到“FrameStats”对象，并如下所示设置该子对象的属性：

![画布属性](media/framestats-canvas.png)

将“UI > 文本”对象添加为画布的子级，并如下所示设置其属性：

![文本属性](media/framestats-text.png)

选择“FrameStats”对象，通过单击圆圈图标并选择“文本”对象来填充“FrameStats”字段： 

![设置文本属性](media/framestats-set-text.png)

现在，在连接到远程会话后，文本应显示流统计信息：

![帧统计信息输出](media/framestats-output.png)

此代码将禁用编辑器外部的统计信息更新，因为头部锁定的文本框会分散注意力。 在[快速入门](../../quickstarts/render-model.md)项目中可以找到更复杂的实现。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解创建空白 Unity 项目，并使其可在 Azure 远程渲染中正常运行所需执行的全部步骤。 下一篇教程将更详细地介绍如何使用远程实体。

> [!div class="nextstepaction"]
> [教程：在 Unity 中使用远程实体](working-with-remote-entities.md)
