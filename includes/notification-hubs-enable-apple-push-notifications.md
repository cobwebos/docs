---
title: include 文件
description: include 文件
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bf2596f5a8e287799285f97f3d1be9f3fe10f644
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77123207"
---
## <a name="generate-the-certificate-signing-request-file"></a>生成证书签名请求文件

Apple Push Notification 服务 (APNs) 使用证书对推送通知进行身份验证。 请遵照这些说明来创建用于发送和接收通知的所需推送证书。 有关这些概念的详细信息，请参阅正式的 [Apple Push Notification 服务](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)文档。

生成证书签名请求 (CSR) 文件，Apple 将使用该文件生成签名的推送证书。

1. 在 Mac 上，运行 Keychain Access 工具。 可以从启动台上的“Utilities”或“Other”文件夹中打开该工具。  

1. 选择“Keychain Access”，展开“Certificate Assistant”（证书助理），然后选择“Request a Certificate from a Certificate Authority”（从证书颁发机构请求证书）。   

    ![使用 Keychain Access 请求新证书](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > 默认情况下，Keychain Access 选择列表中的第一项。 如果你位于“Certificates”（证书）  类别中，并且“Apple Worldwide Developer Relations Certification Authority”（Apple 全球开发者关系证书颁发机构）  不是列表中的第一项，这可能会是个问题。 在生成 CSR（证书签名请求）之前，请确保已有非密钥项，或者已选择“Apple Worldwide Developer Relations Certification Authority”（Apple 全球开发者关系证书颁发机构）  密钥。

1. 选择“User Email Address”（用户电子邮件地址），输入“Common Name”（公用名）值，确保指定“Saved to disk”（保存到磁盘），然后选择“Continue”（继续）。     将“CA Email Address”（CA 电子邮件地址）留空，因为它不是必填字段  。

    ![所需证书信息](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. 在“Save As”（另存为）中为 CSR 文件输入一个名称，在“Where”（位置）中选择位置，然后选择“Save”（保存）。   

    ![为证书选择一个文件名](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    此操作会将 CSR 文件保存到选定位置。 默认位置为“桌面”。  请记住为此文件选择的位置。

接下来，向 Apple 注册应用，启用推送通知并上传导出的 CSR，以便创建推送证书。

## <a name="register-your-app-for-push-notifications"></a>为推送通知注册应用程序

若要将推送通知发送到 iOS 应用，请向 Apple 注册应用程序，还要注册推送通知。  

1. 如果尚未注册应用，请浏览到 Apple 开发人员中心的 [iOS 预配门户](https://go.microsoft.com/fwlink/p/?LinkId=272456)。 使用你的 Apple ID 登录到门户，然后选择“标识符”  。 然后选择 **+** 注册新应用。

    ![iOS 预配门户应用 ID 页](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. 在“注册新的标识符”  屏幕上，选择“应用 ID”  单选按钮。 然后选择“继续”。 

    ![iOS 预配门户“注册新 ID”页](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. 更新新应用的以下三个值，然后选择“Continue”（继续）： 

   * **说明**：键入应用的描述性名称。

   * **捆绑 ID**：按[应用分发指南](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)中所述，输入格式为 **Organization Identifier.Product Name** 的捆绑 ID。 “Organization Identifier”（组织标识符）和“Product Name”（产品名称）值必须与创建 Xcode 项目时使用的组织标识符与产品名称匹配。   在下面的屏幕截图中，**NotificationHubs** 值用作组织标识符，**GetStarted** 值用作产品名称。 确保“Bundle Identifier”（捆绑标识符）值与 Xcode 项目中的值匹配，这样 Xcode 就可以使用正确的发布配置文件。 

      ![iOS 预配门户“注册应用 ID”页](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **推送通知**：在“Capabilities”（功能）部分选中“Push Notifications”（推送通知）选项   。

      ![用于注册新应用 ID 的窗体](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      此操作会生成应用 ID 并请求你确认信息。 选择“Continue”（继续），然后选择“Register”（注册）   以确认新的应用 ID。

      ![确认新的应用 ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      选择“Register”（注册）  后，新的应用 ID 将作为行项出现在“Certificates, Identifiers & Profiles”（证书、标识符和配置文件）  页中。

4. 在“Certificates, Identifiers & Profiles”（证书、标识符和配置文件）  页的“Identifiers”（标识符）  下，找到你刚刚创建的应用 ID 行项，并选择其所在的行以显示“Edit your App ID Configuration”（编辑应用 ID 配置）  屏幕。

5. 向下滚动到选中的“Push Notifications”（推送通知）  选项，然后选择“Configure”（配置）  以创建证书。

    ![编辑应用 ID 页](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

6. 此时将显示“Apple Push Notification service SSL Certificates”（Apple 推送通知服务 SSL 证书）  窗口。 选择“Development SSL Certificate”（开发 SSL 证书）  部分下的“Create Certificate”（创建证书）  按钮。

    ![“为应用 ID 创建证书”按钮](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    此时将显示“Create a new Certificate”（创建新证书）  屏幕。

    > [!NOTE]
    > 本教程使用开发证书。 注册生产证书时使用相同的过程。 只需确保在发送通知时使用相同的证书类型。

1. 选择“Choose File”（选择文件），浏览到在第一个任务中保存 CSR 文件的位置，然后双击证书名以加载该证书。  然后选择“继续”。 

1. 当门户创建证书后，请选择“Download”（下载）按钮。  保存证书，并记住保存证书的位置。

    ![已生成证书的下载页](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    这将下载证书并将其保存到计算机的 **Downloads** 文件夹。

    ![在 Downloads 文件夹中找到证书文件](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > 默认情况下，下载的开发证书名为 **aps_development.cer**。

1. 双击下载的推送证书 **aps_development.cer**。 此操作将在密钥链中安装新证书，如下图所示：

    ![Keychain Access 证书列表，显示了新证书](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > 证书中的名称可能会不同，但会以 **Apple Development iOS Push Services** 作为前缀。

1. 在 Keychain Access 中，右键单击在“Certificates”（证书）类别中创建的新推送证书。  选择“Export”（导出），为文件命名，选择“.p12”格式，并选择“Save”（保存）。   

    ![将证书作为 p12 格式导出](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    可以选择使用密码保护证书，但这是可选的。 如果要跳过密码创建，请单击“OK”（确定）  。 记下导出的 .p12 证书的文件名和位置。 它们用于启用 APNs 身份验证。

    > [!NOTE]
    > 你的 .p12 文件名和位置可能不同于本教程中所示的名称和位置。

## <a name="create-a-provisioning-profile-for-the-app"></a>为应用程序创建配置文件

1. 返回到 [iOS 预配门户](https://go.microsoft.com/fwlink/p/?LinkId=272456)，选择“Certificates, Identifiers & Profiles”（证书、标识符和配置文件）  ，从左侧菜单中选择“Profiles”（配置文件）  ，然后选择 **+** 创建新的配置文件。 此时将显示“Register a New Provisioning Profile”（注册新的预配配置文件）  屏幕。

1. 选择“Development”（开发）下的“iOS App Development”（iOS 应用程序开发）作为预配配置文件类型，然后选择“Continue”（继续）。   

    ![预配配置文件列表](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. 接下来，从“App ID”（应用 ID）下拉列表中选择创建的应用 ID，然后选择“Continue”（继续）。  

    ![选择应用 ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. 在“Select certificates”（选择证书）窗口中，选择用于代码签名的开发证书，然后选择“Continue”（继续）。   此证书不是所创建的推送证书。 如果不存在，则必须创建一个。 如果证书存在，请跳到下一步。 若要创建开发证书（如果开发证书不存在），请执行以下操作：

    1. 如果看到“No Certificates are available”（没有可用的证书）  ，请选择“Create Certificate”（创建证书）  。
    2. 在“Software”（软件）  部分中，选择“Apple Development”（Apple 开发）  。 然后选择“继续”。 
    3. 在“Create a new Certificate”（创建新证书）  屏幕中，选择“Choose File”（选择文件）  。
    4. 浏览到之前创建的“Certificate Signing Request”（证书签名请求）  证书，选择该证书，然后选择“Open”（打开）  。
    5. 选择“继续”。 
    6. 下载开发证书，并记住其保存位置。

1. 返回到“Certificates, Identifiers & Profiles”（证书、标识符和配置文件）  页，从左侧菜单中选择“Profiles”（配置文件）  ，然后选择 **+** 创建新的配置文件。 此时将显示“Register a New Provisioning Profile”（注册新的预配配置文件）  屏幕。

1. 在“Select certificates”（选择证书）窗口中，选择你刚刚创建的开发证书。  然后选择“继续”。 

1. 接下来，选择用于测试的设备，然后选择“Continue”（继续）。 

1. 最后，在“Provisioning Profile Name”（预配配置文件名称）中为概要文件选择一个名称，然后选择“Generate”（生成）。  

    ![选择预配配置文件名称](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. 创建了新的预配配置文件后，选择“Download”（下载）  。 记住保存证书的位置。

1. 浏览到预配配置文件所在的位置，然后双击该配置文件以将其安装在 Xcode 开发计算机上。

## <a name="create-a-notification-hub"></a>创建通知中心

在本部分，我们创建一个通知中心，并使用以前创建的 .p12 推送证书配置 APNs 身份验证。 如果想要使用已创建的通知中心，可以跳到步骤 5。

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>使用 APNs 信息配置通知中心

1. 在“通知服务”下选择“Apple (APNS)”。  

1. 选择“证书”。 

1. 选择文件图标。

1. 选择前面导出的 .p12 文件，然后选择“Open”（打开）  。

1. 如果需要，请指定正确的密码。

1. 选择“沙盒”  模式。 仅当希望将推送通知发送给从应用商店购买应用的用户时，才应使用“生产”模式。 

    ![在 Azure 门户中配置 APNs 证书](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. 选择“保存”。 

现在已使用 APNs 配置通知中心。 此外还有了用于注册应用和发送推送通知的连接字符串。
