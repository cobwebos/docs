
1. 在解决方案视图中 (或**解决方案资源管理器**Visual Studio 中)，右键单击**组件**文件夹中，单击**获取更多组件...**，搜索**Google Cloud Messaging Client**组件并将其添加到项目。
2. 打开 ToDoActivity.cs 项目文件并添加以下 using 语句到类：
   
        using Gcm.Client;
3. 在**ToDoActivity**类中，添加以下新代码： 
   
        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();
   
        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }
   
    这使您要通过推送处理程序服务流程访问移动客户端的实例。
4. 以下代码添加到**OnCreate**方法之后， **MobileServiceClient**创建：
   
       // Set the current instance of TodoActivity.
       instance = this;
   
       // Make sure the GCM client is set up correctly.
       GcmClient.CheckDevice(this);
       GcmClient.CheckManifest(this);
   
       // Register the app for push notifications.
       GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

你**ToDoActivity**现在已准备好进行添加推送通知。

