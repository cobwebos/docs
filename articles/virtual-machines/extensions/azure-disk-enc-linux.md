---
title: 适用于 Linux 的 Azure 磁盘加密 | Microsoft Docs
description: 使用虚拟机扩展将适用于 Linux 的 Azure 磁盘加密部署到虚拟机。
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 1801ca8bd59153de81ef680253da229e2bd4a338
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597870"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>适用于 Linux 的 Azure 磁盘加密 (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>概述

Azure 磁盘加密利用 Linux 中的 dm-crypt 子系统在[选择 Azure Linux 发行版](https://aka.ms/adelinux)上提供完整磁盘加密。  此解决方案与 Azure Key Vault 集成，用于管理磁盘加密密钥和机密。

## <a name="prerequisites"></a>必备组件

有关先决条件的完整列表，请参阅[适用于 Linux vm 的 Azure 磁盘加密](../linux/disk-encryption-overview.md)，具体如下部分：

- [适用于 Linux Vm 的 Azure 磁盘加密](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [其他 VM 要求](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [网络要求](../linux/disk-encryption-overview.md#networking-requirements)

## <a name="extension-schemata"></a>扩展架构

Azure 磁盘加密有两个架构：1.1 版，一种较新的建议架构，不使用 Azure Active Directory （AAD）属性和 v 0.1，后者要求使用 AAD 属性。 必须使用对应于所使用的扩展的架构版本：架构 v1.1 （适用于 AzureDiskEncryptionForLinux 扩展版本1.1），架构 v 0.1 （对于 AzureDiskEncryptionForLinux 扩展版本0.1）。
### <a name="schema-v11-no-aad-recommended"></a>架构1.1 版：不使用 AAD （建议）

V1.1 架构是推荐的，不需要 Azure Active Directory 属性。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>架构 v 0.1：与 AAD 

0\.1 架构需要 `aadClientID` 和 `aadClientSecret` 或 `AADClientCertificate`。

使用 `aadClientSecret`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

使用 `AADClientCertificate`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| 发布者 | Microsoft.Azure.Security | 字符串 |
| type | AzureDiskEncryptionForLinux | 字符串 |
| typeHandlerVersion | 0.1、1。1 | int |
| （0.1 架构）AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| （0.1 架构）AADClientSecret | password | 字符串 |
| （0.1 架构）AADClientCertificate | thumbprint | 字符串 |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 字典 |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | 字符串 | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | 字符串 |
| KeyEncryptionKeyURL | url | 字符串 |
| 可有可无KeyVaultURL | url | 字符串 |
| 通行短语 | password | 字符串 | 
| SequenceVersion | uniqueidentifier | 字符串 |
| VolumeType | OS, Data, All | 字符串 |

## <a name="template-deployment"></a>模板部署

有关模板部署的示例，请参阅[在正在运行的 Linux VM 上启用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)。

## <a name="azure-cli-deployment"></a>Azure CLI 部署

可以在最新 [Azure CLI 文档](/cli/azure/vm/encryption?view=azure-cli-latest)中找到相关说明。 

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关故障排除，请参阅 [Azure 磁盘加密故障排除指南](../../security/azure-security-disk-encryption-tsg.md)。

### <a name="support"></a>支持

如果你对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/community/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤

有关 VM 扩展的详细信息，请参阅[适用于 Linux 的虚拟机扩展和功能](features-linux.md)。