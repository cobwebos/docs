## <a name="configure-the-nodejs-simulated-device"></a>配置 Node.js 模拟的设备
1. 在远程监视仪表板，单击**+ 添加设备**，然后添加*自定义设备*。 主机名、 设备 id 和设备密钥，请记下 IoT 中心。 你需要它们本教程中稍后当你准备 remote_monitoring.js 设备客户端应用程序。
2. 确保该 Node.js 版本 0.12.x 或更高版本安装在你的开发计算机上。 运行`node --version`在命令提示符下或在命令行程序若要检查的版本。 有关使用程序包管理器在 Linux 上安装 Node.js 的信息，请参阅[通过程序包管理器安装 Node.js][node-linux]。
3. 当安装 Node.js 后时，克隆的最新版本[azure iot-sdk 节点][ lnk-github-repo]到你的开发计算机的存储库。 始终使用**master**的库和示例的最新版本的分支。
4. 从你的本地副本[azure iot-sdk 节点][ lnk-github-repo]存储库中，复制以下两个文件节点/设备/示例文件夹中的空文件夹在你的开发计算机上：
   
   * packages.json
   * remote_monitoring.js
5. 打开 remote_monitoring.js 文件并查找以下变量定义：
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. 替换**[IoT 中心设备连接字符串]**与你设备的连接字符串。 用于 IoT 中心主机名、 设备 id 和你记下的步骤 1 中的设备密钥使用的值。 设备连接字符串具有以下格式：
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    如果你的 IoT 中心主机名是**contoso**和你的设备 id 是否**mydevice**，你的连接字符串类似于下面的代码段：
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. 保存该文件。 在 shell 或包含这些文件，以安装所需的包，然后运行示例应用程序的文件夹中的命令提示符中运行以下命令：
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>观察操作中的动态遥测
仪表板显示从现有的模拟设备的温度和湿度遥测数据：

![默认的仪表板][image1]

如果你选择在上一节中运行的 Node.js 模拟的设备，你将看到温度、 湿度和外部温度遥测：

![将外部温度添加到仪表板][image2]

远程监视解决方案自动检测到其他外部温度遥测类型，并将其添加到仪表板上的图表。

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-send-external-temperature/image1.png
[image2]: media/iot-suite-send-external-temperature/image2.png