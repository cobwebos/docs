
默认情况下，可以以匿名方式调用中的移动应用后端 Api。 接下来，你需要限制对仅经过身份验证的客户端的访问。  

* **Node.js 回结束 （通过 Azure 门户）** :  

    在移动应用设置中，单击**轻松表**，然后选择你的表。 单击**更改权限**，选择**身份验证的访问仅**作为所有权限，然后单击**保存**。
* **.NET 后端 (C#)**:  

    在服务器项目中，导航到**控制器** > **TodoItemController.cs**。 添加`[Authorize]`属性设为**TodoItemController**类，如下所示。 若要仅对特定方法限制访问，你还可以对这些方法，而不是类应用此属性。 重新发布服务器项目。

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Node.js （通过 Node.js 代码） 的后端**:  

    以要求进行表访问身份验证，请在 Node.js 服务器脚本中添加以下行：

        table.access = 'authenticated';

    有关更多详细信息，请参阅[如何： 对表进行访问要求进行身份验证](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth)。 若要了解如何从你的站点下载快速入门代码项目，请参阅[如何： 使用 Git 将 Node.js 后端快速入门代码项目下载](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)。
