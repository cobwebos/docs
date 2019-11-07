---
title: 使用适用于 Azure CLI 的 Azure IoT 扩展与 IoT 即插即用 Preview 设备交互 |Microsoft Docs
description: 安装适用于 Azure CLI 的 Azure IoT 扩展，并使用它与连接到 IoT 中心的 IoT 即插即用设备进行交互。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 09/08/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: eb4f607672c39d45b7791ccaeeb6f7cff9393cb9
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571018"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>安装并使用适用于 Azure CLI 的 Azure IoT 扩展

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)是一种用于管理 Azure 资源（例如 IoT 中心）的开源跨平台命令行工具。 Azure CLI 适用于 Windows、Linux 和 MacOS。 还会在[Azure Cloud Shell](https://shell.azure.com)中预安装 Azure CLI。 利用 Azure CLI，你可以管理 Azure IoT 中心资源、设备预配服务实例和链接中心，而无需安装任何扩展。

适用于 Azure CLI 的 Azure IoT 扩展是一个命令行工具，用于与测试 IoT 即插即用预览版设备进行交互。 你可以使用扩展来执行以下操作：

- 连接到设备。
- 查看设备发送的遥测数据。
- 使用设备属性。
- 调用设备命令。

本文介绍以下操作：

- 为 Azure CLI 安装和配置 Azure IoT 扩展。
- 使用该扩展与设备交互并对其进行测试。
- 使用扩展来管理模型存储库中的接口。

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>为 Azure CLI 安装 Azure IoT 扩展

### <a name="step-1---install-the-azure-cli"></a>步骤 1-安装 Azure CLI

按照[安装说明](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)在你的环境中设置 Azure CLI。 若要使用下面的所有命令，Azure CLI 版本必须是2.0.73 或更高版本。 请使用 `az -–version` 验证版本。

### <a name="step-2---install-iot-extension"></a>步骤 2-安装 IoT 扩展

[IoT 扩展自述文件](https://github.com/Azure/azure-iot-cli-extension)介绍了该扩展的多种安装方法。 最简单的方法是运行 `az extension add --name azure-cli-iot-ext`。 安装完成后，可以使用 `az extension list` 验证当前安装的扩展，或使用 `az extension show --name azure-cli-iot-ext` 查看有关 IoT 扩展的详细信息。 若要删除该扩展，可以使用 `az extension remove --name azure-cli-iot-ext`。

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>将 Azure IoT 扩展用于 Azure CLI

### <a name="prerequisites"></a>先决条件

若要登录到 Azure 订阅，请运行以下命令：

```cmd/sh
az login
```

> [!NOTE]
> 如果使用的是 Azure cloud shell，则会自动登录，无需运行上一个命令。

若要将 Azure IoT 扩展用于 Azure CLI，需要：

- Azure IoT 中心。 可以通过多种方式将 IoT 中心添加到 Azure 订阅，如[使用 Azure CLI 创建 iot 中心](../iot-hub/iot-hub-create-using-cli.md)。 需要 IoT 中心的连接字符串来运行 Azure IoT 扩展命令。 如果没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!NOTE]
    > 在公共预览版期间，IoT 即插即用功能仅适用于在**美国中部**、**北欧**和**日本东部**区域中创建的 iot 中心。

- 注册到 IoT 中心的设备。 你可以使用以下 Azure CLI 命令注册设备，请确保将 `{YourIoTHubName}` 和 `{YourDeviceID}` 占位符替换为你的值：

    ```cmd/sh
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- 某些命令需要公司模型存储库的连接字符串。 第一次加入[到 Azure IoT 认证门户](howto-onboard-portal.md)时，将创建公司的型号存储库。 第三方可能会与你共享其模型存储库连接字符串，以允许你访问其接口和模型。

### <a name="interact-with-a-device"></a>与设备交互

你可以使用该扩展来查看连接到 IoT 中心的 IoT 即插即用设备并与之交互。 此扩展适用于代表 IoT 即插即用设备的数字输出。

#### <a name="list-devices-and-interfaces"></a>列出设备和接口

列出 IoT 中心上的所有设备：

```cmd/sh
az iot hub device-identity list --hub-name {YourIoTHubName}
```

列出 IoT 即插即用设备注册的所有接口：

```cmd/sh
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>属性

列出设备上某个接口的所有属性和属性值：

```cmd/sh
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

设置读写属性的值：

```cmd/sh
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

将设备**传感器**接口上的 "**名称**" 属性设置为 " **Contoso** " 的示例负载文件如下所示：

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>命令

列出设备上某个接口的所有命令：

```cmd/sh
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

如果没有 `--repo-login` 参数，此命令将使用公共模型存储库。

调用命令：

```cmd/sh
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>数字克隆事件

监视从特定设备和接口到 **$Default**事件中心使用者组的所有 IoT 即插即用数字克隆事件：

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

监视所有 IoT 即插即用来自特定设备的数字克隆事件，并将接口发送到特定的使用者组：

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>管理模型存储库中的接口

以下命令使用公共 IoT 即插即用模型存储库。 若要使用公司模型存储库，请将 `--login` 参数添加到模型存储库连接字符串中。

列出公共 IoT 即插即用模型存储库中的接口：

```cmd/sh
az iot pnp interface list
```

在公共 IoT 即插即用模型存储库中显示接口：

```cmd/sh
az iot pnp interface show --interface {YourInterfaceId}
```

在 IoT 即插即用公司模型存储库中创建一个接口：

```cmd/sh
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

不能直接在公共模型存储库中创建接口。

在 IoT 即插即用公司模型存储库中更新接口：

```cmd/sh
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

不能直接更新公共模型存储库中的接口。

将 IoT 即插即用公司模型存储库中的接口发布到公共模型存储库。 此操作使接口变得不可变：

```cmd/sh
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

只有 Microsoft 合作伙伴可以将接口发布到公共模型存储库。

### <a name="manage-device-capability-models-in-a-model-repository"></a>管理模型存储库中的设备功能模型

以下命令使用公共 IoT 即插即用模型存储库。 若要使用公司模型存储库，请将 `--login` 参数添加到模型存储库连接字符串中。

列出 IoT 即插即用公共模型存储库中的设备功能模型：

```cmd/sh
az iot pnp capability-model list
```

在 IoT 即插即用公共模型存储库中显示设备功能模型：

```cmd/sh
az iot pnp capability-model show --model {YourModelID}
```

在 IoT 即插即用公司模型存储库中创建设备功能模型：

```cmd/sh
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

不能直接在公共模型存储库中创建模型。

在 IoT 即插即用公司模型存储库中更新设备功能模型：

```cmd/sh
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

不能直接更新公共模型存储库中的模型。

将 IoT 即插即用公司模型存储库中的设备功能模型发布到公共模型存储库。 此操作使模型变得不可变：

```cmd/sh
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

只有 Microsoft 合作伙伴可以将模型发布到公共模型存储库。

## <a name="next-steps"></a>后续步骤

本操作指南文章介绍了如何安装和使用适用于 Azure CLI 的 Azure IoT 扩展，以便与即插即用设备交互。 建议的下一步是了解如何[管理模型](./howto-manage-models.md)。
