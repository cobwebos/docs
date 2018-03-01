
1. 在 Visual Studio 解决方案资源管理器中，右键单击 Windows 应用商店应用项目。 然后选择“应用商店” > “将应用与应用商店关联”。

    ![将应用与 Windows 应用商店关联](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. 在向导中，选择“下一步”。 然后，使用 Microsoft 帐户登录。 在“保留新应用名称”中为应用键入一个名称，然后选择“保留”。
3. 成功创建应用注册后，选择新应用名称。 选择“下一步”，然后选择“关联”。 此过程会将所需的 Windows 应用商店注册信息添加到应用程序清单中。
4. 使用之前为 Windows 应用商店应用创建的相同注册，对 Windows Phone 商店应用重复步骤 1 和 3。  
5. 转到 [Windows 开发人员中心](https://dev.windows.com/en-us/overview)，并使用 Microsoft 帐户登录。 在“我的应用”中，选择新的应用注册。 然后展开“服务” > “推送通知”。
6. 在“推送通知”页上，在“Windows 推送通知服务(WNS)和 Microsoft Azure 移动应用”下，选择“Live 服务站点”。  记录下**包 SID** 的值和“应用程序密钥”中的当前值。 

    ![开发人员中心中的应用设置](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > 应用程序机密和程序包 SID 是重要的安全凭据。 不要将这些值告诉任何人，也不要随应用分发它们。
   >
   >
