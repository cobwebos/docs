---
title: Windows 语音助手-设计指南
titleSuffix: Azure Cognitive Services
description: 设计语音代理体验时的最佳实践指南。
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: a9145c7c26f4d6caa1679052035b36f1ae88f878
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714774"
---
# <a name="design-assistant-experiences-for-windows-10"></a>适用于 Windows 10 的设计助手体验

在 Windows 10 上开发的语音助手必须实现下面的用户体验指导原则，以便为 Windows 10 上的语音激活提供最佳体验。 本文档将指导开发人员了解语音助手与 Windows 10 Shell 集成所需的关键工作。

## <a name="contents"></a>目录

- [Windows 10 中支持的语音激活视图摘要](#summary-of-voice-activation-views-supported-in-windows-10)
- [要求摘要](#requirements-summary)
- [良好侦听体验的最佳实践](#best-practices-for-good-listening-experiences)
- [应用内语音激活的设计指南](#design-guidance-for-in-app-voice-activation)
- [超出锁定的语音激活的设计指南](#design-guidance-for-voice-activation-above-lock)
- [语音激活预览版设计指南](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Windows 10 中支持的语音激活视图摘要

Windows 10 根据设备上下文推断出客户上下文的激活体验。 下面的摘要表简要介绍了屏幕打开时可用的不同视图。

| 视图（可用性） | 设备上下文 | 客户目标 | 出现在 | 设计需求 |
| --- | --- | --- | --- | --- |
| **应用内（19H1）** | 下方锁定，助手具有焦点 | 与助手应用交互 | 助手处理应用内的请求 | 应用程序中的主视图侦听体验 |
| **上方锁（19H2）** | 超出锁定，未经身份验证 | 与助手交互，但从远处 | 系统已锁定，助手请求激活 | 面向现场 UI 的全屏幕视觉对象。 实现消除策略，不阻止解锁。 |
| **语音激活预览（20H1）** | 下方锁定，助手没有焦点 | 与助手交互，但以较低的方式进行交互 | 系统低于锁定，助手请求后台激活 | 最小画布。 根据需要调整主应用视图的大小或将其关闭。 |

## <a name="requirements-summary"></a>要求摘要

访问不同体验需要最少的工作量。 但是，助手需要为每个视图实现正确的设计指南。 下表列出了必须遵循的要求。

| **语音激活视图** | **助理要求摘要** |
| --- | --- |
| **应用中** | <ul><li>处理应用内的请求</li><li>为侦听状态提供 UI 指示器</li><li>UI 适应窗口大小更改</li></ul> |
| **上方锁** | <ul><li>检测锁定状态和请求激活</li><li>不要提供阻止访问 Windows 锁屏界面的始终持久 UX</li><li>提供全屏视觉对象和语音首次体验</li><li>遵循下面的消除指导</li><li>遵循下面的隐私和安全注意事项</li></ul> |
| **语音激活预览** | <ul><li>检测解锁状态并请求后台激活</li><li>在预览窗格中绘制最少的侦听 UX</li><li>在右上方和自行消除并在按下时停止流式传输音频</li><li>根据需要调整主助手应用视图的大小或将其关闭以提供答案</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>良好侦听体验的最佳实践

助手应该构建一个倾听体验来提供重要的反馈，使客户能够理解助手的状态。 下面是构建助手体验时需要考虑的一些可能的状态。 这些只是可能的建议，不是必需的指导。

- 助手可用于语音输入
- 助手正在激活（关键字或 mic 按钮按下）
- 助手正在积极地将音频流式传输到助手云
- 助手已准备好让客户开始讲话
- 助手听到说说词汇
- 助手了解客户已完成交谈
- 助手正在处理并准备响应
- 助手正在响应

即使状态更改迅速，也值得考虑提供状态的 UX，因为在 Windows 生态系统中，持续时间是可变的。 视觉反馈以及 brief 音频 chimes 或鸟鸣（也称为 &quot; earcons &quot; ）都是解决方案的一部分。 同样，与音频说明耦合的视觉对象卡也能提供良好的响应选项。

## <a name="design-guidance-for-in-app-voice-activation"></a>应用内语音激活的设计指南

当助手应用有焦点时，客户意向清楚地与应用程序进行交互，因此所有语音激活体验都应由主要的应用程序视图处理。 此视图可能已由客户调整大小。 为了帮助说明助手 shell 交互，本文档的其余部分使用名为 Contoso 的财务服务助理的具体示例。 在此和后续的关系图中，客户显示的内容将显示在左侧的卡通语音气泡中，在右侧的卡通气泡中显示助手响应。

**应用内视图。语音激活开始时的初始状态：** 
 ![ 激活之前 Windows 上的语音助手屏幕截图](media/voice-assistants/windows_voice_assistant/initial_state.png)

**应用内视图。语音激活成功后，倾听体验开始：** ![ 语音助手正在侦听时 Windows 上的语音助手屏幕截图](media/voice-assistants/windows_voice_assistant/listening.png)

**应用内视图。所有响应都保持在应用程序体验中。** ![作为助手回复的 Windows 上的语音助手屏幕截图](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>超出锁定的语音激活的设计指南

可用于19H2，在 Windows 语音激活平台上构建的助手可用于回答以上锁定。

### <a name="customer-opt-in"></a>客户选择加入

默认情况下，始终禁用超出锁定状态的语音激活。 客户选择通过 Windows 设置>隐私>语音激活。 有关监视和提示此设置的详细信息，请参阅[上面的锁定实现指南](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference)。

### <a name="not-a-lock-screen-replacement"></a>不是锁定屏幕替换

尽管通知或其他标准应用锁定屏幕集成点仍可用于助手，但 Windows 锁屏界面始终定义初始客户体验，直到语音激活发生。 检测到语音激活后，助手应用会暂时显示在锁定屏幕上方。 若要避免客户混乱，请在活动上方处于锁定状态时，助手应用程序不得提供 UI 来要求提供任意类型的凭据或登录。

![Windows 锁屏界面的屏幕截图](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>声音激活后的锁体验

当屏幕打开时，助手应用程序为全屏，在锁定屏幕上方没有标题栏。 较大的视觉对象和强大的语音说明，具有强大的语音主界面，允许客户太远离开读取 UI 或通过其他（非 PC）任务进行干预。

当屏幕停留在屏幕上时，助手应用程序可以播放 earcon 来指示助手正在激活，并提供仅语音体验。

![Lock 上方的语音助手屏幕截图](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>消除策略

助手必须实现本部分中的消除指导，使客户在下次使用其 Windows PC 时可以更轻松地登录。 下面是助手必须实现的特定要求：

- **上面显示锁定的所有助手都必须**在右上方包含一个关闭助手的 "X"。
- **按任意键还必须关闭助手应用**。 键盘输入是客户想要登录的传统锁定应用信号。 因此，不应将任何键盘/文本输入定向到应用。 相反，应用程序应在检测到键盘输入时自行关闭，因此客户可以轻松登录到其设备。
- **如果屏幕出现故障，则应用程序必须自行关闭。** 这可确保下一次客户使用其电脑时，登录屏幕将准备就绪并等待。
- 如果应用正在 &quot; 使用中 &quot; ，则它可能会继续锁定。 &quot;使用中 &quot; 构成了任何输入或输出。 例如，当流式传输音乐或视频时，应用可能会继续锁定。 &quot;接下来 &quot; ，还可以执行其他 multiturn 对话框步骤，使应用保持锁定。
- **有关关闭应用程序的实现的详细信息**，请参阅[上述锁定实现指南](windows-voice-assistants-implementation-guide.md#closing-the-application)。

![激活之前 Windows 上的语音助手屏幕截图](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![激活之前 Windows 上的语音助手屏幕截图](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>&amp;超出锁定的隐私安全注意事项

许多 Pc 是可移植的，但并不总是在客户范围内。 它们可能暂时留在旅馆房间、飞机座位或工作区中，而其他人则具有物理访问权限。 如果启用上述锁定的助手未准备就绪，则它们可能会受到所谓的 &quot; [maid](https://en.wikipedia.org/wiki/Evil_maid_attack) &quot; 攻击。

因此，助手应遵循此部分中的指导来帮助保持安全安全。 如果未对 Windows 用户进行身份验证，则会发生锁定。 这意味着，通常情况下，**助手的输入还应视为未经身份验证**。

- 助手应**实现技能白名单，以确定在锁定之前访问安全和安全的技能**。
- 发言人 ID 技术可以在缓解某些风险的情况下发挥作用，但发言人 ID 并不是 Windows 身份验证的合适替代品。
- 技能白名单应考虑三类操作或技能：

| **操作类** | **说明** | **示例（不是完整列表）** |
| --- | --- | --- |
| 无身份验证的安全 | 常规用途信息或基本应用程序命令和控制 | &quot;什么时间？ &quot; ， &quot; 播放下一个曲目&quot; |
| 用发言人 ID 安全 | 模拟风险，泄露个人信息。 | &quot;下一次约会&#39;是什么？ &quot; ， &quot; 查看我的购物列表 &quot; ， &quot; 答复呼叫&quot; |
| 仅在 Windows 身份验证后安全 | 攻击者可能会用来损害客户的高风险操作 | &quot;购买更多杂货 &quot; ， &quot; 删除我的（重要）约会 &quot; ， &quot; 发送 a （均值）短信 &quot; ， &quot; 启动（恶意）网页&quot; |

对于 Contoso，有关公共股票信息的一般信息在未经身份验证的情况下是安全的。 特定于客户的信息（例如，拥有的共享数）很可能是通过扬声器 ID 来安全的。 但是，在没有 Windows 身份验证的情况下，不允许购买或销售股票。

为了进一步保护体验、 **weblinks 或其他应用到应用的启动，Windows 将始终阻止 Windows，直到客户登录。** 作为最后的手段缓解措施，如果不及时解决严重安全问题，Microsoft 将保留从启用的助手白名单中删除应用程序的权利。

## <a name="design-guidance-for-voice-activation-preview"></a>语音激活预览版设计指南

锁定后，如果助手_应用没有焦点_，Windows 提供了一个干扰较少的语音激活 UI，帮助客户保持流动。 这对于错误激活尤其适用，如果启动完整应用程序，这会造成严重的影响。 核心理念是，每个助手在外壳程序中都有另一个主页，即助手任务栏图标。 当后台激活请求发生时，将显示助手任务栏图标上方的小视图。 助手应在此画布中提供小的侦听体验。 处理请求后，助手可以选择调整此视图的大小以显示上下文中答案，或将其应用于主应用视图以显示更大、更详细的视觉对象。

- 为了保持最小，预览没有标题栏，因此，**助手必须在右上方绘制 X，以允许客户关闭视图。** 若要在按下 "取消" 按钮时调用特定 Api，请参阅[关闭应用程序](windows-voice-assistants-implementation-guide.md#closing-the-application)。
- 为了支持语音激活预览，助手可能会邀请客户在首次运行时将助手固定到任务栏。

**语音激活预览：初始状态**

Contoso 助手在任务栏上有一个 home：其 swirling，圆形图标。

![Windows 语音助手的屏幕截图，作为任务栏图标预激活](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**激活过程**中，助手请求后台激活。 助手提供一个小型预览窗格（默认宽度为408，高度为：248）。 如果服务器端语音激活确定信号为误报，则可以消除此视图以实现最小中断。

![验证激活时，精简视图中的 Windows 语音助手屏幕截图](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**确认最终激活**后，助手会显示其侦听 UX。 助手必须始终在语音激活预览的右上方绘制一个消除 X。

![Windows 上的语音助手监听精简视图的屏幕截图](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

语音激活预览中可能会显示**快速答案**。 TryResizeView 将允许助手请求不同的大小。

![Windows 上的语音助手的屏幕截图回复](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**移交**。 随时，助手可能会向其主要的应用程序视图提供更多的信息、对话框或需要更多屏幕空间的答案。 有关实现的详细信息，请参阅[从 compact 视图过渡到完整视图](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view)部分。

![展开精简视图之前和之后在 Windows 上的语音助手屏幕截图](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [开始开发你的语音助手](how-to-windows-voice-assistants-get-started.md)