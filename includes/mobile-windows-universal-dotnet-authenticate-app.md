
1. 打开共享项目文件 MainPage.cs 并将以下代码段添加到 MainPage 类：
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    此代码使用 Facebook 登录对用户进行身份验证。 如果使用的标识提供商不是 Facebook，请将上述 **MobileServiceAuthenticationProvider** 的值更改为你的提供商的值。
2. 注释掉或删除现有 **OnNavigatedTo** 方法重写中对 **RefreshTodoItems** 方法的调用。
   
    这可以防止在对用户进行身份验证之前加载数据。 接下来，向应用添加用于触发身份验证的“登录”按钮。
3. 将以下代码段添加到 MainPage 类：
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. 在 Windows 应用商店应用项目中打开 MainPage.xaml 项目文件，然后在定义 **Save** 按钮的元素前面添加以下 **Button** 元素：
   
        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
5. 在 Windows Phone 应用商店应用项目中，在 **ContentPanel** 中的 **TextBox** 元素后面添加以下 **Button** 元素：
   
        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>
6. 打开共享的 App.xaml.cs 项目文件并添加以下代码：
   
        protected override void OnActivated(IActivatedEventArgs args)
        {
            // Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Completes the sign-in process started by LoginAsync.
                // Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif
   
            base.OnActivated(args);
        }
   
    如果 **OnActivated** 方法已存在，只需添加 `#if...#endif` 代码块。
7. 按 F5 键运行 Windows 应用商店应用，单击“登录”按钮，然后使用所选的标识提供者登录到此应用。 
   
       When you are successfully logged-in, the app should run without errors, and you should be able to query your backend and make updates to data.
8. 右键单击 Windows Phone 应用商店应用项目，单击“设置为启动项目”，然后重复上一步来验证 Windows Phone 应用商店应用是否也正常运行。  



<!--HONumber=Jan17_HO3-->


