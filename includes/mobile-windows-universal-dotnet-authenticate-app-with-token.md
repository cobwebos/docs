
1. 在 MainPage.xaml.cs 项目文件中，添加以下 **using** 语句：
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. 将 **AuthenticateAsync** 方法替换为以下代码：
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider);
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    在此版本的 **AuthenticateAsync** 中，应用将尝试使用存储在 **PasswordVault** 中的凭据来访问服务。 没有存储任何凭证时，也执行常规登录。
   
   > [!NOTE]
   > 缓存的令牌可能已过期，正在使用应用时，在身份验证之后也可能会发生令牌到期。 若要了解如何确定令牌是否已过期，请参阅[检查过期的身份验证令牌](http://aka.ms/jww5vp)。 有关用于处理到期令牌相关的授权错误的解决方案，请参阅文章[在 Azure 移动服务托管 SDK 中缓存和处理到期令牌](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx)。 
   > 
   > 
3. 两次重新启动此应用。
   
    请注意，在第一次启动时，再次需要使用此提供商进行登录。 但是，在第二次重新启动时，将使用缓存的凭证，而绕过登录。 

