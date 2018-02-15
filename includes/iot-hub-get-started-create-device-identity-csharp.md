## <a name="create-a-device-identity"></a>创建设备标识

本部分将创建一个 .NET 控制台应用，用于在 IoT 中心的标识注册表中创建设备标识。 设备无法连接到 IoT 中心，除非它在标识注册表中具有条目。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]中的“标识注册表”部分。 运行此控制台应用时，它会生成唯一的设备 ID 和密钥。 设备在向 IoT 中心发送设备到云的消息时，使用这些值来标识自身。 设备 ID 区分大小写。

1. 在 Visual Studio 中，使用“控制台应用(.NET Framework)”项目模板将 Visual C# Windows 经典桌面项目添加到新解决方案。 确保 .NET Framework 版本为 4.5.1 或更高。 将项目命名为 **CreateDeviceIdentity**，将解决方案命名为 **IoTHubGetStarted**。

    ![新的 Visual C# Windows 经典桌面项目][10]

1. 在解决方案资源管理器中，右键单击“CreateDeviceIdentity”项目，并单击“管理 NuGet 包”。

1. 在“NuGet 包管理器”窗口中，选择“浏览”，搜索 **microsoft.azure.devices**，选择“安装”以安装 **Microsoft.Azure.Devices** 包，并接受使用条款。 该过程将下载、安装 [Azure IoT 服务 SDK][lnk-nuget-service-sdk] NuGet 包及其依赖项并添加对它的引用。

    ![“NuGet 包管理器”窗口][11]

1. 在 **Program.cs** 文件顶部添加以下 `using` 语句：

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Common.Exceptions;
    ```

1. 将以下字段添加到 **Program** 类。 将占位符值替换为在上一部分为中心创建的 IoT 中心连接字符串。

    ```csharp
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. 将以下方法添加到 **Program** 类：

    ```csharp
    private static async Task AddDeviceAsync()
    {
      string deviceId = "myFirstDevice";
      Device device;
      try
      {
          device = await registryManager.AddDeviceAsync(new Device(deviceId));
      }
      catch (DeviceAlreadyExistsException)
      {
          device = await registryManager.GetDeviceAsync(deviceId);
      }
      Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    此方法将创建 ID 为 **myFirstDevice**的设备标识。 （如果该设备 ID 已在标识注册表中，代码就只检索现有的设备信息。）然后，应用程序会显示该标识的主密钥。 在模拟设备应用中使用此密钥连接到 IoT 中心。
[!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. 最后，在 **Main** 方法中添加以下行：

    ```csharp
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddDeviceAsync().Wait();
    Console.ReadLine();
    ```

1. 运行此应用程序并记下设备密钥。

    ![应用程序生成的设备密钥][12]

> [!NOTE]
> IoT 中心标识注册表只存储设备标识，以启用对 IoT 中心的安全访问。 标识注册表存储用作安全凭据的设备 ID 和密钥。 标识注册表还为每个设备存储启用/禁用标志，该标志可以用于禁用对该设备的访问。 如果应用程序需要存储其他特定于设备的元数据，则应使用特定于应用程序的存储。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]。

<!-- Images. -->
[10]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp3.png

<!-- Links -->
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
