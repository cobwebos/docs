---
title: include 文件
description: include 文件
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c664e73b39ad48a860661cfd9141ee74df203f3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116648"
---
## <a name="generate-the-certificate-signing-request-file"></a>生成证书签名请求文件

Apple Push Notification 服务 (APNs) 使用证书对推送通知进行身份验证。 请遵照这些说明来创建用于发送和接收通知的所需推送证书。 有关这些概念的详细信息，请参阅正式的 [Apple Push Notification 服务](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)文档。

生成证书签名请求 (CSR) 文件，Apple 将使用该文件生成签名的推送证书。

1. 在 Mac 上，运行 Keychain Access 工具。 可以从启动台上的“Utilities”或“Other”文件夹中打开该工具。  

1. 选择“Keychain Access”，展开“Certificate Assistant”（证书助理），然后选择“Request a Certificate from a Certificate Authority”（从证书颁发机构请求证书）。   

    ![使用 Keychain Access 请求新证书](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. 选择“User Email Address”（用户电子邮件地址），输入“Common Name”（公用名）值，确保指定“Saved to disk”（保存到磁盘），然后选择“Continue”（继续）。     将“CA Email Address”（CA 电子邮件地址）留空，因为它不是必填字段  。

    ![所需证书信息](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. 在“Save As”（另存为）中为 CSR 文件输入一个名称，在“Where”（位置）中选择位置，然后选择“Save”（保存）。   

    ![为证书选择一个文件名](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    此操作会将 CSR 文件保存到选定位置。 默认位置为“桌面”。  请记住为此文件选择的位置。

接下来，向 Apple 注册应用，启用推送通知并上传导出的 CSR，以便创建推送证书。

## <a name="register-your-app-for-push-notifications"></a>为推送通知注册应用程序

若要将通知推送到 iOS 应用，请向 Apple 注册应用程序，还要注册推送通知。  

1. 如果尚未注册应用，请浏览到 Apple 开发人员中心的 [iOS 预配门户](https://go.microsoft.com/fwlink/p/?LinkId=272456)。 然后，使用 Apple ID 登录，依次选择“Identifiers”（标识符）、“App IDs”（应用 ID）、“+”，以便注册新应用。   

    ![iOS 预配门户应用 ID 页](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. 更新新应用的以下三个值，然后选择“Continue”（继续）： 

   * **名称**：在“App ID Description”（应用 ID 说明）部分的“Name”（名称）框中为应用键入一个描述性名称   。

   * **捆绑标识符**：在“Explicit App ID”（显式应用 ID）部分，使用[应用分发指南](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)中所述的 `<Organization Identifier>.<Product Name>` 格式输入“Bundle Identifier”（捆绑标识符）   。 “Organization Identifier”（组织标识符）和“Product Name”（产品名称）值必须与创建 Xcode 项目时使用的组织标识符与产品名称匹配。   在下面的屏幕截图中，*NotificationHubs* 值用作组织标识符，*GetStarted* 值用作产品名称。 确保“Bundle Identifier”（捆绑标识符）值与 Xcode 项目中的值匹配，这样 Xcode 就可以使用正确的发布配置文件。 

   * **推送通知**：在“App Services”（应用服务）部分选中“Push Notifications”（推送通知）选项   。

     ![用于注册新应用 ID 的窗体](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     此操作会生成应用 ID 并请求你确认信息。 选择“Register”（注册）  ，确认新的应用 ID。

     选择“Register”（注册）后，会看到“Registration complete”（注册已完成）屏幕，如下图所示。   选择“完成”  。

     ![应用 ID 注册完成，显示了权利](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. 在开发人员中心的“App IDs”（应用 ID）下，找到创建的应用 ID，并选择它所在的行。 

    ![应用 ID 列表](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    选择用于显示应用详细信息的应用 ID，然后选择底部的“Edit”（编辑）按钮。 

    ![编辑应用 ID 页](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. 滚动到屏幕底部并选择“Development Push SSL Certificate”（开发推送 SSL 证书）部分下的“Create Certificate”（创建证书）按钮。  

    ![“为应用 ID 创建证书”按钮](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    现在会显示“Add iOS Certificate”（添加 iOS 证书）助手。 

    > [!NOTE]
    > 本教程使用开发证书。 注册生产证书时使用相同的过程。 只需确保在发送通知时使用相同的证书类型。

1. 选择“Choose File”（选择文件），浏览到在第一个任务中保存 CSR 文件的位置，然后选择“Generate”（生成）。  

    ![已生成证书的 CSR 上传页](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. 门户创建证书后，请选择“Download”（下载）按钮，然后选择“Done”（完成）。  

    ![已生成证书的下载页](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    这将下载证书并将其保存到计算机的 **Downloads** 文件夹。

    ![在 Downloads 文件夹中找到证书文件](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > 默认情况下，下载的开发证书名为 **aps_development.cer**。

1. 选择下载的推送证书 **aps_development.cer**。

    此操作将在密钥链中安装新证书，如下图所示：

    ![Keychain Access 证书列表，显示了新证书](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > 证书中的名称可能会不同，但会以 **Apple Development iOS Push Services** 作为前缀。

1. 在 Keychain Access 中，右键单击在“Certificates”（证书）类别中创建的新推送证书。  选择“Export”（导出），为文件命名，选择“.p12”格式，并选择“Save”（保存）。   

    ![将证书作为 p12 格式导出](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    记下导出的 .p12 证书的文件名和位置。 它们用于启用 APNs 身份验证。

    > [!NOTE]
    > 本教程创建名为 **QuickStart.p12** 的文件。 文件名和位置可能不同。

## <a name="create-a-provisioning-profile-for-the-app"></a>为应用程序创建配置文件

1. 在 [iOS 预配门户](https://go.microsoft.com/fwlink/p/?LinkId=272456)中，依次选择“Provisioning Profiles”（预配配置文件）、“All”（全部）、“+”，以便创建新的配置文件。    此时会显示“Add iOS Provisiong Profile”（添加 iOS 预配配置文件）向导。 

    ![预配配置文件列表](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. 在“Development”（开发）下选择“iOS App Development”（iOS 应用开发）作为预配配置文件类型，然后选择“Continue”（继续）。   

1. 接下来，从“App ID”（应用 ID）下拉列表中选择创建的应用 ID，然后选择“Continue”（继续）。  

    ![选择应用 ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. 在“Select certificates”（选择证书）窗口中，选择经常用于代码签名的开发证书，然后选择“Continue”（继续）。   此证书不是所创建的推送证书。

    ![选择证书](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. 接下来，选择用于测试的设备，然后选择“Continue”（继续）。 

    ![选择设备](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. 最后，在“Profile Name”（配置文件名称）中为配置文件选取一个名称，然后选择“Generate”（生成）。  

    ![选择预配配置文件名称](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. 创建新的预配配置文件后，请选择下载该文件并将其安装在 Xcode 开发计算机上。 然后选择“完成”  。

    ![下载预配配置文件](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>创建通知中心

在本部分，我们创建一个通知中心，并使用以前创建的 .p12 推送证书配置 APNs 身份验证。 如果想要使用已创建的通知中心，可以跳到步骤 5。

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>使用 APNs 信息配置通知中心

1. 在“通知服务”下选择“Apple (APNS)”。  

1. 选择“证书”。 

1. 选择文件图标。

1. 选择前面导出的 .p12 文件。

1. 指定正确的密码。

1. 选择“沙盒”  模式。 仅当希望将推送通知发送给从应用商店购买应用的用户时，才应使用“生产”模式。 

    ![在 Azure 门户中配置 APNs 证书](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

现在已使用 APNs 配置通知中心。 此外还有了用于注册应用和发送推送通知的连接字符串。
