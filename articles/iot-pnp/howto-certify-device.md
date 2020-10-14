---
title: 如何认证 IoT 即插即用设备 |Microsoft Docs
description: 作为设备生成器，了解如何运行测试并提交设备以进行认证
author: konichi3
ms.author: koichih
ms.date: 08/21/2020
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5cdee274ebc815b23b8ce59e8b9eca90d00e3818
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042960"
---
# <a name="how-to-certify-iot-plug-and-play-devices"></a>如何认证 IoT 即插即用设备

IoT 即插即用设备认证计划包含用于检查设备是否符合 IoT 即插即用认证要求的工具。 这些工具还可帮助组织了解他们的 IoT 即插即用设备的可用性。 这些认证的设备专为 IoT 解决方案定制，有助于缩短投放市场的时间。

本文介绍如何：

- 安装适用于 Azure CLI 的 Azure IoT 命令行工具扩展
- 运行 IoT 即插即用测试，在开发阶段验证你的设备应用程序  
- 使用 Azure 认证的设备门户验证设备应用程序

## <a name="prepare-your-device"></a>准备设备

在 IoT 即插即用上运行的应用程序代码必须：

- 使用 [设备预配服务 (DPS) ](../iot-dps/about-iot-dps.md)连接到 Azure IoT 中心。
- 遵循 [IoT 即插即用约定](concepts-convention.md) 来实现遥测、属性和命令。

该应用程序是独立于操作系统安装的软件，或者与操作系统捆绑在闪存到设备的固件映像中。

认证过程通过验证设备实现是否匹配在 [数字孪生定义语言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl) 设备模型中定义的遥测、属性和命令以及该模型在 [Azure IoT 公共模型存储库](concepts-model-repository.md)中是否可用，来验证设备是否为 IoT 即插即用兼容。

为了满足认证要求，你的设备必须：

- 使用 [DPS](../iot-dps/about-iot-dps.md)连接到 Azure IoT 中心。
- 按照 IoT 即插即用约定实现遥测、属性或命令。
- 介绍与 [DTDL v2](https://aka.ms/dtdl) 模型的设备交互。
- 在[Azure IoT 公共模型存储库](https://devicemodels.azureiotsolutions.com/)中发布模型和所有所需的接口
- 在 dps 预配负载中，在 [dps 注册](concepts-developer-guide-device-csharp.md#dps-payload) 过程中发送模型 ID。
- 在 [MQTT 连接](concepts-developer-guide-device-csharp.md#model-id-announcement)过程中公告模型 ID。

## <a name="test-with-the-azure-iot-extension-cli"></a>使用 Azure IoT 扩展 CLI 进行测试

使用 [Azure IOT CLI 扩展](/cli/azure/ext/azure-iot/iot/product?preserve-view=true&view=azure-cli-latest) ，可以在提交设备以通过 Azure 认证的设备门户提交设备之前，验证设备实现是否与该模型匹配。

以下步骤演示了如何使用 CLI 准备和运行证书测试：

### <a name="install-the-azure-iot-extension-for-the-azure-cli"></a>安装适用于 Azure CLI 的 Azure IoT 扩展

请参阅安装说明，以在你的环境中设置 [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) 。

若要安装 Azure IoT 扩展，请运行以下命令：

```azurecli
az extension add --name azure-iot
```

若要了解详细信息，请参阅 [Azure IoT Azure CLI](/cli/azure/azure-cli-reference-for-iot?preserve-view=true&view=azure-cli-latest)。

### <a name="create-a-new-product-test"></a>创建新的产品测试

下面的命令通过对称密钥证明方法使用 DPS 创建测试：

- 创建要测试的新产品并生成测试配置。 输出显示设备必须用于预配的 DPS 信息：主密钥、设备 ID 和 ID 作用域。
- 指定文件夹，其中包含描述您的模型的 DTDL 文件。

```azurecli
az iot product test create --badge-type Pnp --at SymmetricKey --device-type FinishedProduct --models {local folder name}
```

> [!NOTE]
> 使用 CLI 时，需要 [登录](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest) 到订阅。

命令的 JSON 输出包含在 `primaryKey` `registrationId` `scopeID` 连接设备时要使用的、和。

预期输出：

```json
"deviceType": "FinishedProduct",
"id": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
"provisioningConfiguration": {
  "symmetricKeyEnrollmentInformation": {
    "primaryKey":"Ci/Ghpqp0n4m8FD5PTicr6dEikIfM3AtVWzAhObU7yc=",
    "registrationId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
    "scopeId": "0ne000FFA42"
  }
}
```

### <a name="connect-your-device"></a>连接设备

使用前面的命令输出的 DPS 信息将设备连接到 test IoT 中心实例。

### <a name="manage-and-configure-the-product-tests"></a>管理和配置产品测试

当设备连接并准备好与 IoT 中心交互时，生成产品测试配置文件。 创建文件：

- 使用 `id` 上一命令输出中的测试。
- 使用 `--wait` 参数获取测试用例。

```azurecli
az iot product test task create --type GenerateTestCases --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

预期输出：

```json
{
  "deviceTestId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
  "error": null,
  "id": "526da38e-91fc-4e20-a761-4f04b392c42b",
  "resultLink": "/deviceTests/d45d53d9-656d-4be7-9bbf-140bc87e98dc/TestCases",
  "status": "Completed",
  "type": "GenerateTestCases"
}
```

您可以使用 `az iot product test case update` 命令来修改测试配置文件。

### <a name="run-the-tests"></a>运行测试

生成测试配置后，下一步是运行测试。 使用 `devicetestId` 前面命令中的相同作为参数来运行测试。 检查测试结果以确保所有测试都具有状态 `Passed` 。

```azurecli
az iot product test task create --type QueueTestRun --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

示例测试运行输出

```json
      "validationTasks": [
        {
          "componentName": "Default component",
          "endTime": "2020-08-25T05:18:49.5224772+00:00",
          "interfaceId": "dtmi:com:example:TemperatureController;1",
          "logs": [
            {
              "message": "Waiting for telemetry from the device",
              "time": "2020-08-25T05:18:37.3862586+00:00"
            },
            {
              "message": "Validating PnP properties",
              "time": "2020-08-25T05:18:37.3875168+00:00"
            },
            {
              "message": "Validating PnP commands",
              "time": "2020-08-25T05:18:37.3894343+00:00"
            },
            {
              "message": "{\"propertyName\":\"serialNumber\",\"expectedSchemaType\":null,\"actualSchemaType\":null,\"message\":\"Property is successfully validated\",\"passed\":true,\"time\":\"2020-08-25T05:18:37.4205985+00:00\"}",
              "time": "2020-08-25T05:18:37.4205985+00:00"
            },
            {
              "message": "PnP interface properties validation passed",
              "time": "2020-08-25T05:18:37.4206964+00:00"
            },
```

## <a name="test-using-the-azure-certified-device-portal"></a>使用 Azure 认证设备门户进行测试

以下步骤演示了如何使用 [Azure 认证的设备门户](https://aka.ms/acdp) 来加入、注册产品详细信息、提交入门指南以及运行认证测试。

### <a name="onboarding"></a>登记

若要使用 [证书门户](https://aka.ms/acdp)，必须使用工作或学校租户中的 Azure Active Directory。

若要将模型发布到 Azure IoT 公共模型存储库，你的帐户必须是 [Microsoft 合作伙伴网络](https://partner.microsoft.com)的成员。 系统将检查 Microsoft 合作伙伴网络 ID 是否存在，并在发布到设备目录之前完全审查帐户。

### <a name="company-profile"></a>公司配置文件

可以从左侧导航菜单管理公司配置文件。 公司配置文件包含公司 URL、电子邮件地址和公司徽标。 在运行任何证书操作之前，必须在此页上接受程序协议。

公司配置文件信息在设备目录中的设备描述展示中使用。

### <a name="create-new-project"></a>创建新项目

若要验证设备，必须首先创建一个新项目。

导航到 [证书门户](https://aka.ms/acdp)。 在 " **项目** " 页上，选择 " *+ 创建新项目*"。 然后，输入项目的名称和设备名称，然后选择设备类。

在认证过程中提供的产品信息分为四类：

- 设备信息。 收集有关设备的信息，例如设备的名称、描述、证书和操作系统。
- **入门**指南。 在发布设备之前，你必须以 PDF 文档的形式提交指南以供系统管理员批准。
- 市场营销详细信息。 为设备提供客户可用的营销信息。 营销信息包括为说明、照片和分销商。
- 其他行业认证。 此可选部分允许你提供有关设备已获取的任何其他证书的其他信息。

### <a name="connect-and-test"></a>连接和测试

"连接和测试" 步骤检查设备是否符合 IoT 即插即用认证要求。

需要完成三个步骤：

1. 连接并发现接口。 设备必须通过 DPS 连接到 Azure IoT 认证服务。 选择身份验证方法 (x.509 证书、对称密钥或受信任的平台模块) 使用并使用 DPS 信息更新设备应用程序。
1. 查看接口。 查看接口，确保每个接口都具有可用于测试的有效负载输入。
1. 测试。 系统将测试每个设备模型，以检查模型中所述的遥测、属性和命令是否遵循 IoT 即插即用约定。 测试完成后，请选择 " **查看日志** " 链接以查看来自设备的遥测数据以及发送到 IoT 中心设备克隆属性的原始数据。

### <a name="submit-and-publish"></a>提交并发布

最终要求的阶段是提交项目以供评审。 此步骤通知 Azure 认证的设备团队成员查看你的项目的完整性，包括设备和营销详细信息以及入门指南。 在批准之前，团队成员可以通过与问题相关的公司电子邮件地址与你联系。

如果你的设备需要进一步的手动验证作为认证的一部分，此时你会收到通知。

在认证设备后，你可以选择使用产品摘要页中的 " **发布到目录** " 功能将产品详细信息发布到 Azure 认证设备目录。

## <a name="next-steps"></a>后续步骤

现在设备提交已完成，你可以联系设备认证团队 [iotcert@microsoft.com](mailto:iotcert@microsoft.com) 以继续执行后续步骤，其中包括 Microsoft 合作伙伴网络成员身份验证和入门指南。 满足所有要求后，可以选择将设备包含在 [认证的 Azure IoT 设备目录](https://aka.ms/devicecatalog)中。