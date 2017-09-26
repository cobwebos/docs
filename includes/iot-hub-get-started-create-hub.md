## <a name="create-an-iot-hub"></a>创建 IoT 中心
为模拟设备应用创建要连接到的 IoT 中心。 以下步骤说明如何使用 Azure 门户来完成此任务。

1. 登录到 [Azure 门户][lnk-portal]。
1. 在“跳转栏”中，依次单击“新建” > “物联网” > “IoT 中心”。
   
    ![Azure 门户跳转栏][1]
1. 在“IoT 中心”窗口中，选择 IoT 中心的配置。
   
    ![IoT 中心窗口][2]
   
   1. 在“名称”框中，输入 IoT 中心的名称。 如果该“名称”有效且可用，“名称”框中会出现绿色的勾选标记。
    [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]
   
   1. 选择 [定价和缩放层][lnk-pricing]。 本教程不需要特定的层。 对于本教程，请使用免费 F1 层。
   1. 在“资源组”中，创建资源组或选择现有的资源组。 有关详细信息，请参阅[使用资源组管理 Azure 资源][lnk-resource-groups]。
   1. 在“位置”中，选择托管 IoT 中心的位置。 对于本教程，请选择最近位置。
1. 选择 IoT 中心配置选项后，单击“创建”。  Azure 可能需要几分钟时间来创建 IoT 中心。 若要检查状态，可以在“启动板”或“通知”面板中监视进度。
   
1. 成功创建 IoT 中心后，请在 Azure 门户中单击 IoT 中心对应的新磁贴，以打开新 IoT 中心的属性窗口。 记下“主机名”，并单击“共享访问策略”。
   
    ![新建 IoT 中心窗口][4]
1. 在“共享访问策略”中，单击“iothubowner”策略，并复制并记下“iothubowner”窗口中的 IoT 中心连接字符串。 有关详细信息，请参阅“IoT 中心开发人员指南”中的[访问控制][lnk-access-control]。
   
    ![共享访问策略][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
