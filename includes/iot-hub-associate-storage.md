## <a name="associate-an-azure-storage-account-to-iot-hub"></a>将 Azure 存储帐户关联到 IoT 中心

由于模拟设备应用将文件上传到 Blob，因此必须拥有与 IoT 中心关联的 [Azure 存储](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)帐户。 将 Azure 存储帐户与 IoT 中心相关联时，IoT 中心会生成一个 SAS URI。 设备可以使用此 SAS URI 安全地将文件上传到 Blob 容器。 IoT 中心服务和设备 SDK 协调生成 SAS URI 的过程，并使其可供设备用来上传文件。

按照[通过 Azure 门户配置文件上传](../articles/iot-hub/iot-hub-configure-file-upload.md)中的说明，将 Azure 存储帐户关联到 IoT 中心。 确保有一个 Blob 容器与你的 IoT 中心关联并且文件通知已启用。

![在门户中启用文件通知](media/iot-hub-associate-storage/enable-file-notifications.png)