
前面的示例显示标准登录，这要求客户端每次启动该应用程序与联系标识提供程序和后端 Azure 服务。 此方法、 低效且如果很多客户尝试同时启动您的应用程序，可能会遇到关于使用率的问题。 更好的方法是缓存授权令牌由 Azure 服务，并尝试使用基于提供程序的登录之前，使用此第一个。

> [!NOTE]
> 你可以缓存由后端无论你使用客户端管理或由服务管理身份验证的 Azure 服务颁发的令牌。 本教程使用服务托管的身份验证。
>
>

1. 打开 ToDoActivity.java 文件并添加以下 import 语句：

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;
2. 添加到以下成员`ToDoActivity`类。

        public static final String SHAREDPREFFILE = "temp";    
        public static final String USERIDPREF = "uid";    
        public static final String TOKENPREF = "tkn";    
3. 在 ToDoActivity.java 文件中，添加以下定义`cacheUserToken`方法。

        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }    

    此方法将标记为私有的首选项文件中存储的用户 ID 和令牌。 这应保护对缓存的访问，以便其他应用在设备上的没有访问令牌。 首选方法是沙盒应用程序。 但是，如果有人获取了对设备进行访问，则可能它们可能会获得对通过其他方式令牌缓存的访问。

   > [!NOTE]
   > 如果对你的数据的访问令牌被认为非常敏感，并且有人可能会获得访问设备，可以进一步防止具有加密的令牌。 但是，完全安全的解决方案超出了本教程中，范围并且取决于您的安全要求。
   >
   >
4. 在 ToDoActivity.java 文件中，添加以下定义`loadUserTokenCache`方法。

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, null);
            if (userId == null)
                return false;
            String token = prefs.getString(TOKENPREF, null);
            if (token == null)
                return false;

            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);

            return true;
        }
5. 在*ToDoActivity.java*文件中，将`authenticate`使用以下方法，它使用令牌缓存的方法。 如果你想要使用的帐户不是 Google，请更改登录提供程序。

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();    
                    }
                });
            }
        }
6. 生成的应用和测试身份验证使用有效的帐户。 至少两次运行它。 在第一次运行，您应收到登录并创建令牌缓存的提示。 之后，每次运行尝试加载令牌缓存中的用于身份验证。 你不应需要登录。
