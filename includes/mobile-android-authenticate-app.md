
1. 在 Android Studio 的“项目资源管理器”中，打开 ToDoActivity.java 文件，然后添加以下 import 语句。

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
2. 将以下方法添加到 **ToDoActivity** 类：

        private void authenticate() {
            // Login using the Google provider.

            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();    
                }
            });       
        }

    这将会创建一个用于处理身份验证过程的新方法。 使用 Google 登录对用户进行身份验证。 出现的对话框中会显示已经过身份验证的用户 ID。 如果未正常完成身份验证，你将无法继续操作。

    > [!NOTE]
    > 如果使用的标识提供者不是 Google，请将传递给上述 **login** 方法的值更改为下列其中一项：_MicrosoftAccount_、_Facebook_、_Twitter_ 或 _windowsazureactivedirectory_。

3. 在 **onCreate** 方法中，在实例化 `MobileServiceClient` 对象的代码后面添加以下代码行。

        authenticate();

    此调用启动身份验证过程。
4. 将 **onCreate** 方法中 `authenticate();` 后面的剩余代码移到新的 **createTable** 方法。 其应如下所示：

        private void createTable() {

            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);

            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

            // Load the items from Azure.
            refreshItemsFromTable();
        }
5. 然后，从“运行”菜单中单击“运行应用”启动应用，并使用所选的标识提供者登录。

成功登录后，应用应可以正常运行，并且你应能够查询后端服务并对数据进行更新。


<!--HONumber=Dec16_HO2-->


