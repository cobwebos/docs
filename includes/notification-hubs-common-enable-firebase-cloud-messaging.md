---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 71e4b0b3b7bf84938123acf70ac18a93c3015a4d
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095191"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>创建 Firebase 项目并为 Android 启用 Firebase Cloud Messaging

1. 登录到 [Firebase 控制台](https://firebase.google.com/console/)。 创建一个新的 Firebase 项目，输入 PushDemo 作为项目名称 。

    > [!NOTE]
    > 将生成唯一名称。 默认情况下，此名称由提供的名称小写变体和用短划线分隔的生成数字组成。 如果需要可以对其进行更改，但前提是它仍是全局唯一名称。

1. 创建项目后，选择“向 Android 应用添加 Firebase”。

    ![将 Firebase 添加到 Android 应用](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

1. 在“将 Firebase 添加到 Android 应用”页上，执行以下步骤。
    1. 对于“Android 包名称”，请输入包的名称。 例如：`com.<organization_identifier>.<package_name>`。

        ![指定程序包名称](./media/notification-hubs-common-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    1. 选择“注册应用”。  
    1. 选择“下载 google-services.json”。 然后将该文件保存到本地文件夹中供稍后使用，并选择“下一步”。  

        ![下载 google-services.json](./media/notification-hubs-common-enable-firebase-cloud-messaging/download-google-service-button.png)
    1. 选择“**下一页**”。
    1. 选择“继续访问控制台”

        > [!NOTE]
        > 如果由于“验证安装”检查未启用“继续访问控制台”按钮，请选择“跳过此步骤”。

1. 在 Firebase 控制台中，选择与项目相对应的齿轮图标。 然后，选择“项目设置”。

    ![选择“项目设置”](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > 如果尚未下载 google-services.json 文件，可以在此页上进行下载。

1. 切换到顶部的“Cloud Messaging”选项卡。 复制并保存**服务器密钥**以供将来使用。 你将使用此值来配置通知中心。

    ![复制服务器密钥](./media/notification-hubs-common-enable-firebase-cloud-messaging/server-key.png)
