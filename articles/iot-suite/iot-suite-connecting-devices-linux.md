---
title: 使用 C 将 Linux 设备预配到远程监视 - Azure | Microsoft Docs
description: 介绍如何使用在 Linux 上运行的以 C 编写的应用程序将设备连接到 Azure IoT 套件预配置远程监视解决方案。
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 2c6b245e938b44891314c3e752c064ff152f2c9a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>将设备连接到远程监视预配置解决方案 (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本教程介绍如何将物理设备连接到远程监视预配置解决方案。

## <a name="create-a-c-client-project-on-linux"></a>在 Linux 上创建 C 客户端项目

与受约束设备上运行的大多数嵌入式应用程序一样，设备应用程序的客户端代码是用 C 语言编写的。在本教程中，将在运行 Ubuntu (Linux) 的计算机上生成应用程序。

若要完成这些步骤，需要一个运行 Ubuntu 版本 15.04 或更高版本的设备。 继续操作之前，请使用以下命令在 Ubuntu 设备上安装必备组件包：

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>在设备上安装客户端库

Azure IoT 中心客户端库以包的形式提供，可以使用 **apt-get** 命令在 Ubuntu 设备上安装该包。 完成以下步骤，在 Ubuntu 计算机上安装包含 IoT 中心客户端库和标头文件的包：

1. 在外壳程序中，向计算机添加 AzureIoT 库：

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. 安装 azure-iot-sdk-c-dev 包

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>安装 Parson JSON 分析器

IoT 中心客户端库使用 Parson JSON 分析器分析消息有效负载。 在计算机的适当文件夹中，使用以下命令克隆 Parson GitHub 存储库：

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>准备项目

在 Ubuntu 计算机上，创建名为 `remote_monitoring` 的文件夹。 在 `remote_monitoring` 文件夹中：

- 创建四个文件：`main.c`、`remote_monitoring.c`、`remote_monitoring.h` 和 `CMakeLists.txt`。
- 创建名为 `parson` 的文件夹。

将文件 `parson.c` 和 `parson.h` 从 Parson 存储库的本地副本复制到 `remote_monitoring/parson` 文件夹。

在文本编辑器中打开 `remote_monitoring.c` 文件。 添加以下 `#include` 语句：

```c
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include <string.h>
```

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="add-code-to-run-the-app"></a>添加代码以运行应用

在文本编辑器中打开 `remote_monitoring.h` 文件。 添加以下代码：

```c
void remote_monitoring_run(void);
```

在文本编辑器中打开 `main.c` 文件。 添加以下代码：

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

## <a name="build-and-run-the-application"></a>构建并运行应用程序

以下步骤描述如何使用 *CMake* 生成客户端应用程序。

1. 在文本编辑器中，打开 `remote_monitoring` 文件夹中的 **CMakeLists.txt** 文件。

1. 添加以下指令，以定义如何生成客户端应用程序：

    ```cmake
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```

1. 在 `remote_monitoring` 文件夹中，创建一个文件夹以存储 CMake 生成的 *make* 文件。 然后运行 **cmake** 和 **make** 命令，如下所示：

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. 运行客户端应用程序，并将遥测数据发送到 IoT 中心：

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
