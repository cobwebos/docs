---
title: Windows 上的语音助手-超出锁实现准则
titleSuffix: Azure Cognitive Services
description: 用于实现语音代理应用程序的语音激活和锁定功能的说明。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 726dd4e18565174c8bbf49b204af64129e607db5
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714723"
---
# <a name="implementing-voice-assistants-on-windows"></a>在 Windows 上实现语音助手

本指南介绍了在 Windows 上创建语音助手的重要实现细节。

## <a name="implementing-voice-activation"></a>实现语音激活

[设置环境](how-to-windows-voice-assistants-get-started.md)并了解[语音激活的工作方式](windows-voice-assistants-overview.md#how-does-voice-activation-work)后，可以开始为自己的语音助手应用程序实现语音激活。

### <a name="registration"></a>注册

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>确保麦克风可用且可访问，然后监视其状态

MVA 需要提供麦克风并可访问，以便能够检测语音激活。 使用[AppCapability](https://docs.microsoft.com/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability?view=winrt-18362)、 [DeviceWatcher](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher?view=winrt-18362)和[MediaCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture?view=winrt-18362)类分别检查麦克风隐私访问、设备是否存在以及设备状态（如音量和静音）。

### <a name="register-the-application-with-the-background-service"></a>向后台服务注册应用程序

为了让 MVA 在后台启动应用程序，需要将应用程序注册到后台服务。 请参阅[此处](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task)的有关后台服务注册的完整指南。

### <a name="unlock-the-limited-access-feature"></a>解锁受限访问功能

使用你的 Microsoft 提供的有限访问权限功能密钥解锁语音助手功能。 使用 Windows SDK 中的[LimitedAccessFeature](https://docs.microsoft.com/uwp/api/windows.applicationmodel.limitedaccessfeatures?view=winrt-18362)类执行此操作。

### <a name="register-the-keyword-for-the-application"></a>为应用程序注册关键字

应用程序需要在 Windows 上注册自身、其关键字模型及其语言。

首先检索关键字检测器。 在此示例代码中，我们检索第一个探测器，但你可以通过从选择它来选择它 `configurableDetectors` 。

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

检索 ActivationSignalDetector 对象后，调用其 `ActivationSignalDetector.CreateConfigurationAsync` 方法，其中包含信号 ID、型号 id 和显示名称，用于注册关键字并检索应用程序的 `ActivationSignalDetectionConfiguration` 。 信号和模型 Id 应该是开发人员确定的 guid，并且对于同一个关键字保持一致。

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>验证是否已启用语音激活设置

若要使用语音激活，用户需要为其系统启用语音激活，并为应用程序启用语音激活。 你可以在 "Windows 设置" 中的 "语音激活隐私设置" 下找到此设置。 若要检查应用程序中语音激活设置的状态，请使用中的实例 `ActivationSignalDetectionConfiguration` 注册关键字。 上的[AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128)字段 `ActivationSignalDetectionConfiguration` 包含一个枚举值，该值描述语音激活设置的状态。

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>检索 ConversationalAgentSession 以向 MVA 系统注册应用程序

`ConversationalAgentSession`是 Windows SDK 中的一个类，它允许应用程序使用应用状态（空闲、检测、侦听、工作、说话）更新 Windows，并接收事件，如激活检测和系统状态更改（如屏幕锁定）。 检索 AgentSession 的实例还可将应用程序注册为 Windows，并按语音激活。 最佳做法是保持对的引用 `ConversationalAgentSession` 。 若要检索会话，请使用 `ConversationalAgentSession.GetCurrentSessionAsync` API。

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>倾听两个激活信号： OnBackgroundActivated 和 OnSignalDetected

Windows 将在通过以下两种方式之一来检测关键字时发出通知。 如果应用未处于活动状态（即，你没有对的非释放实例的引用 `ConversationalAgentSession` ），则它将启动你的应用程序并在应用程序的 App.xaml.cs 文件中调用 OnBackgroundActivated 方法。 如果事件参数的 `BackgroundActivatedEventArgs.TaskInstance.Task.Name` 字段与字符串 "AgentBackgroundTrigger" 相匹配，则应用程序启动将由语音激活触发。 应用程序需要重写此方法，并检索 ConversationalAgentSession 的实例以向现在处于活动状态的窗口发出信号。 当应用程序处于活动状态时，Windows 将使用事件发出声音激活的信号 `ConversationalAgentSession.OnSignalDetected` 。 一旦检索到此事件，就添加一个事件处理程序 `ConversationalAgentSession` 。

## <a name="keyword-verification"></a>关键字验证

语音代理应用程序通过语音激活后，下一步是验证关键字检测是否有效。 Windows 不提供关键字验证解决方案，但允许语音助手从假设激活中访问音频，并自行完成验证。

### <a name="retrieve-activation-audio"></a>检索激活音频

创建[AudioGraph](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph)并将其传递到的 `CreateAudioDeviceInputNodeAsync` `ConversationalAgentSession` 。 这会加载在*检测到关键字之前约3秒开始*音频的图形音频缓冲区。 随附这一额外的领先音频是为了容纳各种关键字长度和发言人速度。 然后，处理来自音频图形的[QuantumStarted](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph.quantumstarted?view=winrt-18362)事件以检索音频数据。

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

注意：音频缓冲区中包含的前导音频可能导致关键字验证失败。 若要解决此问题，请在发送音频缓冲区的开头之前剪裁关键字验证。 此初始剪裁应针对每个助手进行定制。

### <a name="launch-in-the-foreground"></a>在前台启动

关键字验证成功后，应用程序需要移到前台才能显示 UI。 调用 `ConversationalAgentSession.RequestForegroundActivationAsync` API，将应用程序移动到前台。

### <a name="transition-from-compact-view-to-full-view"></a>从紧凑视图过渡到完整视图

当应用程序第一次通过语音激活时，它会在精简视图中启动。 请阅读[语音激活预览版的设计指南](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview)，以获取有关 Windows 上的语音助手的不同视图和它们之间的转换。

若要将精简视图转换为完整应用视图，请使用 w API `TryEnterViewModeAsync` ：

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>实现以上锁定激活

以下步骤介绍了在 Windows 上启用语音助手以运行上述锁定的要求，包括对示例代码的引用和有关管理应用程序生命周期的指导原则。

有关设计以上锁定体验的指导，请访问[最佳做法指南](windows-voice-assistants-best-practices.md)。

应用显示超出锁定状态的视图时，会将其视为 "展台模式"。 有关实现使用展台模式的应用的详细信息，请参阅[展台模式文档](https://docs.microsoft.com/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access)。

### <a name="transitioning-above-lock"></a>转换超过锁

上面的激活与激活的锁定类似。 如果没有应用程序的活动实例，将在后台启动新实例，并 `OnBackgroundActivated` 在 App.xaml.cs 中调用。 如果有应用程序的实例，则该实例将通过事件获得通知 `ConversationalAgentSession.SignalDetected` 。

如果应用程序未显示在锁定之上，则必须调用 `ConversationalAgentSession.RequestForegroundActivationAsync` 。 这会触发 `OnLaunched` App.xaml.cs 中的方法，该方法应导航到将显示在锁定上方的视图。

### <a name="detecting-lock-screen-transitions"></a>检测锁屏锁定转换

Windows SDK 中的 ConversationalAgent 库提供了一个 API，用于使锁定屏幕状态和更改锁定屏幕状态轻松访问。 若要检测当前锁屏状态，请检查 `ConversationalAgentSession.IsUserAuthenticated` 字段。 若要检测锁定状态的更改，请向对象的事件添加事件处理程序 `ConversationalAgentSession` `SystemStateChanged` 。 当屏幕更改为 "锁定" 或 "锁定" 时，将触发此方法。 如果事件参数的值为 `ConversationalAgentSystemStateChangeType.UserAuthentication` ，则锁定屏幕状态已更改。

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>检测以上锁定激活用户首选项

"语音激活隐私设置" 页中的 "应用程序" 条目具有对以上锁定功能的切换。 要使你的应用能够启动以上锁定，用户将需要启用此设置。 以上锁定权限的状态也存储在 ActivationSignalDetectionConfiguration 对象中。 AvailabilityInfo. HasLockScreenPermission 状态反映用户是否已获得上述锁定权限。 如果禁用此设置，语音应用程序可以提示用户导航到链接 "ms-settings： voiceactivation" 的相应设置页面，并提供有关如何对应用程序启用上述-锁定激活的说明。

## <a name="closing-the-application"></a>关闭应用程序

若要在上方或下方锁定时以编程方式适当关闭应用程序，请使用 `WindowService.CloseWindow()` API。 这会触发所有 UWP 生命周期方法（包括 OnSuspend），使应用程序能够 `ConversationalAgentSession` 在关闭前释放其实例。

> [!NOTE]
> 应用程序可以在不关闭[以下锁定实例](https://docs.microsoft.com/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-)的情况下关闭。 在这种情况下，以上锁定视图需要 "清除"，确保在屏幕解锁后，不存在将尝试操作以上锁定视图的事件处理程序或任务。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [有关示例和代码演练，请访问 UWP Voice 助手示例应用演练](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
