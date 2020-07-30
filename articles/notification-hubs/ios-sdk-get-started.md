---
title: 使用 Azure 通知中心和 iOS SDK 向 iOS 发送推送通知
description: 在本教程，你将学习如何使用 Azure 通知中心和 Apple Push Notification 服务向 iOS 设备发送推送通知。
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: 94e930c996710c2b25d4114c7cfcb17129c5177d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022423"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>教程：使用 Azure 通知中心向 iOS 应用发送推送通知

本教程将介绍如何设置 Azure 通知中心，并展示如何配置凭据，以通过 [Apple Push Notification 服务 (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) 将通知推送到 iOS 设备。 

为了后续学习 Objective C 和 Swift iOS 教程，必须运行此教程，它涵盖以下步骤：

- 生成证书签名请求文件。
- 为应用请求推送通知。
- 为应用创建预配配置文件。
- 创建通知中心。
- 使用 APNS 信息配置通知中心。

## <a name="prerequisites"></a>先决条件

要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/free/)。

还需要以下项：

- 一个有效的 [Apple 开发人员](https://developer.apple.com/)帐户。
- 一部运行 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) 的 Mac，以及一个安装到密钥链的有效开发人员证书。
- 一部运行 iOS 10 或更高版本的 iPhone 或 iPad。
- 一台在 [Apple 门户](https://developer.apple.com/) 中注册并与证书关联的物理设备。

如果你不熟悉此服务，请务必阅读 [Azure 通知中心概述](notification-hubs-push-notification-overview.md)。

> [!NOTE]
> 通知中心将配置为仅使用沙盒身份验证模式。 不得将此身份验证模式用于生产工作负载。

## <a name="generate-the-certificate-signing-request-file"></a>生成证书签名请求文件

Apple Push Notification 服务 (APNS) 使用证书对推送通知进行身份验证。 请遵照这些说明来创建用于发送和接收通知的所需推送证书。 有关这些概念的详细信息，请参阅 [Apple Push Notification 服务](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) 官方文档。

生成证书签名请求 (CSR) 文件，Apple 将使用该文件生成签名的推送证书：

1. 在 Mac 上，运行 Keychain Access 工具。 可从启动板上的“Utilities”或“Other”文件夹中打开该工具。 ****   ****  

2. 选择“密钥链访问”，展开“证书助理”，然后选择“从证书颁发机构请求证书”。 ****  ****  ****

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="密钥链访问":::

   > [!NOTE]
   > 默认情况下，Keychain Access 选择列表中的第一项。 如果你位于“Certificates”（证书）类别中，并且“Apple Worldwide Developer Relations Certification Authority”（Apple 全球开发者关系证书颁发机构）不是列表中的第一项，这可能会是个问题。 在生成 CSR（证书签名请求）之前，请确保已有非密钥项，或者已选择“Apple Worldwide Developer Relations Certification Authority”（Apple 全球开发者关系证书颁发机构）密钥。

3. 选择“用户电子邮件地址”，输入“公用名”值，确保指定“保存到磁盘”，然后选择“继续”。 ****  ****   ****  **** 将“CA 电子邮件地址”留空，因为它不是必填字段 ****  。

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="必需的证书信息":::

4. 在“另存为”中为 CSR 文件输入一个名称，在“位置”中选择位置，然后选择“保存”。 ****  ****  ****

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="选择文件名":::

   此操作会将 CSR 文件保存到选定位置。 默认位置为“桌面”。 **** 请记住为此文件选择的位置。

接下来，向 Apple 注册应用，启用推送通知并上传导出的 CSR，以便创建推送证书。

## <a name="register-your-app-for-push-notifications"></a>为推送通知注册应用程序

若要将推送通知发送到 iOS 应用，请向 Apple 注册应用程序，还要注册推送通知。

1. 如果尚未注册应用，请浏览到苹果开发者中心的  [iOS 预配门户](https://go.microsoft.com/fwlink/p/?LinkId=272456) 。 使用你的 Apple ID 登录到门户，然后选择“标识符” **** 。 接下来，选择“+注册新应用”。 ****  

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="“应用 ID”页面":::

2. 在“注册新的标识符”屏幕上，选择“应用 ID”单选按钮。 ****   ****   接下来，选择“继续”。 ****

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="“注册新 ID”页面":::

3. 更新新应用的下面 3 个值，然后选择“继续”： ****

   - **说明**：键入应用的描述性名称。
   - **捆绑 ID**：按 [应用分发指南](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)中所述，输入格式为 Organization Identifier.Product Name 的捆绑 ID。 ****   “组织标识符”和“产品名称”值必须与创建 Xcode 项目时使用的组织标识符与产品名称匹配。 ****   ****   在下面的屏幕截图中，“NotificationHubs”值用作组织标识符，“GetStarted”值用作产品名称。 ****   ****   确保“捆绑标识符”值与 Xcode 项目中的值匹配，这样 Xcode 才能使用正确的发布配置文件。 ****  

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="注册应用 ID":::

   - **推送通知**：勾选“功能”部分中的“推送通知”选项。 ****   ****  

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="注册新应用 ID":::

      此操作会生成应用 ID 并请求你确认信息。 选择“继续”，然后选择“注册”以确认新的应用 ID。 ****  ****  

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="确认新的应用 ID":::

      选择“注册”后，会在“证书、标识符和配置文件”页面上看到新的应用 ID 作为行项。 ****  ****  

4. 在“证书、标识符和配置文件”页面的“标识符”下，找到你刚刚创建的应用 ID 行项，并选择其所在的行，显示“编辑应用 ID 配置”屏幕。 ****   ****  ****  

## <a name="create-a-certificate-for-notification-hubs"></a>为通知中心创建证书

需要提供证书才能使通知中心与 APNS 一起使用。 **** 可通过以下两种方式之一实现此目的：

- 创建可直接上传到通知中心的 .p12 文件。 ****

- 创建可用于 [基于令牌的身份验证](notification-hubs-push-notification-http2-token-authentication.md) （新方法）的 .p8文件。 ****  

与使用证书相比，第二个选项有很多好处，如 [APNS 的基于令牌的 (HTTP/2) 的身份验证](notification-hubs-push-notification-http2-token-authentication.md)中所述。 但是，为这两种方法都提供了步骤。

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>选项 1：创建可直接上传到通知中心的 .p12 推送证书

1. 向下滚动到选中的“推送通知”选项，然后选择“配置”以创建证书。 ****   ****  

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="应用 ID":::

2. 此时将显示“Apple Push Notification 服务 SSL 证书”窗口。 ****   在“开发 SSL 证书”部分中，选择“创建证书”按钮。 ****   ****  

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="创建证书":::

   此时将显示“创建新证书”屏幕。 ****  

   > [!NOTE]
   > 本教程使用开发证书。 注册生产证书时使用相同的过程。 请确保在发送通知时使用相同的证书类型。

3. 勾选“选择文件”，浏览到在第一个任务中保存 CSR 文件的位置，然后双击证书名以加载该证书。 **** 接下来，选择“继续”。 ****

4. 当门户创建证书后，选择“下载”按钮。 ****   保存证书，并记住保存证书的位置。

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="下载证书":::

   下载证书并将其保存在“Downloads”文件夹中。 ****  

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="查找证书文件":::

   默认情况下，下载的开发证书名为 **aps_development.cer**。

5. 双击下载的推送证书 aps\_development.cer。 **** 此操作将在密钥链中安装新证书，如下图所示：

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="密钥链访问":::

   证书中的名称可能会不同，但会以 **Apple Development iOS Push Services** 作为前缀。

6. 在“密钥链访问”中，右键单击在“证书”类别中创建的新推送证书。 ****   选择“导出”，为文件命名，选择“.p12”格式，然后选择“保存”。 ****  ****   ****

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="导出证书":::

   可以选择使用密码保护证书，但这是可选的。 如果要跳过密码创建，请单击“确定”。 ****   记下导出的 .p12 证书的文件名和位置。 它们用于启用 APNS 身份验证。

   > [!NOTE]
   > 你的 .p12 文件名和位置可能不同于本教程中所示的名称和位置。

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>选项 2：创建可用于基于令牌的身份验证的 p8 证书

1. 请记下以下详细信息：

   - 应用 ID 前缀（这是团队 ID）  ****
   - 捆绑包 ID

2. 返回“证书，标识符和配置文件”，再单击“密钥”。 ****  **** 如果已为 APNS 配置了密钥，则可以重复使用在创建后立即下载的 .p8 证书。 如果已配置，则可忽略步骤 3-5。

3. 单击 + 按钮（或“创建密钥”按钮）以创建新密钥。 ****   ****  

4. 提供合适的密钥名称值，选中“Apple Push Notification 服务(APNS)”选项，然后单击“继续”，在下一个屏幕上单击“注册”。 ****   ****   ****  ****  

5. 单击“下载”，然后将 .p8 文件（前缀为 `AuthKey_`）移动到安全的本地目录，再单击“完成”。 ****   ****   ****

   > [!IMPORTANT]
   > 请确保将 .p8 文件保存在安全的地方（并保存备份）。 密钥在下载后无法重新下载；服务器副本已被删除。

6. 在“密钥”上，单击刚创建的密钥（如果已选择使用现有密钥，则改为单击现有密钥）。 ****

7. 记下“密钥 ID”值。 ****  

8. 在所选的适当应用程序（如 [Visual Studio Code](https://code.visualstudio.com/)）中打开 .p8 证书，然后记下密钥值。 这是“-----BEGIN PRIVATE KEY-----”和“-----END PRIVATE KEY-----”之间的值。 ****   ****  

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   该令牌值稍后将用于配置通知中心。

完成这些步骤后，你应具有稍后要在 [使用 APNS 信息配置通知中心](#configure-the-notification-hub-with-apns-information)中用到的下列信息：

- 团队 ID（请参阅步骤 1） 
- 捆绑包 ID（请参阅步骤 1） 
- 密钥 ID（请参阅步骤 7） 
- 令牌值（即 .p8 密钥值，请参阅步骤 8） 

## <a name="create-a-provisioning-profile"></a>创建预配配置文件

1. 返回到 [iOS 预配门户](https://go.microsoft.com/fwlink/p/?LinkId=272456)，选择“证书、标识符和配置文件”，从左侧菜单中选择“配置文件”，然后选择 + 来创建新的配置文件。 ****  ****   ****   此时将显示“注册新的预配配置文件”屏幕。 ****  

2. 在“开发”下选择“iOS 应用开发”作为预配配置文件类型，然后选择“继续”。 ****   ****   ****

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="预配配置文件列表":::

3. 接下来，从“应用 ID”下拉列表中选择所创建的应用 ID，然后选择“继续”。 ****   ****

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="选择应用 ID":::

4. 在“选择证书”窗口中，选择用于代码签名的开发证书，然后选择“继续”。 ****   **** 此证书不是所创建的推送证书。 如果不存在，则必须创建一个。 如果证书存在，请跳到下一步。 若要创建开发证书（如果开发证书不存在），请执行以下操作：

   1. 如果看到“没有可用的证书”，请选择“创建证书”。 ****  ****
   2. 在“软件”部分中，选择“Apple 开发”。 ****   **** 接下来，选择“继续”。 ****
   3. 在“创建新证书”屏幕中，勾选“选择文件”。 ** **  ****
   4. 浏览到之前创建的“证书签名请求”证书，选择该证书，然后选择“打开”。 ****   ****
   5. 选择“继续”。 ****
   6. 下载开发证书，并记住其保存位置。

5. 返回到“证书、标识符和配置文件”页面，从左侧菜单中选择“配置文件”，然后选择 + 来创建新的配置文件”。 ****   ****   ****   此时将显示“注册新的预配配置文件”屏幕。 ****  

6. 在“选择证书”窗口中，选择你刚刚创建的开发证书。 ****   接下来，选择“继续”。 ****

7. 接下来，选择用于测试的设备，然后选择“继续”。 ****

8. 最后，在“预配配置文件名称”中为配置文件选择一个名称，然后选择“生成”。 ****  ****

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="选择预配配置文件名称":::

9. 创建新的预配配置文件后，选择“下载”。 **** 记住证书保存的位置。

10. 浏览到预配配置文件所在的位置，然后双击该配置文件以将其安装在 Xcode 开发计算机上。

## <a name="create-a-notification-hub"></a>创建通知中心

在本部分中，你将创建一个通知中心，并使用 .p12 推送证书或基于令牌的身份验证来配置 APNS 身份验证。 如果想要使用已创建的通知中心，可以跳到步骤 5。

1. 登录  [Azure 门户](https://portal.azure.com/)。

2. 在左侧菜单上选择“所有服务”，然后在“移动”部分中选择“通知中心” ****   ****   ****  。 选择服务名称旁边的星形图标，将服务添加到左侧菜单上的“收藏夹”部分 ****  。 在将“通知中心”添加到“收藏夹”之后，将其选中。 ****   ****

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Azure 门户":::

3. 在“通知中心”页上，选择工具栏上的“添加” ****   ****  。

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="添加工具栏按钮":::

4. 在“通知中心”页面上，执行以下步骤： ****  

   1. 在“通知中心”中输入名称 **** 。
   2. 在“创建新的命名空间”中输入名称 **** 。 命名空间包含一个或多个通知中心。
   3. 从“位置”下拉列表中选择一个值。 ****   此值指定要在其中创建通知中心的位置。
   4. 选择“资源组”中的现有资源组，或创建新的资源组。 ****
   5. 选择“创建”。 ****

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="设置属性":::

5. 选择“通知”（钟形图标），然后选择“转到资源” ****   **** 。 还可以刷新“通知中心”页上的列表，然后选择相应中心 ****  。

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="门户通知":::

6. 从列表中选择“访问策略” ****  。 记下向你提供的两个连接字符串。 稍后在处理推送通知时需要它们。

   > [!IMPORTANT]
   > 请不要在应用程序中使用 **DefaultFullSharedAccessSignature** 策略。 这只能在后端使用。

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="连接字符串":::

## <a name="configure-the-notification-hub-with-apns-information"></a>使用 APNS 信息配置通知中心

在“通知服务”下，选择“Apple (APNS)”，然后根据之前在 [为通知中心创建证书](#create-a-certificate-for-notification-hubs)部分中选择的方法，执行相应的步骤。 ****  ****  

> [!NOTE]
> 仅当希望将推送通知发送给从应用商店购买应用的用户时，才对“应用程序模式”使用“生产”。 

### <a name="option-1-use-a-p12-push-certificate"></a>选项 1：使用 .p12 推送证书

1. 选择“证书”。 ****

2. 选择文件图标。

3. 选择前面导出的 .p12 文件，然后选择“打开”。 ****

4. 如果需要，请指定正确的密码。

5. 选择“沙盒”模式。  ****

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="配置":::

6. 选择“保存”。 ****

### <a name="option-2-use-token-based-authentication"></a>选项 2：使用基于令牌的身份验证

1. 选择“令牌”。 ****

2. 输入前面获取的以下值：

   - 密钥 ID
   - 捆绑包 ID
   - 团队 ID
   - 令牌

3. 选择“沙盒” ****

4. 选择“保存”。 ****

你现已使用 APNS 配置了通知中心。 此外，还具有注册应用和发送推送通知所需的连接字符串。

## <a name="next-steps"></a>后续步骤

在本教程中，你在 Azure 中创建并配置了一个通知中心，并已它其配置为允许通过 Apple Push Notification 服务 (APNS) 将通知发送到你的应用程序。 接下来，我们将创建一个示例 iOS 应用程序并集成 Azure 通知中心 SDK，使其能够接收通过 Azure 门户发送的推送通知。 根据所选的语言，前往以下教程：

- [教程：使用 Swift 与 iOS 应用集成]()
