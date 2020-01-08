---
title: 认证 IoT 即插即用预览版设备 | Microsoft Docs
description: 本教程介绍如何将产品信息添加到 Azure IoT 认证设备目录、将设备连接到 Azure IoT 认证服务，然后运行 IoT 即插即用认证测试。
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550156"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>教程：认证 IoT 即插即用预览版设备

若要在 [Azure IoT 认证设备目录](https://aka.ms/iotdevcat)中发布 IoT 即插即用预览版设备，必须通过一系列认证测试。 使用 [Azure IoT 认证](https://aka.ms/ACFI)门户提交设备进行认证。 [Azure IoT 认证服务](https://aka.ms/azure-iot-aics)运行认证测试。

在本教程中，学习以下内容：

> [!div class="checklist"]
> * IoT 即插即用认证要求是什么。
> * 如何将产品名称和信息添加到门户。
> * 如何连接和发现 IoT 即插即用接口。
> * 如何查看 IoT 即插即用接口和运行认证测试。
> * 如何将认证的 IoT 即插即用设备发布到目录。

## <a name="prerequisites"></a>必备条件

要完成本教程，需要：

* Microsoft 合作伙伴中心帐户。
* Microsoft 合作伙伴网络 ID。

有关详细信息，请参阅[如何在 Azure IoT 认证门户中登记](howto-onboard-portal.md)。

## <a name="certification-requirements"></a>认证要求

若要认证你的 IoT 即插即用设备，该设备必须满足以下要求：

* IoT 即插即用设备代码必须已安装在你的设备上。
* IoT 即插即用设备代码是使用 Azure IoT SDK 生成的。
* 设备代码必须支持 [Azure IoT 中心设备预配服务](../iot-dps/about-iot-dps.md)。
* 设备代码必须实现[设备信息接口](concepts-common-interfaces.md)。
* 功能模型和设备代码可在 IoT Central 正常运行。

当前包含在目录中的所有设备被视为预先认证的 IoT 即插即用设备。 不保证 IoT 即插即用软件组件（例如 SDK 和数字孪生定义语言）的最终产品的质量与合规性。

在 IoT 即插即用推出正式版后，所有预先认证的 IoT 即插即用设备必须根据 Microsoft 提供的最终版认证要求和软件组件重新认证。

## <a name="add-product-name"></a>添加产品名称

“产品”是客户可以购买的产品模型的易记名称。  若要添加产品：

1. 登录到 [Azure IoT 认证](https://aka.ms/ACFI)门户。
1. 通过门户中的左侧菜单转到“产品”页，然后选择“+ 新建”。  
1. 输入易记的产品名称，然后选择“创建”。  此处输入的名称不同于设备目录中显示的名称。

## <a name="add-product-information"></a>添加产品信息

在门户中成功创建产品后，该产品将显示在“产品”页中。  若要添加产品信息：

1. 选择创建的产品链接（可在“产品”页上的产品列中找到）。  状态应是草稿状态。
1. 选择“产品信息”标题旁边的“编辑”链接。   在产品信息页中输入有关产品的信息，并确保填写所有必填字段。
1. 选择“保存”。  仅当已填写所有必填字段时，“保存”按钮才会显示。  如果字段不完整，该按钮将显示为“保存并稍后完成”。 
1. 检查输入的内容。 填写所有必填字段，以将设备发布到设备目录。 始终可以在产品详细信息页中返回到前面的步骤来对产品信息进行编辑，只需单击“产品信息”标题旁边的“编辑”链接即可。  

## <a name="connect-and-discover-interfaces"></a>连接和发现接口

若要运行认证测试，请将设备连接到门户中显示的 [Azure IoT 认证服务](https://aka.ms/azure-iot-aics) (AICS)。

只需运行这些步骤一次即可运行认证测试，且无需更改产品设备代码。 若要开始，请遵循以下步骤连接到 AICS：

1. 使用合作伙伴中心帐户登录到门户。
1. 单击“连接 + 测试”启动认证流。 
1. 选择用于通过 [Azure IoT 中心设备预配服务](../iot-dps/about-iot-dps.md)将设备预配到 AICS 的[身份验证方法](../iot-dps/concepts-security.md#attestation-mechanism)。
   * 如果使用 [X.509 证书](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)，请上传生成的 X.509 证书。 建议查看用于演示如何使用 X.509 证书的示例代码：[C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c)、[C#](../iot-hub/iot-hub-security-x509-get-started.md)。
   * 如果使用[对称密钥](../iot-dps/concepts-symmetric-key-attestation.md)，请将对称密钥复制并粘贴到设备代码中。
   * 目前不支持 TPM 身份验证方法。
1. 将以下生成的 ID 复制并粘贴到设备代码中。
   * [注册 ID](../iot-dps/use-hsm-with-sdk.md)
   * [DPS ID](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [DPS 终结点](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. 生成设备代码并将其部署到设备后，选择“连接”以发现 IoT 即插即用接口。 
1. 成功连接到 AICS 后，选择“下一步”查看已发现的 IoT 即插即用接口。 

## <a name="run-tests-and-publish-the-device"></a>运行测试和发布设备

在复查页上，可以复查发现的 IoT 即插即用接口。 使用此页可以检查接口中实现的基元是否正确显示。 还可以检查接口的位置。

1. 请确保在必填字段中提供有效负载输入。 这些字段包括所指定接口的命令基元的有效负载信息。
1. 输入所有必需的信息后，选择“下一步”。 
1. 若要对实现的 IoT 即插即用接口运行测试，请选择“运行测试”。 
1. 所有测试将自动运行。 如果任何测试失败，请选择“查看日志”，以查看来自 AICS 的错误消息，以及发送到 Azure IoT 中心的原始遥测数据。 
1. 若要完成认证测试，请选择“完成”。 
1. 将认证的 IoT 即插即用设备发布到目录。 若要将认证的设备添加到目录，请在工具栏上选择“添加到目录”。  如果“添加到目录”灰显，则表示产品信息不完整，或测试失败。  
1. 选择“已认证且在目录中”链接可查看设备目录中已发布的设备。

## <a name="next-steps"></a>后续步骤

了解如何认证 IoT 即插即用设备后，建议接下来详细了解如何管理功能模型：

> [!div class="nextstepaction"]
> [管理模型](./howto-manage-models.md)
