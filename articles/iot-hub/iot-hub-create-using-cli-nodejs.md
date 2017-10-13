---
title: "使用 Azure CLI 创建 IoT 中心 (azure.js) | Microsoft Docs"
description: "如何使用跨平台的 Azure CLI (azure.js) 创建 Azure IoT 中心。"
services: iot-hub
documentationcenter: .net
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: 46a17831-650c-41d9-b228-445c5bb423d3
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: boltean
ms.openlocfilehash: 5e37c6c5e8625ce446ab203f19f9a8b2f1cd5a46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>使用 Azure CLI 创建 IoT 中心

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>介绍

可使用 Azure CLI (azure.js) 以编程方式创建和管理 Azure IoT 中心。 本文介绍如何使用 Azure CLI (azure.js) 创建 IoT 中心。

可以使用以下 CLI 版本之一完成任务：

* Azure CLI (azure.js) – 如本文所述，用于经典部署模型和资源管理部署模型的 CLI。
* [Azure CLI 2.0 (az.py)](iot-hub-create-using-cli.md) - 适用于资源管理部署模型的下一代 CLI。

若要完成本教程，需要以下各项：

* 有效的 Azure 帐户。 如果没有帐户，只需几分钟即可创建一个[免费帐户][lnk-free-trial]。
* [Azure CLI 0.10.4][lnk-CLI-install] 或更高版本。 如果已经安装 Azure CLI，则可在命令提示符处使用以下命令验证当前版本：

```azurecli
azure --version
```

> [!NOTE]
> Azure 提供了用于创建和使用资源的两个不同部署模型：[Azure Resource Manager 模型和经典模型](../azure-resource-manager/resource-manager-deployment-model.md)。 Azure CLI 必须处于 Azure Resource Manager 模式：
>
> ```azurecli
> azure config mode arm
> ```

## <a name="set-your-azure-account-and-subscription"></a>设置 Azure 帐户和订阅

1. 在命令提示符处键入以下命令进行登录：

   ```azurecli
    azure login
   ```

   使用建议的 Web 浏览器和代码进行身份验证。
1. 如果有多个 Azure 订阅，则连接到 Azure 即有权访问与凭据关联的所有 Azure 订阅。 可以使用以下命令查看 Azure 订阅以及确定哪个订阅是默认订阅：

   ```azurecli
    azure account list
   ```

   若要设置订阅上下文，以便在其下运行其余命令，请使用：

   ```azurecli
    azure account set <subscription name>
   ```

1. 如果没有资源组，则可创建一个，将其命名为 **exampleResourceGroup**：

   ```azurecli
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> [使用 Azure CLI 管理 Azure 资源和资源组][lnk-CLI-arm]一文提供了如何使用 Azure CLI 管理 Azure 资源的详细信息。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

所需参数：

```azurecli
azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>
```

* **resource-group**。 资源组名称。 格式为 1-64 位长度不区分大小写的字母数字、下划线和连字符。
* **name**。 要创建的 IoT 中心的名称。 格式为 3-50 位长度不区分大小写的字母数字、下划线和连字符。
* **location**。 要预配 IoT 中心的位置（Azure 区域/数据中心）。
* **sku-name**。 sku 的名称，以下值之一：[F1, S1, S2, S3]。 有关最新的完整列表，请参阅 IoT 中心的定价页。
* **units**。 预配的单位数。 范围：F1 [1-1]: S1、S2 [1-200] : S3 [1-10]。 IoT Hub 单位数基于总消息计数和要连接的设备数。

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

若要查看所有可以创建的参数，可以在命令提示符处使用帮助命令：

```azurecli
azure iothub create -h
```

快速示例：若要在资源组 **exampleResourceGroup** 中创建名为 **exampleIoTHubName** 的 IoT 中心，请运行以下命令：

```azurecli
azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> 此 Azure CLI 命令为用户创建付费的 S1 标准 IoT 中心。 可以使用以下命令删除 IoT 中心 **exampleIoTHubName**：
>
> ```azurecli
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```

## <a name="next-steps"></a>后续步骤

若要详细了解如何开发 IoT 中心，请参阅以下文章：

* [IoT SDK][lnk-sdks]

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 Azure 门户管理 IoT 中心][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
