---
title: 使用 Azure CLI 配置到 IoT 中心的文件上传 | Microsoft Docs
description: 如何使用跨平台 Azure CLI 配置到 Azure IoT 中心的文件上传。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: df3c8d2abf59de6c9f685ad8d93e6689738df8e6
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302519"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>使用 Azure CLI 配置 IoT 中心文件上传

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

若要[从设备上传文件](iot-hub-devguide-file-upload.md)，必须先将 Azure 存储帐户与 IoT 中心关联。 可以使用现有存储帐户，也可以创建新的存储帐户。

若要完成本教程，您需要以下各项：

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

* Azure IoT 中心。 如果没有 IoT 中心，可以使用 [`az iot hub create` 命令](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create)创建一个，或[使用门户创建 IoT 中心](iot-hub-create-through-portal.md)。

* 一个 Azure 存储帐户。 如果没有 Azure 存储帐户，可以使用 Azure CLI 创建一个。 有关详细信息，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md)。

## <a name="sign-in-and-set-your-azure-account"></a>登录并设置 Azure 帐户

登录到 Azure 帐户，并选择订阅。

1. 在命令提示符中，运行 [login 命令](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)：

    ```azurecli
    az login
    ```

    按照说明使用代码进行身份验证，并通过 Web 浏览器登录 Azure 帐户。

2. 如果有多个 Azure 订阅，登录 Azure 可获得与凭据关联的所有 Azure 帐户的访问权限。 使用[以下命令列出可供使用的 Azure 帐户](https://docs.microsoft.com/cli/azure/account)：

    ```azurecli
    az account list
    ```

    使用以下命令，选择想要用于运行命令以创建 IoT 中心的订阅。 可使用上一命令输出中的订阅名称或 ID：

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>检索存储帐户详细信息

以下步骤假设已使用 **Resource Manager** 部署模型而不**经典**部署模型创建了存储帐户。

若要从设备配置文件上传，需要 Azure 存储帐户的连接字符串。 存储帐户必须与 IoT 中心位于同一订阅中。 还需要存储帐户中 Blob 容器的名称。 使用以下命令检索存储帐户密钥：

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

记下 **connectionString** 值。 在后续步骤中需要用到它。

可将现有的 Blob 容器用于文件上传，或新建一个容器：

* 若要列出存储帐户中的现有 Blob 容器，请使用以下命令：

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* 若要在存储帐户中创建 Blob 容器，请使用以下命令：

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>文件上传

现在可以使用存储帐户详细信息配置 IoT 中心以[将文件上传到 IoT 中心](iot-hub-devguide-file-upload.md)。

配置需要以下值：

* **存储容器**：当前 Azure 订阅中要与 IoT 中心关联的 Azure 存储帐户中的 Blob 容器。 检索在上一部分中必要的存储帐户信息。 IoT 中心会自动生成对此 Blob 容器具有写入权限的 SAS URI，以供设备上传文件时使用。

* 接收已上传文件的通知：启用或禁用文件上传通知。

* **SAS TTL**：此设置是 IoT 中心返回给设备的 SAS URI 生存时间。 默认设置为一小时。

* **文件通知设置默认 TTL**：文件上传通知到期前的生存时间。 默认设置为一天。

* **文件通知最大传送数**：IoT 中心将尝试传送文件上传通知的次数。 默认设置为 10。

使用以下 Azure CLI 命令在 IoT 中心内配置文件上传设置：

<!--Robinsh this is out of date, add cloud powershell -->

在 bash shell 中，使用：

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

可以使用以下命令在 IoT 中心内查看文件上传配置：

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>后续步骤

有关 IoT 中心文件上传功能的详细信息，请参阅[从设备上传文件](iot-hub-devguide-file-upload.md)。

若要了解有关如何管理 Azure IoT 中心的详细信息，请参阅以下链接：

* [批量管理 IoT 设备](iot-hub-bulk-identity-mgmt.md)
* [IoT 中心指标](iot-hub-metrics.md)
* [操作监视](iot-hub-operations-monitoring.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/tutorial-simulate-device-linux.md)
* [从根本上保护 IoT 解决方案](../iot-fundamentals/iot-security-ground-up.md)
