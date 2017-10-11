#### <a name="configure-the-ios-project-in-xamarin-studio"></a>在 Xamarin Studio 中配置的 iOS 项目
1. 在 Xamarin.Studio 中，打开**Info.plist**，并更新**捆绑标识符**与新的应用程序 id。 使用前面创建的程序包 ID

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. 向下滚动到**后台模式**。 选择**启用后台模式**框和**远程通知**框。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. 双击以打开解决方案面板中的项目**项目选项**。
4. 下**生成**，选择**iOS 捆绑签名**，并选择相应的标识和设置的配置文件刚才设置设置为此项目。

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   这可确保项目使用新的配置文件进行代码签名。 有关正式的 Xamarin 设备设置文档，请参阅[Xamarin 设备设置]。

#### <a name="configure-the-ios-project-in-visual-studio"></a>在 Visual Studio 中配置的 iOS 项目
1. 在 Visual Studio 中，右键单击该项目，然后单击**属性**。
2. 在属性页中，单击**iOS 应用程序**选项卡，然后更新**标识符**前面创建的 id。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. 在**iOS 捆绑签名**选项卡上，选择相应的标识和预配你刚刚设置为此项目的配置文件。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    这可确保项目使用新的配置文件进行代码签名。 有关正式的 Xamarin 设备设置文档，请参阅[Xamarin 设备设置]。
4. 双击以打开它，然后启用的 Info.plist **RemoteNotifications**下**后台模式**。

[Xamarin 设备设置]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
