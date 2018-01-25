1. 登录到 [Azure 门户][lnk-portal]。
1. 选择“新建” > “物联网” > “IoT 中心”。
   
    ![Azure 门户跳转栏][1]

1. 在“IoT 中心”窗格中，输入 IoT 中心的以下信息：

   * **名称**：创建 IoT 中心的名称。 如果输入的名称有效，会显示一个绿色复选标记。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **定价和缩放级别**：对于本教程，请选择**F1 - 免费**级别。 有关详细信息，请参阅[定价和缩放层][lnk-pricing]。

   * **资源组**：创建用于托管 IoT 中心的资源组，或使用现有的资源组。 有关详细信息，请参阅[使用资源组管理 Azure 资源][lnk-resource-groups]

   * **位置**：选择最近的位置。

   * **固定仪表板**：选中此选项可以方便地从仪表板访问 IoT 中心。

    ![IoT 中心窗口][2]

1. 单击“创建” 。 创建 IoT 中心可能需要数分钟的时间。 可在“通知”窗格中监视进度。
<!-- Images -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md