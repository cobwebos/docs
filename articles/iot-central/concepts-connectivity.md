---
title: Azure IoT Central 中的设备连接 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 中的设备连接相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 7e90fb6bcfa1bfab59177cbc6c717fefc163a67a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960087"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Azure IoT Central 中的设备连接

本文介绍与 Microsoft Azure IoT Central 中的设备连接相关的重要概念。

Azure IoT Central 使用 [Azure IoT 中心设备预配服务 (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)，使 IoT Central 能够支持载入并连接大量设备。

-   客户现在可以脱机生成设备凭据并配置设备，而不需要先在 IoT Central 中注册设备
-   IoT 中心支持使用行业推荐的基于 X509 证书的连接的设备连接，同时继续支持和改进共享访问签名 (SAS) 连接
-   IoT Central 客户现在可以凭借其自己的设备 ID 在 IoT Central 中注册设备，实现与现有后端办公系统的简单集成
-   可以通过一种一致的方式来将设备连接到 IoT Central 

>[!NOTE]
>IoT Central 使用下方的 Azure IoT 设备预配服务 (DPS) 用于所有设备注册和连接，[了解有关 DPS 的详细信息](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)。

根据用例，按照说明将设备连接到 IoT Central
1. [快速连接单个设备（使用共享访问签名）](#connect-a-single-device)
1. [使用共享访问签名 (SAS) 连接大量设备](#connect-devices-at-scale-using-shared-access-signatures)
1. [使用 X509 证书连接大量设备](#connect-devices-using-x509-certificates) 建议用于生产工作负载
1. [直接连接而无需首先注册设备](#connect-without-first-registering-devices) 


>[!NOTE]
>对于要连接和预配的设备，全局终结点如下：global.azure-devices-provisioning.net

## <a name="connect-a-single-device"></a>连接单个设备
使用 SAS 可将单个设备轻松连接到 IoT Central，并且仅需几步即可完成 
1. 在 Device Explorer 中添加真实设备，单击“+新增”>“真实设备”以添加真实设备。
    * 输入设备 ID<span style="color:Red">（须小写）</span>或使用所建议的设备 ID。
    * 输入设备名或使用建议名称   
    ![添加设备](media/concepts-connectivity/add-device.png)
1. 通过单击设备页上的“连接”，获取已添加设备的作用域 ID、设备 ID 和主键等连接详细信息。
    * [作用域 ID](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) 为每个 IoT Central 应用一个，且由 DPS 生成，用于确保应用内的唯一设备 ID。
    * “设备 ID”对每个应用都是唯一的，设备需要在注册调用过程中发送设备 ID。   
    * “主键”是 SAS 令牌，是 IoT Central 为此特定设备生成的。 
    ![连接详细信息](media/concepts-connectivity/device-connect.PNG)
1. 在设备代码中使用以下连接详细信息：设备标识、设备名称和设备主键，用于预配和连接设备并即时查看数据流。 如果使用 MxChip 设备，请按照[此处的逐步说明](howto-connect-devkit.md#add-a-real-device)，从“准备 DevKit 设备”部分开始。   

    下面是你可能想要使用的其他语言的参考。

    *   **C 语言：** 如果使用的是 C，请按照[此 C 示例设备客户端](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)来连接示例设备。 在示例中使用以下设置。   

         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
         
         static const char* const SYMMETRIC_KEY_VALUE = "Enter Primary Symmetric key here";

         static const char* const REGISTRATION_NAME = "Enter Device Id here";
        ```

    *   **Node.js：** 如果要使用 Node.js，请[使用此处的分步说明](tutorial-add-device.md#prepare-the-client-code)，从“准备客户端代码”部分开始。



## <a name="connect-devices-at-scale-using-shared-access-signatures"></a>使用共享访问签名连接大量设备

若要通过 IoT Central 使用 SAS 连接大量设备，包含两个步骤 
1. 通过 CSV 文件将设备导入 IoT Central 进行注册并导出包含设备连接详细信息的设备，用于连接设备
1. 设备设置：使用连接详细信息（作用域 ID、设备 ID 和主键）对设备进行编程，设备开启时，将能够调用预配服务来获取其连接信息/IoT Central 应用分配。

>[!NOTE]
>还可以使用高级选项，你可以连接设备，而无需首先在 IoT Central 中注册设备，请[在此处了解详细信息](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)。

**注册设备**

Azure IoT Central 通过 CSV 文件提供批量导入设备功能，以便将大量的设备连接到应用程序。 

CSV 文件要求：该 CSV 文件应包含以下列（和标头）
1.  IOTC_DeviceID<span style="color:Red">（应为小写）</span>
1.  IOTC_DeviceName（可选）



导入设备，以在应用程序中对其注册
1.  在左侧导航菜单中选择“Explorer”。
1.  在左面板中，选择要为其批量创建设备的设备模板。 
1.  单击“导入”，选择包含要导入的设备 ID 列表的 CSV 文件。
该 CSV 文件应包含以下列（和标头）
    *   IOTC_DeviceID<span style="color:Red">（应为小写）</span>
    *   IOTC_DeviceName（可选）
1.  导入完成后，设备网格上会显示成功消息。

导出设备以获取连接详细信息，“导出”将创建具有设备 ID、设备名称和设备密钥的 CSV 文件。 使用这些详细信息将设备连接到 IoT Central。
从应用程序中批量导出设备：
1.  在左侧导航菜单中选择“Explorer”。
1.  选择想要导出的设备，然后单击“导出”操作。
1.  一旦导出完成，将显示一条成功消息，并提供一个用来下载生成文件的链接。
1.  单击“成功消息”将文件下载到磁盘上的本地文件夹。
1.  导出的 CSV 文件将具有以下列信息：设备 ID、设备名称、设备主/辅助密钥以及主/次要证书指纹
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY


设备设置

在设备代码中使用这些连接详细信息，设备标识 (IOTC_DEVICEID)、设备主键 (IOTC_SASKEY_PRIMARY) 和作用域 ID，预配和连接设备。 如果尚无这些信息，请在 IoT Central 应用中通过“管理”>“设备连接”>“作用域 ID”获取作用域 ID。
如果使用 MxChip 设备进行连接，请按照[此处的逐步说明](howto-connect-devkit.md#add-a-real-device)，从“准备 DevKit 设备”部分开始。   

下面是你可能想要使用的其他语言的参考。

   *   **C 语言：** 如果使用的是 C，请按照[此 C 示例设备客户端](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)来连接示例设备。 在示例中使用以下设置。   
         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         static const char* const SYMMETRIC_KEY_VALUE = "Enter Primary Symmetric key here";
         static const char* const REGISTRATION_NAME = "Enter Device Id here";
        ```
    * **Node.js：** 如果要使用 Node.js，请[使用此处的分步说明](tutorial-add-device.md#prepare-the-client-code)，从“准备客户端代码”部分开始。


## <a name="connect-devices-using-x509-certificates"></a>使用 X509 证书连接设备

将 X.509 证书用作一种证明机制是扩大生产规模和简化设备设置的极佳途径。 X.509 证书通常是信任证书链中一系列证书中的一个，证书链中的每个证书均通过下一个更高级别证书的私钥进行签名，位于链顶端的证书是自签名的根证书。 这会建立一个委托的信任链，该信任链始于受信任的根证书颁发机构 (CA) 生成的根证书，期间是每个中间 CA，终结于设备上安装的最终实体“叶”证书。 有关详细信息，请参阅[使用 X.509 CA 证书进行设备身份验证](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview)。 

若要使用 X509 证书将设备连接到 IoT Central，包含三个关键步骤 
1. 通过验证用于生成设备证书的 X509 根/中间证书，在 IoT Central 应用中配置连接设置。  可通过两个步骤来配置 X509 证书的连接设置。  

    *   添加正在使用的 X509 根或中间证书，以生成叶设备证书。 转到“管理”>“设备连接”>“证书”。 
    
        ![连接设置](media/concepts-connectivity/connection-settings.PNG)
    *   **证书验证：** 验证证书所有权可以确保证书的上传者拥有此证书的私钥。 验证证书
        *  生成验证码，单击验证码字段旁边的按钮以生成验证码。 
        *  使用验证码，创建 X.509 验证证书，将证书保存为.cer 文件。 
        *  上传已签名的验证证书，并单击验证。

        ![连接设置](media/concepts-connectivity/verify-cert.png)
    *   **次要证书：** 在 IoT 解决方案的生命周期内，需要滚动更新证书。 滚动更新证书的两个主要原因是出现安全违规和证书过期。 辅助证书用于减少更新主证书时，因设备尝试预配而出现的停机时间。

    **仅用于测试目的** 
    
    下面是一些实用程序命令行工具，可用于生成 CA 证书和设备证书。

    * 如果使用 MxChip，此处提供[命令行工具](https://aka.ms/iotcentral-docs-dicetool)，可用于生成 CA 证书，请将其添加到 IoT Central 应用并验证该证书。 

    *   使用此[命令行工具](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )
        * 创建证书链（按照 GitHub 文档中的步骤 2）。 
         将证书另存为.cer 文件并上传到 IoT Central（主键）。   
        * 从 IoT Central 应用获取验证码，生成证书（按照 GitHub 文档中的步骤 3），并上传以验证。 
        * 使用设备 ID 创建叶证书，作为工具的参数（按照步骤 4）。 保存该证书并用于设备。     

1. 通过 CSV 文件将设备导入 IoT Central，对其进行注册。

1. **设备设置**：使用上传的根证书生成叶证书。 请确保在叶证书中使用设备 ID 作为 CNAME，并且采用小写形式。 下面是一个[命令行工具](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )，以生成叶/设备证书，仅用于测试目的。

    使用预配服务信息对设备进行编程，使其能够在设备开启时获取连接详细信息和 IoT Central 应用分配。    

    **更多参考** 
    *   [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases) 的示例实现。  

    *   [C 编写的示例设备客户端](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

>[!NOTE]
>使用设备 ID，作为生成设备叶证书时的 cname。

>[!NOTE]
>设备 ID 应为小写 
 
## <a name="connect-without-first-registering-devices"></a>直接连接而无需首先注册设备
IoT Central 支持的重要场景之一是使 OEM 批量生产设备，并在工厂中生成凭据并配置它们，而无需首先在 IoT Central 中进行注册。 设备开启并连接到 IoT Central 后，操作员将批准设备连接到 IoT Central 应用。

下面是使用此功能连接设备的流程

![连接设置](media/concepts-connectivity/device-connection-flow.PNG)


根据所选设备身份验证方案 (X509/SAS)，执行以下步骤

1. **连接设置** 
    * **X509 证书：**[添加和验证根/中间证书](#connect-devices-using-x509-certificates)，并在下一步中用它来生成设备证书。
    * **SAS：** 复制主密钥（此密钥是此 IoT Central 应用程序的组 SAS 密钥），并在下一步中用它来生成设备 SAS 密钥。 
![连接设置 SAS](media/concepts-connectivity/connection-settings-sas.png)

1. **生成设备凭据** 
    *   **证书 X509：** 使用已添加到此应用中的根/中间证书为设备生成叶证书。 请确保在叶证书中使用设备 ID 作为 CNAME，并且应<span style="color:Red">采用小写形式</span>。 下面是一个[命令行工具](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )，以生成叶/设备证书，用于测试。
    *   **SAS：** 可以使用此[命令行工具](https://www.npmjs.com/package/dps-keygen)生成 SAS 密钥。 使用上一步中的主 SAS 密钥（组 SAS 密钥）。 请确保设备 ID <span style="color:Red">采用小写形式</span>。

        使用以下说明生成设备 SAS 密钥           

        ```
        npm i -g dps-keygen
        ```
    
        **使用情况**
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **设备设置** 
    
     使用作用域 ID、设备 ID 和设备证书/SAS 密钥闪刷设备，然后开启设备，以连接到 IoT Central 应用。

1. **将设备连接到 IoT 中心：** 一旦打开，设备将连接到 DPS/IoT Central 进行注册。

1. **将设备关联到模板：** 连接的设备将在“Device Explorer”中的“未关联设备”下显示。 设备预配状态为“已注册”。 将设备与相应的设备模板相关联，并批准设备连接到 IoT Central 应用。 设备将获取 IoT Central 应用的连接详细信息，它将连接并开始发送数据。 设备预配现已完成，而“预配状态”变为“已预配”。

## <a name="device-provisioning-status"></a>设备预配状态
将真实设备连接到 Azure IoT Central 时，包含一系列步骤 
1. **已注册**：此设备是第一次注册，这意味着此设备在 IoT Central 中创建，并且具有设备的设备 ID。
出现以下情况时，设备已注册：  
    *   资源管理器中已添加新的真实设备
    *   在资源管理器中使用“导入”添加一组设备
    *   尚未注册但已通过有效凭据连接的设备将显示在“未关联”设备下面。 

    在上述所有情况下，“预配状态”均为“已注册”

1. **已预配**：下一步是设备使用有效的凭据连接时，IoT Central 将完成预配步骤（通过在 IoT 中心创建此设备）。 然后将向设备返回连接字符串，以连接和开始发送数据。 现在设备的“预配状态”将从“已注册”变为“已预配”。

1.  **已阻止**：操作员可以阻止某台设备，一旦设备被阻止，它就不能将数据发送到 IoT 中心，并且需要重置。 被阻止的设备的“预配状态”为“已阻止”。 操作员还可以取消阻止设备。 取消阻止设备后，设备的“预配状态”将返回其之前的“预配状态”（“已注册”或“已预配”）。 

## <a name="getting-device-connection-string"></a>获取设备连接字符串
可以通过以下步骤，获取指向 Azure IoT 中心的 IoT 中心设备连接字符串 
1. 从设备页（转到“设备”页并单击“连接”）中获取作用域 ID、设备 ID 和主键等连接详细信息 

    ![连接详细信息](media/concepts-connectivity/device-connect.PNG)

1. 使用下述命令行工具获取设备连接字符串。
    通过下述说明获取设备连接字符串  

    ```cmd/sh
    npm i -g dps-keygen
    ```
    **使用情况**

    若要创建连接字符串，在 bin/ 文件夹下找到二进制文件
    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```
    详细了解[此处的 dps-keygen 工具](https://www.npmjs.com/package/dps-keygen)。

## <a name="sdk-support"></a>SDK 支持

可以使用 Azure 设备 SDK 以最简单的方式在连接到 Azure IoT Central 应用程序的设备上实现代码。 以下设备 SDK 已发布：

- [适用于 C 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [适用于 Python 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [适用于 Node.js 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [适用于 Java 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [适用于 .NET 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

每个设备都使用可以标识设备的唯一连接字符串进行连接。 设备只能连接到已在其中注册的 IoT 中心。 在 Azure IoT Central 应用程序中创建真实设备时，该应用程序会为你生成一个可以使用的连接字符串。

## <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 功能和 IoT 中心连接

与 IoT 中心进行的所有设备通信都使用以下 IoT 中心连接选项：

- [设备到云的消息传递](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [设备孪生](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

下表对 Azure IoT Central 设备功能映射到 IoT 中心功能的具体情况进行了汇总：

| Azure IoT Central | Azure IoT 中心 |
| ----------- | ------- |
| 度量：遥测 | 设备到云的消息传递 |
| 设备属性 | 设备孪生报告属性 |
| 设置 | 设备孪生所需的和报告的属性 |

若要详细了解如何使用设备 SDK，请查看下面的某篇文章以获取示例代码：

- [将泛型 Node.js 客户端连接到 Azure IoT Central 应用程序](howto-connect-nodejs.md)
- [将 Raspberry Pi 设备连接到 Azure IoT Central 应用程序](howto-connect-raspberry-pi-python.md)
- [将 DevDiv 工具包设备连接到 Azure IoT Central 应用程序](howto-connect-devkit.md)。


## <a name="protocols"></a>协议

设备 SDK 支持下述用于连接到 IoT 中心的网络协议：

- MQTT
- AMQP
- HTTPS

若要了解这些不同的协议以及选择协议的指南，请参阅[选择通信协议](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols)。

如果设备无法使用这些受支持协议中的任何一种，可以使用 Azure IoT Edge 进行协议转换。 IoT Edge 支持其他边缘智能方案，可以将处理从 Azure IoT Central 应用程序卸载到边缘。

## <a name="security"></a>安全

在设备与 Azure IoT Central 之间交换的所有数据都经过加密。 如果设备已连接到任何面向设备的 IoT 中心终结点，则 IoT 中心会对从该设备发出的所有请求进行身份验证。 为了避免通过网络交换凭据，设备使用签名的令牌进行身份验证。 有关详细信息，请参阅[控制对 IoT 中心的访问](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)。

> [!NOTE]
> 目前，连接到 Azure IoT Central 的设备必须使用 SAS 令牌。 连接到 Azure IoT Central 的设备不支持 X.509 证书。

## <a name="next-steps"></a>后续步骤

了解 Azure IoT Central 中的设备连接后，建议接下来执行以下步骤：

- [准备和连接 DevKit 设备](howto-connect-devkit.md)
- [准备和连接 Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [将泛型 Node.js 客户端连接到 Azure IoT Central 应用程序](howto-connect-nodejs.md)
