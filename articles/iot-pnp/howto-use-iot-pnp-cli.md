---
title: 使用适用于 Azure CLI 的 Azure IoT 扩展与 IoT 即插即用预览版设备交互 | Microsoft Docs
description: 安装适用于 Azure CLI 的 Azure IoT 扩展，并使用它与连接到 IoT 中心的 IoT 即插即用设备交互。
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: dadb1f044547acd6e5f0d274143123e89d7dae46
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475475"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>安装并使用适用于 Azure CLI 的 Azure IoT 扩展

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 是一个开放源代码跨平台命令行工具，用于管理 IoT 中心等 Azure 资源。 Azure CLI 适用于 Windows、Linux 和 macOS。 利用 Azure CLI，你可以管理 Azure IoT 中心资源、设备预配服务实例和链接中心，而无需安装任何扩展。

适用于 Azure CLI 的 Azure IoT 扩展是一个命令行工具，用于与 IoT 即插即用预览版设备交互并测试它们。 可以使用该扩展执行以下操作：

- 连接到设备。
- 查看设备发送的遥测数据。
- 使用设备属性。
- 调用设备命令。

本文介绍如何：

- 安装并配置适用于 Azure CLI 的 Azure IoT 扩展。
- 使用该扩展与设备交互并测试它们。

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>安装适用于 Azure CLI 的 Azure IoT 扩展

### <a name="step-1---install-the-azure-cli"></a>步骤 1 - 安装 Azure CLI

按照[安装说明](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)在环境中安装 Azure CLI。 为获得最佳体验，Azure CLI 版本应为2.9.1 或更高版本。 请使用 `az -–version` 验证版本。

### <a name="step-2---install-iot-extension"></a>步骤 2 - 安装 IoT 扩展

[IoT 扩展自述文件](https://github.com/Azure/azure-iot-cli-extension)介绍了该扩展的多种安装方法。 最简单的方法是运行 `az extension add --name azure-iot`。 安装完成后，可以使用 `az extension list` 验证当前安装的扩展，或使用 `az extension show --name azure-iot` 查看有关 IoT 扩展的详细信息。 编写时，扩展版本号为 `0.9.7` 。

若要删除该扩展，可以使用 `az extension remove --name azure-iot`。

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>使用适用于 Azure CLI 的 Azure IoT 扩展

### <a name="prerequisites"></a>先决条件

若要登录 Azure 订阅，请运行以下命令：

```azurecli
az login
```

若要使用适用于 Azure CLI 的 Azure IoT 扩展，需要：

- Azure IoT 中心。 可以通过多种方式将 IoT 中心添加到 Azure 订阅，例如[使用 Azure CLI 创建 IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 需要 IoT 中心连接字符串才能运行 Azure IoT 扩展命令。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 在 IoT 中心注册的设备。 可以使用以下 Azure CLI 命令注册设备，请确保将 `{YourIoTHubName}` 和 `{YourDeviceID}` 占位符替换为你的值：

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>与设备交互

可以使用该扩展查看连接到 IoT 中心的 IoT 即插即用设备并与之交互。 该扩展适用于表示 IoT 即插即用设备的数字孪生。

#### <a name="list-devices"></a>列出设备

列出 IoT 中心上的所有设备：

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>查看数字输出

查看设备的数字输出：

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>属性

设置读写属性的值：

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>命令

调用命令：

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>数字孪生事件

监视从特定设备和接口发送到 $Default 事件中心使用者组的所有 IoT 即插即用数字孪生事件：

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

### <a name="manage-models-in-the-model-repository"></a>管理模型存储库中的模型

您可以使用 Azure CLI 模型存储库命令来管理存储库中的模型。

#### <a name="create-model-repository"></a>创建模型存储库

如果你是租户中的第一个用户，请为你的租户创建新的 IoT 即插即用公司存储库：

```azurecli
az iot pnp repo create
```

#### <a name="manage-model-repository-tenant-roles"></a>管理模型存储库租户角色

为特定资源创建用户或服务主体的角色分配。

例如，为 user@consoso.com 租户提供**ModelsCreator**的角色：

```azurecli
az iot pnp role-assignment create --resource-id {tenant_id} --resource-type Tenant --subject-id {user@contoso.com} --subject-type User --role ModelsCreator
```

或者为 user@consoso.com 特定模型提供**ModelAdministrator**的角色：

```azurecli
az iot pnp role-assignment create --resource-id {model_id} --resource-type Model --subject-id {user@contoso.com} --subject-type User --role ModelAdministrator
```

#### <a name="create-a-model"></a>创建模型

在公司存储库中创建新模型：

```azurecli
az iot pnp model create --model {model_json or path_to_file}
```

#### <a name="search-a-model"></a>搜索模型

列出与特定关键字匹配的模型：

```azurecli
az iot pnp model list -q {search_keyword}
```

#### <a name="publish-a-model"></a>发布模型

将位于公司存储库中的设备型号发布到公共存储库。

例如，使 ID 为的模型为 `dtmi:com:example:ClimateSensor;1` ：

```azurecli
az iot pnp model publish --dtmi "dtmi:com:example:ClimateSensor;1"
```

若要发布模型，必须满足以下要求：

- 公司或组织租户必须是 Microsoft 合作伙伴。 
- 用户或服务主体必须是存储库租户的 "**发布者**" 角色的成员。

## <a name="next-steps"></a>后续步骤

本操作指南文章介绍了如何安装和使用适用于 Azure CLI 的 Azure IoT 扩展，以便与 IoT 即插即用设备交互。 建议的下一步是了解如何在设备上使用[Azure IoT 浏览器](./howto-use-iot-explorer.md)。
