---
title: "使用 Azure CLI (az.py) 创建 IoT 中心| Microsoft 文档"
description: "如何使用跨平台的 Azure CLI 2.0（预览版）(az.py) 创建 Azure IoT 中心。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 39c8c4944ef19379dc04e04a717ab60d305593c4
ms.openlocfilehash: c52d9a5fadf494cc066bee773543c9d67bb8334b


---
# <a name="create-an-iot-hub-using-the-azure-cli-20-preview"></a>使用 Azure CLI 2.0（预览版）创建 IoT 中心

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>介绍

可使用 Azure CLI 2.0（预览版）(az.py) 以编程方式创建和管理 Azure IoT 中心。 本文说明如何使用 Azure CLI 2.0（预览版）(az.py) 创建 IoT 中心。

可以使用以下 CLI 版本之一完成任务：

* [Azure CLI (azure.js)](iot-hub-create-using-cli-nodejs.md) - 适用于经典部署模型和资源管理部署模型的 CLI。
* Azure CLI 2.0（预览版）(az.py) - 用于本文中所述的资源管理部署模型的下一代 CLI。

若要完成本教程，需要满足以下条件：

* 有效的 Azure 帐户。 如果没有帐户，只需几分钟即可创建一个[免费帐户][lnk-free-trial]。
* [Azure CLI 2.0（预览版）][lnk-CLI-install]。

## <a name="sign-in-and-set-your-azure-account"></a>登录并设置 Azure 帐户

登录 Azure 帐户，并将 Azure CLI 配置为与 IoT 中心资源配合使用。

1. 在命令提示符中，运行 [login 命令][lnk-login-command]：
    
    ```azurecli
    az login
    ```

    按照说明使用代码进行身份验证，并通过 Web 浏览器登录 Azure 帐户。

2. 如果有多个 Azure 订阅，登录 Azure 可获得与凭据关联的所有 Azure 帐户的访问权限。 使用以下[命令，列出可供使用的 Azure 帐户][lnk-az-account-command]：
    
    ```azurecli
    az account list 
    ```

    使用以下命令，选择想要用于运行命令以创建 IoT 中心的订阅。 可使用上一命令输出中的订阅名称或 ID：

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. 必须注册 IoT 提供程序才能部署 IoT 资源。 运行以下[命令，注册 IoT 提供程序][lnk-az-register-command]：
    
    ```azurecli
    az provider register -namespace "Microsoft.Devices"
    ```

4. 可能需要安装 Azure CLI _iot 组件_。 运行以下[命令，添加 iot 组件][lnk-az-addcomponent-command]：
    
    ```azurecli
    az component update --add iot
    ```

## <a name="create-an-iot-hub"></a>创建 IoT 中心

使用 Azure CLI 创建资源组，然后添加 IoT 中心。

1. 创建 IoT 中心时，必须在资源组中创建它。 使用现有资源组，或运行以下[命令创建资源组][lnk-az-resource-command]：
    
    ```azurecli
     az resource group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > 上一示例在美国西部位置创建资源组。 可运行 `az account list-locations -o table` 命令，查看可用位置的列表。
    >
    >

2. 运行以下[命令，在资源组中创建 IoT 中心][lnk-az-iot-command]：
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

> [!NOTE]
> IoT 中心的名称必须是全局唯一的。 上一命令在计费的 S1 定价层中创建 IoT 中心。 有关详细信息，请参阅 [Azure IoT 中心定价][lnk-iot-pricing]。
>
>

## <a name="remove-an-iot-hub"></a>删除 IoT 中心

可使用 Azure CLI [删除单个资源][lnk-az-resource-command]（例如 IoT 中心），或删除资源组及其所有资源（包括任何 IoT 中心）。

若要删除 IoT 中心，请运行以下命令：

```azurecli
az resource delete --name {your iot hub name} --resource-group {your resource group name} --resource-type Microsoft.Devices/IotHubs
```

若要删除资源组及其所有资源，请运行以下命令：

```azurecli
az resource group delete --name {your resource group name}
```

## <a name="next-steps"></a>后续步骤
若要深入了解如何开发 IoT 中心，请参阅以下内容：

* [IoT 中心开发人员指南][lnk-devguide]

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 Azure 门户管理 IoT 中心][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Dec16_HO2-->


