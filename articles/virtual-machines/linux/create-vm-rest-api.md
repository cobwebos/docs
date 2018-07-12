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
ms.openlocfilehash: 0f77b46be0207b0ce96e6dc2562fb5298afbe36b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928176"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>使用 REST API 创建使用 SSH 身份验证的 Linux 虚拟机

Azure 中的虚拟机 (VM) 是通过各种参数（例如位置、硬件大小、操作系统映像和登录凭据）定义的。 本文展示了如何使用 REST API 创建使用 SSH 身份验证的 Linux 虚拟机。

若要创建或更新虚拟机，请使用下面的 *PUT* 操作：

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>创建请求

若要创建 *PUT* 请求，必须使用 `{subscription-id}` 参数。 如果拥有多个订阅，请参阅[使用多个订阅](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions)。 你将定义资源的 `{resourceGroupName}` 和 `{vmName}` 以及 `api-version` 参数。 本文使用的是 `api-version=2017-12-01`。

以下标头是必需的：

| 请求标头   | 说明 |
|------------------|-----------------|
| Content-Type：  | 必需。 设置为 `application/json`。 |
| Authorization： | 必需。 设置为有效的 `Bearer` [访问令牌](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)。 |

有关如何创建请求的详细信息，请参阅 [REST API 请求/响应的组件](/rest/api/azure/#components-of-a-rest-api-requestresponse)。

## <a name="create-the-request-body"></a>创建请求正文

下面的通用定义用来构建请求正文：

| 名称                       | 必选 | Type                                                                                | 说明  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | 字符串                                                                              | 资源位置。 |
| 名称                       |          | 字符串                                                                              | 虚拟机的名称。 |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | 指定虚拟机的硬件设置。 |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | 指定虚拟机磁盘的存储设置。 |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | 指定虚拟机的操作系统设置。 |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | 指定虚拟机的网络接口。 |

有关请求正文中的变量定义的完整列表，请参阅[虚拟机创建或更新请求正文定义](/rest/api/compute/virtualmachines/createorupdate#definitions)。

### <a name="example-request-body"></a>示例请求正文

以下示例请求正文定义了一个使用高级托管磁盘的 Ubuntu 18.04-LTS 映像。 使用了 SSH 公钥身份验证，并且 VM 使用你[以前创建的](../../virtual-network/virtual-network-network-interface.md)现有虚拟网络接口卡 (NIC)。 在 *osProfile.linuxConfiguration.ssh.publicKeys.keyData* 字段中提供你的 SSH 公钥。 如果需要，你可以[生成 SSH 密钥对](mac-create-ssh-keys.md)。

```json
{
  "location": "eastus",
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
      "computerName": "myVM",
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
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  },
  "name": "myVM"
}
```

## <a name="responses"></a>响应

对于要创建或更新虚拟机的操作，有两个成功响应：

| 名称        | Type                                                                              | 说明 |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 正常      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Created | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | 已创建     |

有关 REST API 响应的详细信息，请参阅[处理响应消息](/rest/api/azure/#process-the-response-message)。

### <a name="example-response"></a>示例响应

下面是一个精简后的 *201 Created* 响应，它来自前面创建 VM 的示例请求正文，该响应表明已分配了 *vmId* 并且 *provisioningState* 为 *Creating*：

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>后续步骤

有关 Azure REST API 或其他管理工具（例如 Azure CLI 2.0 或 Azure PowerShell）的详细信息，请参阅以下资源：

- [Azure 计算提供程序 REST API](/rest/api/compute/)
- [Azure REST API 入门](/rest/api/azure/)
- [Azure CLI 2.0](/cli/azure/)
- [Azure PowerShell 模块](/powershell/azure/overview)
