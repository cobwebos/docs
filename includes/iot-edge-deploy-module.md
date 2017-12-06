Azure IoT Edge 的主要功能之一是能够从云中将模块部署到 IoT Edge 设备。 IoT Edge 模块是以容器形式实现的可执行包。 在此部分中，将为模拟设备部署用于生成遥测数据的模块。 

1. 在 Azure 门户中导航到 IoT 中心。
1. 转到“IoT Edge (预览版)”，然后选择 IoT Edge 设备。
1. 选择“设置模块”。
1. 选择“添加 IoT Edge 模块”。
1. 在“名称”字段中，输入 `tempSensor`。 
1. 在“映像 URI”字段中，输入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。 
1. 将其他设置保留不变，然后选择“保存”。

   ![在输入名称和映像 URI 之后保存 IoT Edge 模块](./media/iot-edge-deploy-module/name-image.png)

1. 返回到“添加模块”步骤，选择“下一步”。
1. 在“指定路由”步骤中，选择“下一步”。
1. 在“审阅模板”步骤中，选择“提交”。
1. 返回到“设备详细信息”页，并选择“刷新”。 应看到新的 tempSensor 模块正在沿 IoT Edge 运行时运行。 

   ![在已部署模块列表中查看 tempSensor][1]

<!-- Images -->
[1]: ../articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png