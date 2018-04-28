## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

创建 IoT 中心以后，即可找到将设备和应用程序连接到 IoT 中心时需要使用的重要信息。 

1. 创建 IoT 中心后，请在仪表板中单击它。 记下“主机名”，并单击“共享访问策略”。

   ![获取 IoT 中心的主机名](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. 在“共享访问策略”窗格中单击“iothubowner”策略，复制并记下 IoT 中心的**连接字符串**。 有关详细信息，请参阅[控制对 IoT 中心的访问](../articles/iot-hub/iot-hub-devguide-security.md)。

> [!NOTE] 
此设置教程不需要此 iothubowner 连接字符串。 不过，在完成此设置以后，可能需要将其用于某些讲述其他 IoT 方案的教程。

   ![获取 IoT 中心连接字符串](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>在 IoT 中心为设备进行设备注册

1. 在 [Azure 门户](https://portal.azure.com/)中打开 IoT 中心。

2. 单击“IoT 设备”。
3. 在“IoT 设备”窗格中，单击“添加”将设备添加到 IoT 中心。 然后执行以下操作：

   **设备 ID**：输入新设备的 ID。 设备 ID 区分大小写。

   **身份验证类型**：选择“对称密钥”。

   **自动生成密钥**：选中此复选框。

   **将设备连接到 IoT 中心**：单击“启用”。

   ![在 IoT 中心的“IoT 设备”中添加设备](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-iot-devices-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. 单击“ **保存**”。
5. 创建设备后，在“IoT 设备”窗格中打开该设备。

   ![IoT 中心内的“IoT 设备”列表](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_device-list-in-iot-devices-portal.png)

6. 记下连接字符串的主密钥。

   ![获取设备连接字符串](../articles/iot-hub/media/iot-hub-create-hub-and-device/8_get-device-connection-string-in-iot-devices-portal.png)
