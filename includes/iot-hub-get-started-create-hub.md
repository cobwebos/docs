## <a name="create-an-iot-hub"></a>创建 IoT 中心
为模拟设备应用创建要连接到的 IoT 中心。 以下步骤说明如何使用 Azure 门户来完成此任务。

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

创建 IoT 中心以后，即可找到将设备和应用程序连接到 IoT 中心时需要使用的重要信息。 

1. 成功创建 IoT 中心后，请在 Azure 门户中单击 IoT 中心对应的新磁贴，以打开新 IoT 中心的属性窗口。 记下“主机名”，并单击“共享访问策略”。
   
    ![新建 IoT 中心窗口][4]
1. 在“共享访问策略”中，单击“iothubowner”策略，并复制并记下“iothubowner”窗口中的 IoT 中心连接字符串。 有关详细信息，请参阅“IoT 中心开发人员指南”中的[访问控制][lnk-access-control]。
   
    ![共享访问策略][5]

<!-- Images. -->
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
