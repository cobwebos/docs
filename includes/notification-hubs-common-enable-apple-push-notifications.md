---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72065ce602c6d29255a3500e26d8c6f36a19ebed
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095190"
---
### <a name="register-your-ios-app-for-push-notifications"></a>为推送通知注册 iOS 应用

若要将推送通知发送到 iOS 应用，请向 Apple 注册应用程序，还要注册推送通知。  

1. 如果尚未注册应用，请浏览到 Apple 开发人员中心的 [iOS 预配门户](https://go.microsoft.com/fwlink/p/?LinkId=272456)。 使用 Apple ID 登录门户，导航到“证书、标识符和配置文件”，然后选择“标识符” 。 单击“+”注册新应用。

    ![iOS 预配门户应用 ID 页](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. 在“注册新的标识符”屏幕上，选择“应用 ID”单选按钮。 然后选择“继续”。

    ![iOS 预配门户“注册新 ID”页](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

1. 更新新应用的以下三个值，然后选择“Continue”（继续）：

   * **说明**：键入应用的描述性名称。

   * **捆绑 ID**：按[应用分发指南](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)中所述，输入格式为 com.<organization_identifier>.<product_name> 的捆绑 ID。 在下面的屏幕截图中，`mobcat` 值用作组织标识符，PushDemo 值用作产品名称。

      ![iOS 预配门户“注册应用 ID”页](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **推送通知**：在“Capabilities”（功能）部分选中“Push Notifications”（推送通知）选项 。

      ![用于注册新应用 ID 的窗体](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      此操作会生成应用 ID 并请求你确认信息。 选择“Continue”（继续），然后选择“Register”（注册） 以确认新的应用 ID。

      ![确认新的应用 ID](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      选择“Register”（注册）后，新的应用 ID 将作为行项出现在“Certificates, Identifiers & Profiles”（证书、标识符和配置文件）页中。

1. 在“证书、标识符和配置文件”页上的“标识符”下，找到创建的应用 ID 行项 。 然后，选择其行以显示“编辑应用 ID 配置”屏幕。

#### <a name="creating-a-certificate-for-notification-hubs"></a>为通知中心创建证书

若要使通知中心能够使用 Apple Push Notification 服务 (APNS)，需要使用证书，可以通过以下两种方式之一提供证书：

1. [创建可以直接上传到通知中心的 p12 推送证书](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub)（原始方法）

1. [创建可用于基于令牌的身份验证的 p8 证书](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication)（推荐的较新方法）

如[基于令牌 (HTTP/2) 的 APNS 身份验证](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification)中所述，较新的方法有很多好处。 所需的步骤较少，但一些步骤对于特定的场景来说也是必需的。 不过，这两种方法的步骤均已提供，因为在本教程中，这两种方法均适用。

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>选项 1：创建可以直接上传到通知中心的 p12 推送证书

1. 在 Mac 上，运行 Keychain Access 工具。 可以从启动台上的“Utilities”或“Other”文件夹中打开该工具。 

1. 选择“Keychain Access”，展开“Certificate Assistant”（证书助理），然后选择“Request a Certificate from a Certificate Authority”（从证书颁发机构请求证书）。  

    ![使用 Keychain Access 请求新证书](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > 默认情况下，Keychain Access 选择列表中的第一项。 如果你位于“Certificates”（证书）类别中，并且“Apple Worldwide Developer Relations Certification Authority”（Apple 全球开发者关系证书颁发机构）不是列表中的第一项，这可能会是个问题。 在生成 CSR（证书签名请求）之前，请确保已有非密钥项，或者已选择“Apple Worldwide Developer Relations Certification Authority”（Apple 全球开发者关系证书颁发机构）密钥。

1. 选择“User Email Address”（用户电子邮件地址），输入“Common Name”（公用名）值，确保指定“Saved to disk”（保存到磁盘），然后选择“Continue”（继续）。    将“CA Email Address”（CA 电子邮件地址）留空，因为它不是必填字段。

    ![预期的证书信息](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. 在“另存为”中为证书签名请求 (CSR) 文件输入一个名称，在“位置”中选择一个位置，并选择“保存”   。

    ![为证书选择一个文件名](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-save-csr.png)

    此操作会将 CSR 文件保存到选定位置。 默认位置为“桌面”。 请记住为此文件选择的位置。

1. 返回到 [iOS 预配门户](https://go.microsoft.com/fwlink/p/?LinkId=272456)的“证书、标识符和配置文件”页，向下滚动到已选择的“推送通知”选项，然后选择“配置”来创建证书  。

    ![编辑应用 ID 页](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. 此时将显示“Apple Push Notification 服务 TLS/SSL 证书”窗口。 选择“开发 TLS/SSL 证书”部分下的“创建证书”按钮 。

    ![“为应用 ID 创建证书”按钮](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    此时将显示“Create a new Certificate”（创建新证书）屏幕。

    > [!NOTE]
    > 本教程使用开发证书。 注册生产证书时使用相同的过程。 只需确保在发送通知时使用相同的证书类型。

1. 选择“选择文件”，浏览到保存 CSR 文件的位置，然后双击证书名以加载该证书 。 然后选择“继续”。

1. 当门户创建证书后，请选择“Download”（下载）按钮。 保存证书，并记住保存证书的位置。

    ![已生成证书的下载页](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    这将下载证书并将其保存到计算机的 **Downloads** 文件夹。

    ![在 Downloads 文件夹中找到证书文件](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > 默认情况下，下载的开发证书名为 **aps_development.cer**。

1. 双击下载的推送证书 **aps_development.cer**。 此操作将在密钥链中安装新证书，如下图所示：

    ![Keychain Access 证书列表，显示了新证书](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > 证书中的名称可能有所不同，但会以 Apple Development iOS Push Services 作为前缀，并且关联有相应捆绑标识符。

1. 在 Keychain Access 中，按住 Control 的同时单击在“证书”类别中创建的新推送证书  。 选择“导出”，为文件命名，选择“p12”格式，并选择“保存”。  

    ![将证书作为 p12 格式导出](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    可以选择使用密码保护证书，但这是可选的。 如果要跳过密码创建，请单击“OK”（确定）。 记下导出的 p12 证书的文件名和位置。 它们用于启用 APNS 身份验证。

    > [!NOTE]
    > 你的 p12 文件名和位置可能不同于本教程中所示的名称和位置。

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>选项 2：创建可用于基于令牌的身份验证的 p8 证书

1. 请记下以下详细信息：

    * 应用 ID 前缀（团队 ID ）
    * 捆绑包 ID

1. 返回到“证书、标识符和配置文件”，单击“密钥”。

   > [!NOTE]
   > 如果已为 APNS 配置了密钥，则可以重复使用在创建后立即下载的 p8 证书。 如果是这样，则可以忽略步骤 3 到步骤 5。

1. 单击 **+** 按钮（或“创建密钥”按钮）以创建新密钥。
1. 提供合适的“密钥名称”值，选中“Apple Push Notification 服务(APNS)”选项，然后单击“继续”，接下来在下一个屏幕上单击“注册”。
1. 单击“下载”，然后将 p8 文件（前缀为 AuthKey_）移动到安全的本地目录，然后单击“完成”。

   > [!NOTE]
   > 请确保将 p8 文件保存在安全的位置（并保存备份）。 密钥在下载后无法重新下载，因为服务器副本已删除。
  
1. 在“密钥”上，单击创建的密钥（如果已选择使用现有密钥，则改为单击现有密钥）。
1. 记下“密钥 ID”值。
1. 在所选的合适应用程序（如 [Visual Studio Code](https://code.visualstudio.com)）中打开 p8 证书。 记下密钥值（位于-----BEGIN PRIVATE KEY----- 和 -----END PRIVATE KEY----- 之间） 。

    -----BEGIN PRIVATE KEY-----  
    <key_value>  
    -----END PRIVATE KEY-----

    > [!NOTE]
    > 这是稍后将用于配置通知中心的令牌值 。

完成这些步骤后，你应具有稍后要在[使用 APNS 信息配置通知中心](#configure-your-notification-hub-with-apns-information)中使用的以下信息：

* 团队 ID（请参阅步骤 1）
* 捆绑包 ID（请参阅步骤 1）
* 密钥 ID（请参阅步骤 7）
* 令牌值（在步骤 8 中获取的 p8 密钥值）

### <a name="create-a-provisioning-profile-for-the-app"></a>为应用程序创建配置文件

1. 返回到 [iOS 预配门户](https://go.microsoft.com/fwlink/p/?LinkId=272456)，选择“Certificates, Identifiers & Profiles”（证书、标识符和配置文件），从左侧菜单中选择“Profiles”（配置文件），然后选择 **+** 创建新的配置文件。 此时将显示“Register a New Provisioning Profile”（注册新的预配配置文件）屏幕。

1. 选择“Development”（开发）下的“iOS App Development”（iOS 应用程序开发）作为预配配置文件类型，然后选择“Continue”（继续）。  

    ![预配配置文件列表](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. 接下来，从“App ID”（应用 ID）下拉列表中选择创建的应用 ID，然后选择“Continue”（继续）。 

    ![选择应用 ID](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. 在“Select certificates”（选择证书）窗口中，选择用于代码签名的开发证书，然后选择“Continue”（继续）。 

    > [!NOTE]
    > 此证书不是在[上一步](#creating-a-certificate-for-notification-hubs)中创建的推送证书。 这是开发证书。 如果没有开发证书，则必须创建一个，因为这是本教程的[先决条件](#prerequisites)。 开发人员证书可以在 [Apple 开发人员门户](https://developer.apple.com)中通过 [Xcode](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html)创建，或在 [Visual Studio](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) 中创建。

1. 返回到“Certificates, Identifiers & Profiles”（证书、标识符和配置文件）页，从左侧菜单中选择“Profiles”（配置文件），然后选择 **+** 创建新的配置文件。 此时将显示“Register a New Provisioning Profile”（注册新的预配配置文件）屏幕。

1. 在“选择证书”窗口中，选择你创建的开发证书。 然后选择“继续”。

1. 接下来，选择用于测试的设备，然后选择“Continue”（继续）。

1. 最后，在“Provisioning Profile Name”（预配配置文件名称）中为概要文件选择一个名称，然后选择“Generate”（生成）。 

    ![选择预配配置文件名称](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. 创建了新的预配配置文件后，选择“Download”（下载）。 记住保存证书的位置。

1. 浏览到预配配置文件所在的位置，然后双击该配置文件以将其安装在开发计算机上。
