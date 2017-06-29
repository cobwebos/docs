## <a name="create-a-device-identity"></a>创建设备标识
本部分使用 [Azure 门户][lnk-azure-portal]在 IoT 中心的标识注册表中创建设备标识。 设备无法连接到 IoT 中心，除非它在标识注册表中具有条目。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]中的“标识注册表”部分。 门户中的“Device Explorer”用于生成唯一的设备 ID 和密钥，设备在向 IoT 中心发送设备到云的消息时，可以使用这些信息进行自我标识。 设备 ID 区分大小写。

1. 请确保已登录到 [Azure 门户][lnk-azure-portal]。 

1. 在跳转栏中单击“所有资源”，然后查找 IoT 中心资源。 
   
    ![导航到 IoT 中心][img-find-iothub]
1. IoT 中心资源打开以后，单击“Device Explorer”工具，然后单击顶部的“添加”。 提供新设备的名称，然后单击“保存”。 

    ![在门户中创建设备标识][img-create-device]

   这将为 IoT 中心创建新设备标识。
1. 在 Device Explorer 的设备列表中，单击新创建的设备，记下“连接字符串---主键”。 

    ![设备连接字符串][img-connection-string]
> [!NOTE]
> IoT 中心标识注册表只存储设备标识，以启用对 IoT 中心的安全访问。 它存储设备 ID 和密钥作为安全凭据，以及启用或禁用标志让你禁用对单个设备的访问。 如果应用程序需要存储其他特定于设备的元数据，则应使用特定于应用程序的存储。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]。
> 
> 

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

