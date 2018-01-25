## <a name="create-an-iot-hub"></a>创建 IoT 中心
为模拟设备应用创建要连接到的 IoT 中心。 以下步骤说明如何使用 Azure 门户来完成此任务。

1. 登录到 [Azure 门户][lnk-portal]。

1. 选择“新建” > “物联网” > “IoT 中心”。
   
    ![Azure 门户跳转栏][1]

1. 在“IoT 中心”窗格中，输入 IoT 中心的以下信息：

   * **名称**：创建 IoT 中心的名称。 如果输入的名称有效，会显示一个绿色复选标记。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **定价和缩放层**：对于本教程，请选择“F1 - 免费”层。 有关详细信息，请参阅[定价和缩放层][lnk-pricing]。

   * **资源组**：创建用于托管 IoT 中心的资源组，或使用现有的资源组。 有关详细信息，请参阅[使用资源组管理 Azure 资源][lnk-resource-groups]

   * **位置**：选择最近的位置。

   * **固定仪表板**：选中此选项可以方便地从仪表板访问 IoT 中心。

    ![IoT 中心窗口][2]

1. 单击“创建”。 创建 IoT 中心可能需要数分钟的时间。 可在“通知”窗格中监视进度。

1. 成功创建 IoT 中心后，请在 Azure 门户中单击 IoT 中心对应的新磁贴，以打开新 IoT 中心的属性窗口。 创建 IoT 中心以后，即可找到将设备和应用程序连接到 IoT 中心时需要使用的重要信息。 记下“主机名”，并单击“共享访问策略”。
   
    ![新建 IoT 中心窗口][4]

1. 在“共享访问策略”中，单击“iothubowner”策略，并复制并记下“iothubowner”窗口中的 IoT 中心连接字符串。 有关详细信息，请参阅“IoT 中心开发人员指南”中的[访问控制][lnk-access-control]。
   
    ![共享访问策略][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
