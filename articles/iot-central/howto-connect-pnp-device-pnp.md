---
title: 将 IoT 即插即用设备连接到 IoT Central |Microsoft Docs
description: 作为设备开发人员, 了解如何使用 Visual Studio Code 来创建和测试连接到 IoT Central 的 IoT 即插即用设备。
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6eb73c1c2b768d82143890415702950905f261f6
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997233"
---
# <a name="use-visual-studio-code-to-create-an-iot-plug-and-play-device-that-connects-to-iot-central"></a>使用 Visual Studio Code 创建连接到 IoT Central 的 IoT 即插即用设备

本操作方法指南介绍了如何执行以下操作:

* 作为操作员, 可以在 Azure IoT Central 应用程序中添加和配置真实设备。

* 作为设备开发人员, 使用 Visual Studio Code 创建连接到 IoT Central 应用程序的[IoT 即插即用](../iot-pnp/overview-iot-plug-and-play.md)设备。

使用[设备功能模型](./concepts-architecture-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)来定义连接到 IoT Central 的设备。 在本指南中, 您将使用定义环境传感器设备的模型。

设备开发人员使用该模型生成设备客户端代码, 生成器使用该模型在 IoT Central 中[创建设备模板](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。 该模型充当设备和 IoT Central 应用程序之间的协定。

本指南中介绍如何生成生成的代码的部分假设你使用的是 Windows。

本指南介绍如何：

* 在 IoT Central 中将设备功能模型导入设备模板
* 向显示设备遥测的模板添加仪表板
* 从模板添加真实设备
* 将设备功能模型导入 Visual Studio Code
* 从模型生成 C 设备客户端应用程序
* 生成 C 设备客户端应用程序并将其连接到 IoT Central

## <a name="prerequisites"></a>先决条件

若要在本指南中测试你的设备代码, 需要一个从**预览**应用程序模板创建的 IoT Central 应用程序。 如果还没有要使用的 IoT Central 应用程序, 请完成快速入门[创建 Azure IoT Central 应用程序 (预览功能)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json):

若要使用本指南中的设备功能模型, 你需要:

* [Visual Studio Code](https://code.visualstudio.com/download)：Visual Studio Code 可用于多个平台
* 适用于 Visual Studio Code 的 Azure IoT 设备工作台扩展。 使用以下步骤在 VS Code 中安装 Azure IoT 设备工作台扩展:

    1. 在 VS Code 中, 选择 "**扩展**" 选项卡。
    1. 搜索 " **Azure IoT 设备工作台**"。
    1. 选择“安装”。

    > [!NOTE]
    > 扩展中代码生成器的当前版本不支持**Geopoint**和**矢量**架构类型, 也不支持**加速**、**速度**和**位置**语义类型。 IoT Central 支持这些架构和语义类型。

    > [!NOTE]
    > 若要使用 IoT Central, 设备功能模型必须在同一文件中具有以内联方式定义的所有接口。

若要在本指南中生成 Windows 上生成的 C 代码, 你需要:

* [Visual studio (社区版、专业版或企业版)](https://visualstudio.microsoft.com/downloads/) -在安装 visual studio 时, 请确保包括**NuGet 包管理器**组件和具有**C++** 工作负载的桌面开发。
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/) -安装**CMake**时, 请选择 "**将 CMake 添加到系统路径**" 选项。
* Azure IoT C SDK 的本地副本:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

若要遵循本操作方法末尾的设备首个说明, 还需要安装:

* [Node.js](https://nodejs.org)
* [Dps-ssh-keygen 工具](https://www.npmjs.com/package/dps-keygen):

    ```cmd/sh
    npm i -g dps-keygen
    ```

## <a name="create-device-template"></a>创建设备模板

在**azure iot sdk-c**文件夹中创建名为**pnp_app**的文件夹, 其中包含你克隆的 azure iot c sdk。 从 GitHub 下载[EnvironmentalSensor capabilitymodel](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json)设备功能模型, 并将该文件保存在**pnp_app**文件夹中。 将的两个实例`<YOUR_COMPANY_NAME_HERE>`替换为你的名称。 仅使用字符 a-z、a-z、0-9 和下划线。 记下唯一标识你的设备功能模型`"@id"`的字段的全部值, 稍后需要用到它。 此模型包括两个接口:

* **设备**通用接口。
* **EnvironmentalSensor**自定义接口。

若要在 IoT Central 中为环境传感器设备创建设备模板:

1. 中转到 "**设备模板**" 页, 然后选择 " **+ 新建**"。 然后选择 "**自定义**"。

1. 输入**环境传感器**作为设备模板名称。

1. 选择 "**导入功能模型**"。 然后找到已创建的**EnvironmentalSensor** , 并选择 "**打开**"。 "**环境传感器功能" 模型**中显示了两个接口:**设备信息**和**环境传感器**。

1. 选择 "**视图**", 然后**可视化设备**。

1. 在可添加到仪表板的字段列表中, 选择两个**遥测**值 "**湿度**和**温度**", 然后选择 "**合并**"。 然后选择“保存”。

现在, 你有了一个使用**环境传感器**模型的设备模板, 该模板具有一个简单的仪表板, 可显示来自设备的遥测数据。

## <a name="publish-the-template-and-add-a-real-device"></a>发布模板并添加实际设备

若要发布模板并添加实际设备, 请在 "**设备模板**" 页中, 选择**环境传感器**设备模板。 选择 "**发布**", 查看信息, 然后选择 "**发布**"。

在连接客户端应用程序之前, 需要在 "**设备**" 页上添加设备并获取其连接信息:

1. 请在 "**设备**" 页上, 选择 "**环境传感器**"。 "**设备**" 页使你可以管理可连接到应用程序的设备。

1. 若要添加实际设备, 请选择 " **+ 新建**", 将 "设备 ID" 更改为**sensor01**, 然后选择 "**创建**"。 请确保**模拟**处于**关闭状态**。

1. 在设备列表中, 选择**环境传感器**设备。 然后选择 "**连接**"。

1. 记下 "**作用域 id**"、"**设备 ID**"和 "主密钥"。 然后选择 "**关闭**"。

## <a name="generate-a-device-client-application"></a>生成设备客户端应用程序

使用 Visual Studio Code 从设备功能模型生成主干设备客户端应用程序:

1. 启动 Visual Studio Code 并打开**pnp_app**文件夹。

1. 使用**Ctrl + Shift + P**打开命令面板。

1. 输入**即插即用**, 然后选择 "**生成设备代码存根**"。

1. 选择**EnvironmentalSensor capabilitymodel**设备功能模型文件。

1. 输入**sensor_app**作为项目名称。

1. 选择**ANSI C**作为语言。

1. 选择 " **CMake 项目**" 作为目标。

1. 选择 "**通过 DPS (设备预配服务) 对称密钥**" 作为连接方法。

Visual Studio Code 将在**sensor_app**文件夹中打开一个新窗口, 其中包含生成的 C 代码。

## <a name="add-connection-details-to-the-device-client"></a>将连接详细信息添加到设备客户端

若要将连接信息添加到设备客户端, 请在包含生成的代码的文件夹中打开 " **main .c** :

1. 替换`[DPS Id Scope]`为之前记下的 "**作用域 ID** " 值。

1. 将`[DPS symmetric key]`替换为前面记下的**主键**值。

1. 将`[device registration Id]`替换为您之前记下的 "**设备 ID** " 值。

1. 保存更改。

## <a name="build-and-run-the-client"></a>生成并运行客户端

若要生成并运行客户端, 需要为 Azure IoT C SDK 自定义生成:

1. 在**azure iot-sdk**文件夹的根目录中打开**cmakelists.txt**文件。

1. 在此文件的末尾添加以下行, 以将新的客户端应用包括在生成中:

    ```txt
    add_subdirectory(pnp_app/sensor_app)
    ```

1. 保存更改。

1. 打开命令提示符并导航到 " **azure iot-sdk-c** " 文件夹。

1. 若要生成应用程序, 请运行以下命令:

    ```cmd
    mkdir cmake
    cd cmake
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. 若要运行应用程序, 请在相同的命令提示符下运行以下命令:

    ```cmd
    pnp_app\sensor_app\Release\sensor_app.exe
    ```

## <a name="view-the-telemetry"></a>查看遥测

几分钟后, 你可以在 IoT Central 应用程序中从设备仪表板上的设备查看遥测数据。

## <a name="connect-device-first"></a>连接设备-第一项

可以按[连接](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)中所述, 通过设备首次连接连接 IoT 即插即用设备。 发现过程遵循以下顺序:

1. 如果已在 IoT Central 应用程序中发布, 则与设备模板关联。

1. 从已发布和已认证的功能模型的[公共存储库](https://aka.ms/ACFI)中提取功能模型。

使用上面引用 Visual Studio Code 和**生成设备代码存根 (Stub** ) 命令, 你可以按照以下步骤连接设备, 并将已发布的设备功能模型从公共存储库自动引入 IoT Central:

1. 使用已发布到公共存储库中的现有设备功能模型。 需要完整的设备功能模型, 并记下此模型的 URN。

1. 按照上述步骤[生成设备客户端应用程序](#generate-a-device-client-application), 以使用 Visual Studio Code 并生成设备代码。

1. 在 IoT Central 应用程序中, 请单击 "**管理**" 选项卡, 然后选择 "**设备连接**" 部分。 记下应用程序的 "**作用域 ID** " 和 "**主密钥**" 值。

    > [!NOTE]
    > 此页上显示的**主要密钥**是一个组共享访问签名, 可用于为应用程序生成多个设备密钥。

1. 使用[DPS ssh-keygen](https://www.npmjs.com/package/dps-keygen)工具从你记下的主密钥生成设备密钥。 若要生成设备密钥, 请运行以下命令:

    ```cmd/sh
    dps-keygen -mk:<Primary_Key> -di:<device_id>
    ```

1. 按照上一部分中的步骤[将连接详细信息添加到设备客户端](#add-connection-details-to-the-device-client), 以**添加作用域 id**、**主密钥**和**设备 id**。

1. 按照上一部分中的步骤[生成并运行客户端](#build-and-run-the-client)。

1. 现在, 你会看到设备连接到 IoT Central 应用程序, 并自动将公共存储库中的设备功能模型作为设备模板引入。

## <a name="next-steps"></a>后续步骤

现在, 你已了解如何将实际设备连接到 IoT Central, 接下来建议的下一步是详细了解[设置设备模板](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)中的设备模板
