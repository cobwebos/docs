---
title: 教程 - 将泛型 Python 客户端应用连接到 Azure IoT Central | Microsoft Docs
description: 本教程介绍如何以设备开发人员的身份将运行 Python 客户端应用的设备连接到 Azure IoT Central 应用程序。 通过导入设备功能模型创建设备模板，并添加视图来与连接设备交互
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: devx-track-python
ms.openlocfilehash: b79dce7755d6fab633a442839ae3ff8643d0b554
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876693"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>教程：创建客户端应用程序并将其连接到 Azure IoT Central 应用程序 (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

本文适用于解决方案构建者和设备开发人员。

本教程介绍如何以设备开发人员的身份将 Python 客户端应用程序连接到 Azure IoT Central 应用程序。 该 Python 应用程序模拟环境传感器设备的行为。 你将使用一个示例设备功能模型在 IoT Central 中创建设备模板。 此外，你将向该设备模板中添加视图，使操作员能够与设备进行交互。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 导入设备功能模型以创建设备模板。
> * 将默认视图和自定义视图添加到设备模板。
> * 发布设备模板，并将真实设备添加到 IoT Central 应用程序。
> * 创建并运行 Python 设备代码，并查看它是否可连接到 IoT Central 应用程序。
> * 查看从设备发送的模拟遥测数据。
> * 使用视图管理设备属性。
> * 调用同步和异步命令来控制设备。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要以下各项：

* 使用“自定义应用程序”模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。 应用程序必须在 2020/07/14 或之后创建。
* 装有 [Python](https://www.python.org/) 3.7 或更高版本的开发计算机。 若要检查版本，可以在命令行中运行 `python3 --version`。 Python 适用于各种操作系统。 本教程中的说明假设在 Windows 命令提示符下运行 python3 命令。

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>创建 Python 应用程序

以下步骤展示了如何创建一个 Python 客户端应用程序，用于连接已添加到应用程序的真实设备。 此 Python 应用程序模拟真实设备的行为。

1. 在命令行环境中，导航到前面创建的 `environmental-sensor` 文件夹。

1. 若要安装所需的库，请运行以下命令：

    ```cmd
    pip install azure-iot-device
    ```

1. 在 `environmental-sensor` 文件夹中创建名为 environmental_sensor.py 的文件。

1. 在 environmental_sensor.py 文件的开头添加以下 `import` 语句：

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. 将以下异步 `main` 函数和变量声明添加到该文件：

    ```python
    async def main():
      # In a production environment, don't store
      # connection information in the code.
      provisioning_host = 'global.azure-devices-provisioning.net'
      id_scope = '{your Scope ID}'
      registration_id = '{your Device ID}'
      symmetric_key = '{your Primary Key}'

      delay = 2

      # All the remaining code is nested within this main function

    if __name__ == '__main__':
    asyncio.run(main())
    ```

    将 `{your Scope ID}`、`{your Device ID}` 和 `{your Primary Key}` 占位符更新为前面记下的值。 在真实应用程序中，请不要对此信息进行硬编码。

    以下所有函数定义和代码都嵌套在 `main` 函数中。

1. 将以下两个函数添加到 `main` 函数中，以注册设备并将其连接到 IoT Central 应用程序。 注册将使用 Azure 设备预配服务：

    ```python
      async def register_device():
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
          provisioning_host=provisioning_host,
          registration_id=registration_id,
          id_scope=id_scope,
          symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print(f'Registration result: {registration_result.status}')

        return registration_result

      async def connect_device():
        device_client = None
        try:
          registration_result = await register_device()
          if registration_result.status == 'assigned':
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
              symmetric_key=symmetric_key,
              hostname=registration_result.registration_state.assigned_hub,
              device_id=registration_result.registration_state.device_id,
            )
            # Connect the client.
            await device_client.connect()
            print('Device connected successfully')
        finally:
          return device_client
    ```

1. 将以下函数添加到 `main` 函数中，以向 IoT Central 应用程序发送遥测数据：

    ```python
      async def send_telemetry():
        print(f'Sending telemetry from the provisioned device every {delay} seconds')
        while True:
          temp = random.randrange(1, 75)
          humid = random.randrange(30, 99)
          payload = json.dumps({'temp': temp, 'humid': humid})
          msg = Message(payload)
          await device_client.send_message(msg, )
          print(f'Sent message: {msg}')
          await asyncio.sleep(delay)
    ```

    遥测项的名称（`temp` 和 `humid`）必须与设备模板中使用的名称相匹配。

1. 将以下函数添加到 `main` 函数中，以处理从 IoT Central 应用程序调用的命令：

    ```python
      async def blink_command(request):
        print('Received synchronous call to blink')
        response = MethodResponse.create_from_method_request(
          request, status = 200, payload = {'description': f'Blinking LED every {request.payload} seconds'}
        )
        await device_client.send_method_response(response)  # send response
        print(f'Blinking LED every {request.payload} seconds')

      async def diagnostics_command(request):
        print('Starting asynchronous diagnostics run...')
        response = MethodResponse.create_from_method_request(
          request, status = 202
        )
        await device_client.send_method_response(response)  # send response
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Sending property update to confirm command completion')
        await device_client.patch_twin_reported_properties({'rundiagnostics': {'value': f'Diagnostics run complete at {datetime.datetime.today()}.'}})

      async def turnon_command(request):
        print('Turning on the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      async def turnoff_command(request):
        print('Turning off the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      commands = {
        'blink': blink_command,
        'rundiagnostics': diagnostics_command,
        'turnon': turnon_command,
        'turnoff': turnoff_command,
      }

      # Define behavior for handling commands
      async def command_listener():
        while True:
          method_request = await device_client.receive_method_request()  # Wait for commands
          await commands[method_request.name](method_request)
    ```

    命令的名称（`blink`、`turnon`、`turnoff` 和 `rundiagnostics`）必须与设备模板中使用的名称相匹配。

    目前，IoT Central 不使用设备功能模型中定义的响应架构。 对于同步命令，响应有效负载可以是任何有效 JSON。 对于异步命令，在工作完成后，设备应立即返回 202 响应，后接报告属性更新。 报告属性更新的格式为：

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    操作员可以在命令历史记录中查看响应有效负载。

1. 将以下函数添加到 `main` 函数中，以处理从 IoT Central 应用程序发送的属性更新。 设备响应[可写属性更新](concepts-telemetry-properties-commands.md#writeable-property-types)而发送的消息必须包含 `av` 和 `ac` 字段。 `ad` 字段为可选字段：

    ```python
      async def name_setting(value, version):
        await asyncio.sleep(1)
        print(f'Setting name value {value} - {version}')
        await device_client.patch_twin_reported_properties({'name' : {'value': value, 'ad': 'completed', 'ac': 200, 'av': version}})

      async def brightness_setting(value, version):
        await asyncio.sleep(5)
        print(f'Setting brightness value {value} - {version}')
        await device_client.patch_twin_reported_properties({'brightness' : {'value': value, 'ad': 'completed', 'ac': 200, 'av': version}})

      settings = {
        'name': name_setting,
        'brightness': brightness_setting
      }

      # define behavior for receiving a twin patch
      async def twin_patch_listener():
        while True:
          patch = await device_client.receive_twin_desired_properties_patch() # blocking
          to_update = patch.keys() & settings.keys()
          await asyncio.gather(
            *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
          )
    ```

    当操作员在 IoT Central 应用程序中设置可写属性时，该应用程序会使用设备孪生所需属性将值发送到设备。 然后，设备使用设备孪生报告属性做出响应。 IoT Central 在收到报告属性值时会使用 synced 状态更新属性视图。

    属性的名称（`name` 和 `brightness`）必须与设备模板中使用的名称相匹配。

1. 将以下函数添加到 `main` 函数中，以控制应用程序：

    ```python
      # Define behavior for halting the application
      def stdin_listener():
        while True:
          selection = input('Press Q to quit\n')
          if selection == 'Q' or selection == 'q':
            print('Quitting...')
            break

      device_client = await connect_device()

      if device_client is not None and device_client.connected:
        print('Send reported properties on startup')
        await device_client.patch_twin_reported_properties({'state': 'true', 'processorArchitecture': 'ARM', 'swVersion': '1.0.0'})
        tasks = asyncio.gather(
          send_telemetry(),
          command_listener(),
          twin_patch_listener(),
        )

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)

        # Wait for user to indicate they are done listening for method calls
        await user_finished

        # Cancel tasks
        tasks.add_done_callback(lambda r: r.exception())
        tasks.cancel()
        await device_client.disconnect()

      else:
        print('Device could not connect')
    ```

1. 保存 environmental_sensor.py 文件。

## <a name="run-your-python-application"></a>运行 Python 应用程序

若要启动设备客户端应用程序，请在命令行环境中运行以下命令：

```cmd
python3 environmental_sensor.py
```

可以看到设备连接到 Azure IoT Central 应用程序并开始发送遥测数据：

![运行客户端应用程序](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

可以看到设备如何对命令和属性更新做出响应：

![观察客户端应用程序](media/tutorial-connect-device-python/run-application-2.png)

## <a name="view-raw-data"></a>查看原始数据

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>后续步骤

作为设备开发人员，现在你已了解了如何使用 Python 创建设备的基础知识，一些建议的后续步骤是：

* 要详细了解在实现设备代码时设备模板的作用，请阅读[什么是设备模板？](./concepts-device-templates.md)。
* 阅读[连接到 Azure IoT Central](./concepts-get-connected.md)，详细了解如何向 IoT Central 注册设备以及 IoT Central 如何保护设备连接。

若要继续浏览 IoT Central 系列教程并详细了解如何构建 IoT Central 解决方案，请参阅：

> [!div class="nextstepaction"]
> [创建网关设备模板](./tutorial-define-gateway-device-type.md)
