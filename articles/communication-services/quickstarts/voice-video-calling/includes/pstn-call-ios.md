---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 63fd0af819fde7d78df289a1b8f5cefa2e415101
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779863"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。
- 在通信服务资源中获取的电话号码。 [如何获取电话号码](../../telephony-sms/get-phone-number.md)。
- 用于启用呼叫客户端的`User Access Token`。 详细了解[如何获取`User Access Token`](../../access-tokens.md)
- 完成[开始向应用程序添加呼叫](../getting-started-with-calling.md)快速入门

### <a name="prerequisite-check"></a>先决条件检查

- 若要查看与通信服务资源关联的电话号码，请登录到 [Azure 门户](https://portal.azure.com/)，找到通信服务资源，然后从左侧导航窗格中打开“电话号码”选项卡。
- 可以通过适用于 iOS 的 Azure 通信服务呼叫客户端库来生成和运行应用：

## <a name="setting-up"></a>设置

## <a name="start-a-call-to-phone"></a>发起电话呼叫

指定在通信服务资源中获取的电话号码，该号码将用于发起呼叫：
> [!WARNING]
> 请注意，电话号码应采用 E.164 国际标准格式。 （例如：+12223334444）

修改点击“开始呼叫”按钮时执行的 `startCall` 事件处理程序：

```swift
func startCall() {
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            let startCallOptions = ACSStartCallOptions()
            startCallOptions!.alternateCallerID = PhoneNumber(phoneNumber: "+12223334444")
            self.call = self.callAgent!.call([PhoneNumber(phoneNumber: self.callee)], options: startCallOptions)
            self.callDelegate = CallDelegate(self)
            self.call!.delegate = self.callDelegate
        }
    }
}
```

## <a name="run-the-code"></a>运行代码

可以通过选择“产品” > “运行”或使用 (&#8984;-R) 键盘快捷方式，在 iOS 模拟器上生成并运行应用。

![快速入门应用的最终外观](../media/ios/quick-start-make-call.png)

可以通过在添加的文本字段中提供电话号码，然后单击“开始呼叫”按钮来拨打电话。
> [!WARNING]
> 请注意，电话号码应采用 E.164 国际标准格式。 （例如：+12223334444）

> [!NOTE]
> 首次发出呼叫时，系统将提示你提供麦克风访问权限。 在生产应用程序中，应使用 `AVAudioSession` API [检查权限状态](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)，并在未授予权限时正常更新应用程序的行为。
