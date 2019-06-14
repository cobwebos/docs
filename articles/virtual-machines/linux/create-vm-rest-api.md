---
title: 使用 Azure REST API 创建 Linux 虚拟机 | Microsoft Docs
description: 了解如何使用 Azure REST API 在 Azure 中创建使用托管磁盘和 SSH 身份验证的 Linux 虚拟机。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 2b078cd769a9b4e5e66fe132fd4ef73ec4621efc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60391355"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>使用 REST API 创建使用 SSH 身份验证的 Linux 虚拟机

Azure 中的 Linux 虚拟机 (VM) 包含各种资源（例如磁盘和网络接口）并定义了各种参数（例如位置、大小、操作系统映像以及身份验证设置）。

可以通过 Azure 门户、Azure CLI 2.0、许多 Azure SDK、Azure 资源管理器模板以及许多第三方工具（例如 Ansible 或 Terraform）创建 Linux VM。 从根本上说，所有这些工具都使用 REST API 来创建 Linux VM。

本文展示了如何使用 REST API 创建在 Ubuntu 18.04-LTS 上运行且使用托管磁盘和 SSH 身份验证的 Linux VM。

## <a name="before-you-start"></a>开始之前

在创建并提交请求之前，你需要具有以下项：

* 你的订阅的 `{subscription-id}`
  * 如果你有多个订阅，请参阅[使用多个订阅](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)
* 提前创建的 `{resourceGroupName}`
* 位于同一资源组中的[虚拟网络接口](../../virtual-network/virtual-network-network-interface.md)
* SSH 密钥对（如果还没有，可以[生成一个新的](mac-create-ssh-keys.md)）

## <a name="request-basics"></a>请求基础知识

若要创建或更新虚拟机，请使用下面的 *PUT* 操作：

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

除了 `{subscription-id}` 和 `{resourceGroupName}` 参数之外，还需要指定 `{vmName}`（`api-version` 是可选的，但本文使用 `api-version=2017-12-01` 进行了测试）

以下标头是必需的：

| 请求标头   | 描述 |
|------------------|-----------------|
| Content-Type：   | 必需。 设置为 `application/json`。 |
| Authorization：  | 必需。 设置为有效的 `Bearer` [访问令牌](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)。 |

有关使用 REST API 请求的一般信息，请参阅 [REST API 请求/响应的组件](/rest/api/azure/#components-of-a-rest-api-requestresponse)。

## <a name="create-the-request-body"></a>创建请求正文

下面的通用定义用来构建请求正文：

| Name                       | 需要 | Type                                                                                | 描述  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| 位置                   | True     | 字符串                                                                              | 资源位置。 |
| name                       |          | 字符串                                                                              | 虚拟机的名称。 |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | 指定虚拟机的硬件设置。 |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | 指定虚拟机磁盘的存储设置。 |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | 指定虚拟机的操作系统设置。 |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | 指定虚拟机的网络接口。 |

下面是一个示例请求正文。 请确保在 `{computerName}` 和 `{name}` 参数中指定 VM 名称，在 `networkInterfaces` 下指定你创建的网络接口的名称，在 `adminUsername` 和 `path` 中指定用户名，在 `keyData` 中指定 SSH 密钥的公共  部分（例如位于 `~/.ssh/id_rsa.pub` 中）。 你可能希望更改的其他参数包括 `location` 和 `vmSize`。  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

有关请求正文中的变量定义的完整列表，请参阅[虚拟机创建或更新请求正文定义](/rest/api/compute/virtualmachines/createorupdate#definitions)。

## <a name="sending-the-request"></a>发送请求

可以使用你喜欢使用的客户端发送此 HTTP 请求。 还可以通过单击“试用”按钮使用某个[浏览器中工具](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate)。 

### <a name="responses"></a>Responses

对于要创建或更新虚拟机的操作，有两个成功响应：

| Name        | 类型                                                                              | 描述 |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 正常      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Created | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | 已创建     |

下面是一个精简后的 *201 Created* 响应，它来自前面创建 VM 的示例请求正文，该响应表明已分配了 *vmId* 并且 *provisioningState* 为 *Creating*：

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

有关 REST API 响应的详细信息，请参阅[处理响应消息](/rest/api/azure/#process-the-response-message)。

## <a name="next-steps"></a>后续步骤

有关 Azure REST API 或其他管理工具（例如 Azure CLI 或 Azure PowerShell）的详细信息，请参阅以下资源：

- [Azure 计算提供程序 REST API](/rest/api/compute/)
- [Azure REST API 入门](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Azure PowerShell 模块](/powershell/azure/overview)
