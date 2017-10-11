
1. 在 Visual Studio 解决方案资源管理器，右键单击 Windows 应用商店应用项目中，然后单击**存储** > **将应用与应用商店**。

    ![将应用与 Windows 应用商店相关联](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. 在向导中，单击**下一步**，并使用你的 Microsoft 帐户登录。 键入你的应用程序的名称**新的应用程序名称保留**，然后单击**保留**。
3. 已成功创建应用程序注册后，选择新的应用程序名称，单击**下一步**，然后单击**关联**。 这将所需的 Windows 应用商店注册信息添加到应用程序清单。
4. 通过 Windows 应用商店应用中使用以前创建的相同注册对 Windows Phone 应用商店应用项目重复步骤 1 和 3。  
5. 浏览到[Windows 开发人员中心](https://dev.windows.com/en-us/overview)，并使用你的 Microsoft 帐户登录。 单击中的新应用程序注册**我的应用**，然后展开**服务** > **推送通知**。
6. 上**推送通知**页上，单击**Live 服务站点**下**Windows 推送通知服务 (WNS) 和 Microsoft Azure Mobile Apps**。 记下的值**程序包 SID**和*当前*中的值**应用程序机密**。 

    ![在开发人员中心的应用程序设置](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > 应用程序密钥和程序包 SID 是重要的安全凭据。 请勿与任何人分享这些值或将它们与你的应用。
   >
   >
