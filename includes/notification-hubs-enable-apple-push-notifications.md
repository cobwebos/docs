

## <a name="generate-the-certificate-signing-request-file"></a>生成证书签名请求文件
Apple 推送通知服务 (APNS) 使用证书进行身份验证的推送通知。 按照这些说明来创建发送和接收通知的所需推送证书。 有关这些概念的详细信息，请参阅官方[Apple 推送通知服务](http://go.microsoft.com/fwlink/p/?LinkId=272584)文档。

生成证书签名请求 (CSR) 文件，Apple 用它来生成一个签名的推送证书。

1. 在 Mac 上，运行 Keychain Access 工具。 它可以从打开**实用工具**文件夹或**其他**在启动板上的文件夹。
2. 单击**Keychain Access**，展开**证书助手**，然后单击**从证书颁发机构请求证书...**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. 选择你**用户电子邮件地址**和**公用名**，请确保**已保存到磁盘**已选择，然后单击**继续**。 保留**CA 电子邮件地址**保留空白，因为它不是必需的字段。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. 为中的证书签名请求 (CSR) 文件键入一个名称**另存为**中, 选择一个位置**其中**，然后单击**保存**。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      这将 CSR 文件保存到选定位置;默认位置是桌面。 请记住为此文件选择的位置。

接下来，你将向 Apple 注册你的应用程序、 启用推送通知，并上载此导出的 CSR 以创建一个推送证书。

## <a name="register-your-app-for-push-notifications"></a>你的应用程序注册推送通知
若要能够将推送通知发送到 iOS 应用程序，必须向 Apple 注册你的应用程序，并还注册推送通知。  

1. 如果尚未注册你的应用程序，导航到<a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 设置门户</a>在 Apple 开发人员中心，使用你的 Apple ID，登录单击**标识符**，然后单击**应用 Id**，最后单击上 **+** 符号以注册新的应用程序。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)
      
2. 更新为新应用程序的以下三个字段，然后单击**继续**:
   
   * **名称**： 键入你的应用程序的描述性名称**名称**字段**应用程序 ID 说明**部分。
   * **捆绑标识符**： 下**显式应用 ID**部分中，输入**捆绑标识符**形式`<Organization Identifier>.<Product Name>`中所述[应用程序分发指南](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)。 *组织标识符*和*产品名称*必须与在创建 XCode 项目时将使用的组织标识符与产品名称匹配。 在下面的屏幕截图*NotificationHubs*用作组织标识符和*GetStarted*用作产品名称。 确保这与将在 XCode 项目中使用的值相匹配，从而将允许你使用 XCode 的正确的发布配置文件。 
   * **推送通知**： 检查**推送通知**选项**应用程序服务**部分中。
     
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
     
      这会生成你的应用程序 ID 并请求你确认信息。 单击**注册**以确认新的应用程序 id。
     
      单击后**注册**，你将看到**注册完成**屏幕上，如下所示。 单击“完成”。
      
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


1. 在开发人员中心，在应用 Id 下找到你刚刚创建，并单击其所在行的应用程序 ID。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)
   
      单击应用程序 id 会显示应用详细信息。 单击**编辑**底部的按钮。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)
      
2. 滚动到屏幕，底部并单击**创建证书...**部分下的按钮**开发推送 SSL 证书**。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
      此时将显示"添加 iOS 证书"助手。
   
   > [!NOTE]
   > 本教程使用开发证书。 注册生产证书时使用相同的过程。 只需确保发送通知时使用相同的证书类型。
   > 
   > 
3. 单击**选择文件**，浏览到保存在第一个任务中，创建的 CSR 文件的位置，然后单击**生成**。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
4. 门户创建证书后，单击**下载**按钮，然后单击**完成**。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
      下载证书并将其保存到计算机上的 Downloads 文件夹。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > 默认情况下，下载的文件的开发证书名为**aps_development.cer**。
   > 
   > 
5. 双击下载的推送证书**aps_development.cer**。
   
      这将安装新的证书密钥链，如下所示：
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > 证书中的名称可能不同，但它将使用作为前缀**Apple Development iOS Push Services:**。
   > 
   > 
6. 在 Keychain Access 中，右键单击你在中创建的新推送证书**证书**类别。 单击**导出**，命名该文件，选择**.p12**格式，并依次**保存**。
   
    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)
   
    记下文件名称和导出的.p12 证书的位置。 它将用于使用 APNS 启用身份验证。
   
   > [!NOTE]
   > 本教程将创建 QuickStart.p12 文件。 文件名和位置可能不同。
   > 
   > 

## <a name="create-a-provisioning-profile-for-the-app"></a>创建应用程序的预配配置文件
1. 返回<a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 设置门户</a>，选择**预配配置文件**，选择**所有**，然后单击 **+** 按钮以创建新的配置文件。 这将启动**添加 iOS 设置配置文件**向导
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. 选择**iOS 应用程序开发**下**开发**作为设置配置文件类型，然后单击**继续**。 
3. 接下来，选择刚创建的应用程序 ID**应用程序 ID**下拉列表中，然后单击**继续**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. 在**选择证书**屏幕中，选择你经常开发证书用于代码签名，然后单击**继续**。 这不是你刚刚创建的推送证书。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. 接下来，选择**设备**用于测试，并单击**继续**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. 最后，选择在配置文件的名称**配置文件名称**，单击**生成**。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
7. 创建新的预配配置文件时单击以下载它并在你的 Xcode 开发计算机上安装它。 然后单击**完成**。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
