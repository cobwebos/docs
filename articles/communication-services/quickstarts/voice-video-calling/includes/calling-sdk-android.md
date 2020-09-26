---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: aec9d2049a69aebc7102a70274e5fb2a3ef865a8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376801"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- `User Access Token`要启用调用客户端的。 有关[如何获取 `User Access Token` ](../../access-tokens.md)
- 可选：完成快速入门[添加对应用程序调用](../getting-started-with-calling.md)的入门教程

## <a name="setting-up"></a>设置

### <a name="install-the-package"></a>安装包

<!-- TODO: update with instructions on how to download, install and add package to project -->
找到项目级别 build.gradle，确保将 `mavenCentral()` 添加到 `buildscript` 和 `allprojects` 下的存储库列表中
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
然后，在模块级别的 gradle 中，将以下行添加到 "依赖项" 部分

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.1'
    ...
}

```

## <a name="object-model"></a>对象模型

以下类和接口处理 Azure 通信服务呼叫客户端库的某些主要功能：

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient 是呼叫客户端库的主入口点。|
| CallAgent | CallAgent 用于启动和管理呼叫。 |
| CommunicationUserCredential | CommunicationUserCredential 用作实例化 CallAgent 的令牌凭据。|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>初始化 CallClient 并创建 CallAgent，并访问 DeviceManager

若要创建 `CallAgent` 实例，必须对实例调用 `createCallAgent` 方法 `CallClient` 。 这会以异步方式返回 `CallAgent` 实例对象。
`createCallAgent`方法采用 `CommunicationUserCredential` 作为参数，用于封装[访问令牌](../../access-tokens.md)。
若要访问 `DeviceManager` ，必须首先创建一个 callAgent 实例，然后可以使用 `CallClient.getDeviceManager` 方法获取 DeviceManager。

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationUserCredential tokenCredential = new CommunicationUserCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = await callClient.createCallAgent((appContext, tokenCredential).get();
DeviceManage deviceManager = await callClient.getDeviceManager().get();
```

## <a name="place-an-outgoing-call-and-join-a-group-call"></a>发出传出呼叫并加入组呼叫

若要创建并启动调用，需要调用 `CallAgent.call()` 方法，并为 `Identifier` 被调用方 () 。
若要加入组调用，需要调用 `CallAgent.join()` 方法并提供 groupId。 组 Id 必须采用 GUID 或 UUID 格式。

调用创建和启动是同步的。 调用实例允许您订阅调用中的所有事件。

### <a name="place-a-11-call-to-a-user"></a>向用户发出1:1 调用
若要调用另一个通信服务用户，请 `call` 在上调用方法， `callAgent` 并通过键传递对象 `communicationUserId` 。
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUser acsUserId = new CommunicationUser(<USER_ID>);
CommunicationUser participants[] = new CommunicationUser[]{ acsUserId };
call oneToOneCall = callAgent.call(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>将1： n 调用与用户和 PSTN 一起使用
> [!WARNING]
> 当前不支持 PSTN 调用，无法对用户和 PSTN 号码进行1： n 调用，你必须指定被呼叫方的电话号码。
必须将通信服务资源配置为允许 PSTN 调用：
```java
CommunicationUser acsUser1 = new CommunicationUser(<USER_ID>);
PhoneNumber acsUser2 = new PhoneNumber("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.call(participants, startCallOptions);
```

### <a name="place-a-11-call-with-with-video-camera"></a>使用 with 视频相机发出1:1 呼叫
> [!WARNING]
> 目前仅支持一个传出的本地视频流来向视频发出呼叫，你必须使用 API 枚举本地相机 `deviceManager` `getCameraList` 。
选择所需的照相机后，可以使用它来构造 `LocalVideoStream` 实例，并将其 `videoOptions` 作为数组中的项传递 `localVideoStream` 给 `call` 方法。
呼叫连接后，会自动开始将视频流从所选照相机发送到其他 () 的参与者。
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

CommunicationUser[] participants = new CommunicationUser[]{ new CommunicationUser("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.call(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>加入组调用
若要启动新组调用或加入正在进行的组调用，必须调用 "join" 方法并使用属性传递对象 `groupId` 。 该值必须是 GUID。
```java
Context appContext = this.getApplicationContext();
GroupCallContext groupCallContext = new groupCallContext("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallContext, joinCallOptions);
```

## <a name="push-notification"></a>推送通知

### <a name="overview"></a>概述
移动推送通知是在移动设备上获取的弹出通知。 对于调用，我们将重点介绍 VoIP (通过 Internet 协议) 推送通知。 我们将为你提供注册推送通知的功能，处理推送通知并取消注册推送通知。

### <a name="prerequisite"></a>先决条件

本教程假定你已设置了 Firebase 帐户，且已启用云消息 (FCM) ，Firebase 云消息传递连接到 Azure 通知中心 (ANH) 实例。 有关详细信息，请参阅 [将 Firebase 连接到 Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started) 。
此外，本教程假定你使用 Android Studio 版本3.6 或更高版本来生成应用程序。

Android 应用程序需要一组权限才能接收来自 FCM 的通知消息。 在 AndroidManifest.xml 文件中，在 *<清单 ... >* 或标记下面添加以下权限集 *</application>*

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notification"></a>注册推送通知

- 若要注册推送通知，应用程序需要在具有设备注册令牌的 *CallAgent* 实例上调用 registerPushNotification ( # A1。

- 如何获取设备注册令牌
1. 请确保将 Firebase 客户端库添加到应用程序模块的 *gradle* 文件，方法是在 " *依赖关系* " 部分中添加以下行（如果尚未存在）：
```
    // Add the client library for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

2. 在项目级别的 *gradle* 文件中，在 " *依赖关系* " 部分中添加以下内容（如果尚未存在）
```
    classpath 'com.google.gms:google-services:4.3.3'
```

3. 如果文件中尚不存在以下插件，请将其添加到文件的开头
```
apply plugin: 'com.google.gms.google-services'
```

4. 选择工具栏中的 " *立即同步* "

5. 添加以下代码片段，以获取由 FCM 客户端应用程序实例生成的客户端应用程序实例的设备注册令牌 
- 将这些导入添加到实例的主活动的标头中。 它们是代码片段检索令牌所必需的
```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```
- 添加此代码片段以检索令牌
```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w(TAG, "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d(TAG, "Device Registration token retrieved successfully");
                    }
                });
```
6. 向传入呼叫的调用服务客户端库注册设备注册令牌推送通知

```java
String deviceRegistrationToken = "some_token";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>推送通知处理

- 若要接收传入的调用推送通知，请在具有有效负载的*CallAgent*实例上调用*HandlePushNotification ( # B1* 。

1. 若要从 FCM 获取有效负载，需执行以下步骤：
- > 新的 > 服务 > Service) 中创建新的服务 (文件，该服务将扩展 *FirebaseMessagingService* Firebase 客户端库类，并确保重写 *onMessageReceived* 方法。 此方法是在 FCM 将推送通知传递到应用程序时调用的事件处理程序。

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageData;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageData = serializeDictionaryAsJson(remoteMessage.getData());
        }
    }
}
```
- 还将以下服务定义添加到标记内的 AndroidManifest.xml 文件中 <application> 。

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- 检索有效负载后，可以通过对*CallAgent*实例调用*handlePushNotification*方法，将其传递给*通信服务*客户端库。

```java
java.util.Map<String, String> pushNotificationMessageDataFromFCM = remoteMessage.getData();
try {
    callAgent.handlePushNotification(pushNotificationMessageDataFromFCM).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```
当推送通知消息的处理成功并且所有事件处理程序都已正确注册时，应用程序将会响铃。

### <a name="unregister-push-notification"></a>注销推送通知

- 应用程序可以随时取消注册推送通知。 `unregisterPushNotification()`在 callAgent 上调用方法以注销。

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>呼叫管理
你可以访问调用属性并在调用以管理与视频和音频相关的设置期间执行各种操作。

### <a name="call-properties"></a>调用属性
* 获取此调用的唯一 ID。
```java
String callId = call.getCallId();
```

* 若要了解有关调用中的其他参与者，请检查 `remoteParticipant` 该实例上的集合 `call` ：
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

* 如果调用是传入的，则为调用方的标识。
```java
CommunicationIdentifier callerId = call.getCallerId();
```

* 获取调用的状态。
```java
CallState callState = call.getState();
```
它返回表示调用的当前状态的字符串：
* "无"-初始调用状态
* "传入"-表示调用是传入的，它必须接受或拒绝
* "正在连接"-在调用或接受调用后初始转换状态
* "震铃"-对于传出呼叫，表示呼叫正在拨打远程参与者，这是 "传入" 的一方
* "EarlyMedia"-表示在连接呼叫之前播放公告的状态
* "已连接"-调用已连接
* "保持"-呼叫处于暂挂状态，本地终结点与远程参与者之间没有媒体流动 (s) 
* "断开"-在调用进入 "断开连接" 状态之前转换状态
* "Disconnected"-最终调用状态


* 若要了解调用结束的原因，请检查 `callEndReason` 属性。
它包含代码/子代码 (TODO 链接到文档) 
```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

* 若要查看当前调用是否为传入呼叫，请检查 `isIncoming` 属性：
```java
boolean isIncoming = call.getIsIncoming();
```

*  若要查看当前麦克风是否静音，请检查 `muted` 属性：
```java
boolean muted = call.getIsMicrophoneMuted();
```

* 若要检查活动视频流，请检查 `localVideoStreams` 集合：
```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>静音和取消静音
若要使本地终结点静音或取消静音，你可以使用 `mute` 和 `unmute` 异步 api：
```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>开始和停止发送本地视频
若要开始视频，必须 `getCameraList` 在对象上使用 API 来枚举摄像机 `deviceManager` 。
然后创建 `LocalVideoStream` 传递所需照相机的新实例，并将其 `startVideo` 作为自变量传递到 API
```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
videoOptions = new VideoOptions(currentVideoStream);
Future startVideoFuture = call.startVideo(currentVideoStream);
startVideoFuture.get();
```

成功开始发送视频后， `LocalVideoStream` 实例将添加到 `localVideoStreams` 调用实例上的集合中。
```java
currentVideoStream == call.getLocalVideoStreams().get(0);
```

若要停止本地视频，请 `localVideoStream` 在集合中传递可用的实例 `localVideoStreams` ：
```java
call.stopVideo(localVideoStream).get();
```

通过对实例调用来发送视频时，可以切换到不同的相机设备 `switchSource` `localVideoStream` ：
```java
localVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>远程参与者管理

所有远程参与者都按 `RemoteParticipant` 类型表示，可通过 `remoteParticipants` 调用实例上的集合来使用。

### <a name="list-participants-in-a-call"></a>列出调用中的参与者
`remoteParticipants`集合返回给定调用中的远程参与者列表：
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>远程参与者属性
任何给定的远程参与者都具有与之关联的一组属性和集合：

* 获取此远程参与者的标识符。
标识是一个 "标识符" 类型
```java
CommunicationIdentifier participantIdentity = remoteParticipant.getIdentifier();
```

* 获取此远程参与者的状态。
```java
ParticipantState state = remoteParticipant.getState();
```
状态可以是
* "空闲"-初始状态
* 参与者连接到呼叫时的 "正在连接"-转换状态
* "已连接"-参与者已连接到呼叫
* "保持"-参与者处于暂停状态
* "EarlyMedia"-在参与者连接到呼叫之前播放公告
* "断开连接"-最终状态-参与者已与呼叫断开连接


* 若要了解参与者离开呼叫的原因，请检查 `callEndReason` 属性：
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* 若要检查此远程参与者是否已静音，请检查 `isMuted` 属性：
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* 若要检查此远程参与者是否正在通话，请检查 `isSpeaking` 属性：
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* 若要检查给定参与者在此调用中发送的所有视频流，请检查 `videoStreams` 集合：
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>向呼叫添加参与者

若要将参与者添加到呼叫 (可以调用的用户或电话号码) `addParticipant` 。 这会以同步方式返回远程参与者实例。

```java
const acsUser = new CommunicationUser("<acs user id>");
const acsPhone = new PhoneNumber("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone);
```

### <a name="remove-participant-from-a-call"></a>从呼叫中删除参与者
若要从调用中删除参与者 (可以调用的用户或电话号码) `removeParticipant` 。
这会在从调用中删除参与者后异步解决。
还将从集合中删除该参与者 `remoteParticipants` 。
```java
RemoteParticipant remoteParticipant = call.getParticipants().get(0);
call.removeParticipant(acsUser).get();
call.removeParticipant(acsPhone).get();
```

## <a name="render-remote-participant-video-streams"></a>呈现远程参与者视频流
若要列出视频流和远程参与者的屏幕共享流，请检查 `videoStreams` 集合：
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
若要 `RemoteVideoStream` 从远程参与者呈现，必须订阅 `OnVideoStreamsUpdated` 事件。
在此事件中，将 `isAvailable` 属性更改为 true 表示远程参与者当前正在发送流一次，创建新的实例 `Renderer` ，然后使用异步 API 创建新的， `RendererView` `createView` 并附加到 `view.target` 应用程序 UI 中的任意位置。
当远程流的可用性发生变化时，你可以选择销毁整个呈现器、特定 `RendererView` 或保留它们，但这将导致显示空白的视频帧。

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>远程视频流属性
远程视频流具有几个属性

* `Id` -远程视频流的 ID
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` -可以是 "视频" 或 "ScreenSharing"
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` -指示远程参与者终结点是否正在主动发送流
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>呈现器方法和属性
以下 Api 的呈现器对象

* 创建一个 `RendererView` 可在以后附加到应用程序 UI 以呈现远程视频流的实例。
```java
// Create a view for a video stream
renderer.createView()
```
* Dispose 呈现器和 `RendererView` 与此呈现器相关联的所有
```java
renderer.dispose()
```

* `StreamSize` -size ( 远程视频流的宽度/高度 ) 
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>RendererView 方法和属性
创建时， `RendererView` 可以指定 `scalingMode` `mirrored` 将应用于此视图的和属性：缩放模式可以是 "Stretch" |"裁剪" |"拟合" 如果将 `mirrored` 设置为 `true` ，则呈现的流将垂直翻转。

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

然后，可以使用以下代码片段将创建的 RendererView 附加到应用程序 UI：
```java
layout.addView(rendererView);
```

稍后可以通过 `updateScalingMode` 使用 ScalingMode 之一的 RendererView 对象调用 API 来更新缩放模式 |ScalingMode |ScalingMode 作为一个参数。
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>设备管理

`DeviceManager` 允许你枚举可在调用中用于传输音频/视频流的本地设备。 它还允许你请求用户使用本机浏览器 API 访问其麦克风和照相机的权限。

可以 `deviceManager` 通过调用方法访问 `callClient.getDeviceManager()` 。
> [!WARNING]
> 当前 `callAgent` 必须首先实例化对象，才能获得对 DeviceManager 的访问权限

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>枚举本地设备

若要访问本地设备，可以在设备管理器上使用枚举方法。 枚举是同步操作。

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>设置默认麦克风/发言人

使用设备管理器，可以设置一个默认设备，该设备将在启动呼叫时使用。
如果未设置客户端默认值，则通信服务将回退到 OS 默认值。

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophoneList().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakerList().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>本地相机预览

你可以使用 `DeviceManager` 和 `Renderer` 从本地相机开始呈现流。 此流不会发送给其他参与者;这是一个本地预览源。 这是一个异步操作。

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the renderingSurface to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>事件模型
您可以订阅大多数属性和集合，以便在值发生更改时收到通知。

### <a name="properties"></a>属性
订阅 `property changed` 事件：

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnCallStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnCallStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>集合
订阅 `collection updated` 事件：

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
