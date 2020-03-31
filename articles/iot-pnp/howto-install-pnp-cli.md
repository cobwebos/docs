---
title: 使用 Azure CLI 的 Azure IoT 扩展与 IoT 即插即用预览设备进行交互 |微软文档
description: 安装 Azure CLI 的 Azure IoT 扩展，并用它来与连接到 IoT 中心的 IoT 即插即用设备进行交互。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b5907c0fb127947e90352e68b2726a22f5afea0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234691"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>安装和使用 Azure CLI 的 Azure IoT 扩展

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)是一种开源跨平台命令行工具，用于管理 Azure 资源（如 IoT 中心）。 Azure CLI 在 Windows、Linux 和 MacOS 上可用。 Azure CLI 也预安装在 Azure[云外壳](https://shell.azure.com)中。 Azure CLI 允许您管理 Azure IoT 中心资源、设备预配服务实例和链接中心，而无需安装任何扩展。

Azure CLI 的 Azure IoT 扩展是一个命令行工具，用于与 IoT 即插即用预览设备进行交互和测试。 您可以使用扩展：

- 连接到设备。
- 查看设备发送的遥测数据。
- 使用设备属性。
- 调用设备命令。

本文介绍如何：

- 为 Azure CLI 安装和配置 Azure IoT 扩展。
- 使用扩展与设备交互和测试设备。
- 使用扩展管理模型存储库中的接口。

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>为 Azure CLI 安装 Azure IoT 扩展

### <a name="step-1---install-the-azure-cli"></a>步骤 1 - 安装 Azure CLI

按照[安装说明](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)在环境中设置 Azure CLI。 要使用以下所有命令，Azure CLI 版本必须是版本 2.0.73 或以上。 请使用 `az -–version` 验证版本。

### <a name="step-2---install-iot-extension"></a>步骤 2 - 安装 IoT 扩展

[IoT 扩展自述文件](https://github.com/Azure/azure-iot-cli-extension)介绍了该扩展的多种安装方法。 最简单的方法是运行 `az extension add --name azure-iot`。 安装完成后，可以使用 `az extension list` 验证当前安装的扩展，或使用 `az extension show --name azure-iot` 查看有关 IoT 扩展的详细信息。 若要删除该扩展，可以使用 `az extension remove --name azure-iot`。

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>对 Azure CLI 使用 Azure IoT 扩展

### <a name="prerequisites"></a>先决条件

要登录到 Azure 订阅，请运行以下命令：

```azurecli
az login
```

> [!NOTE]
> 如果使用 Azure 云外壳，则会自动登录，无需运行以前的命令。

要对 Azure CLI 使用 Azure IoT 扩展，需要：

- Azure IoT 中心。 有许多方法可以将 IoT 中心添加到 Azure 订阅，例如使用[Azure CLI 创建 IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 您需要 IoT 中心的连接字符串来运行 Azure IoT 扩展命令。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 在 IoT 中心注册的设备。 可以使用以下 Azure CLI 命令注册设备，请确保将`{YourIoTHubName}`和`{YourDeviceID}`占位符替换为值：

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- 某些命令需要公司模型存储库的连接字符串。 首次[登载为 IoT 门户的 Azure 认证](howto-onboard-portal.md)时，将创建公司的模型存储库。 第三方可能会与您共享其模型存储库连接字符串，以便允许您访问其接口和模型。

### <a name="interact-with-a-device"></a>与设备交互

您可以使用扩展查看连接到 IoT 中心的 IoT 即插即用设备并与之交互。 扩展适用于表示 IoT 即插即用设备的数字孪生。

#### <a name="list-devices-and-interfaces"></a>列出设备和接口

列出 IoT 中心上的所有设备：

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

列出 IoT 即插即用设备注册的所有接口：

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>属性

列出设备上接口的所有属性和属性值：

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

设置读写属性的值：

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

将设备**传感器**接口上**的名称**属性设置为**Contoso**的示例有效负载文件如下所示：

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

列出设备上接口的所有命令：

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

如果没有参数`--repo-login`，此命令将使用公共模型存储库。

调用命令：

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>数字双胞胎活动

监控来自特定设备和接口的所有 IoT 即插即用数字孪生事件，并连接到 **$Default**事件中心使用者组：

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

监控来自特定设备和特定消费群体的所有 IoT 即插即用数字孪生事件：

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>管理模型存储库中的接口

以下命令使用公共 IoT 即插即用模型存储库。 要使用公司模型存储库，`--login`请使用模型存储库连接字符串添加参数。

在公共 IoT 即插即用模型存储库中列出接口：

```azurecli
az iot pnp interface list
```

在公共 IoT 即插即用模型存储库中显示接口：

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

在 IoT 即插即用公司模型存储库中创建界面：

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

不能直接在公共模型存储库中创建接口。

更新 IoT 即插即用公司模型存储库中的界面：

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

不能直接更新公共模型存储库中的接口。

将接口从 IoT 即插即用公司模型存储库发布到公共模型存储库。 此操作使接口不可变：

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

只有 Microsoft 合作伙伴才能将接口发布到公共模型存储库。

### <a name="manage-device-capability-models-in-a-model-repository"></a>在模型存储库中管理设备功能模型

以下命令使用公共 IoT 即插即用模型存储库。 要使用公司模型存储库，`--login`请使用模型存储库连接字符串添加参数。

在 IoT 即插即用公共模型存储库中列出设备功能模型：

```azurecli
az iot pnp capability-model list
```

在 IoT 即插即用公共模型存储库中显示设备功能模型：

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

在 IoT 即插即用公司模型存储库中创建设备功能模型：

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

不能直接在公共模型存储库中创建模型。

更新 IoT 即插即用公司模型存储库中的设备功能模型：

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

不能直接更新公共模型存储库中的模型。

将设备功能模型从 IoT 即插即用公司模型存储库发布到公共模型存储库。 此操作使模型不可变：

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

只有 Microsoft 合作伙伴可以将模型发布到公共模型存储库。

## <a name="next-steps"></a>后续步骤

在本"执行"一文中，您学习了如何安装和使用 Azure CLI 的 Azure IoT 扩展与即插即用设备进行交互。 建议的下一步是学习如何[管理模型](./howto-manage-models.md)。
