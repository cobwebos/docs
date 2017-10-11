## <a name="webapi-project"></a>WebAPI 项目
1. 在 Visual Studio 中，打开**AppBackend**中创建的项目**通知用户**教程。
2. 在 Notifications.cs 中，将整个**通知**类替换为以下代码。 请确保将占位符替换为你的通知中心和中心名称 （具有完全访问权限） 连接字符串。 你可以获取这些值从[Azure 经典门户](http://manage.windowsazure.com)。 此模块现在表示将发送的不同安全通知。 在完整的实现，通知将存储在一个数据库;为简单起见，在这种情况下我们将它们存储在内存中。
   
        public class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
            }

            public Notification CreateNotification(string payload)
            {
                var notification = new Notification() {
                Id = notifications.Count,
                Payload = payload,
                Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Notification ReadNotification(int id)
            {
                return notifications.ElementAt(id);
            }
        }

1. 在 NotificationsController.cs 中中的代码替换**NotificationsController**类定义替换为以下代码。 此组件实现了一种设备，安全地检索通知，并还提供了一种方法 （用于本教程的目的） 来触发安全推送到你的设备。 请注意，当将通知发送到通知中心中，我们仅发送原始通知 id 通知 （和没有实际的消息）：
   
       public NotificationsController()
       {
           Notifications.Instance.CreateNotification("This is a secure notification!");
       }
   
       // GET api/notifications/id
       public Notification Get(int id)
       {
           return Notifications.Instance.ReadNotification(id);
       }
   
       public async Task<HttpResponseMessage> Post()
       {
           var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
           var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;
   
           // windows
           var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                           new Dictionary<string, string> {
                               {"X-WNS-Type", "wns/raw"}
                           });
           await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);
   
           // apns
           await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);
   
           // gcm
           await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }


请注意，`Post`方法现在不发送 toast 通知。 它将发送包含仅通知 ID 且没有任何敏感内容的原始通知。 此外，请务必注释为其你没有凭据配置在通知中心，因为它们将导致出现错误的平台的发送操作。

1. 现在，我们将重新部署到 Azure 网站的此应用程序以使其可从所有设备访问。 右键单击**AppBackend**项目，然后选择**发布**。
2. 选择 Azure 网站作为发布目标。 登录你的 Azure 帐户，选择一个现有的或新的网站，并记下**目标 URL**中的属性**连接**选项卡。 我们将此 url 称为你*后端终结点*本教程后面。 单击**发布**。

