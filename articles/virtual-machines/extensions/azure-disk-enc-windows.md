---
title: 适用于 Windows 的 Azure 磁盘加密
description: 使用虚拟机扩展将 Azure 磁盘加密部署到 Windows 虚拟机。
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 8435663dcf92e2617ea2fe9218649e94243272d2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383342"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>适用于 Windows 的 Azure 磁盘加密 (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>概述

Azure 磁盘加密利用 BitLocker 在运行 Windows 的 Azure 虚拟机上提供完全磁盘加密。  此解决方案与 Azure Key Vault 集成，以管理 Key Vault 订阅中的磁盘加密密钥和机密。 

## <a name="prerequisites"></a>必备条件

有关先决条件的完整列表，请参阅[适用于 Linux vm 的 Azure 磁盘加密](../linux/disk-encryption-overview.md)，具体如下部分：

- [适用于 Linux Vm 的 Azure 磁盘加密](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [网络要求](../windows/disk-encryption-overview.md#networking-requirements)
- [组策略要求](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>扩展架构

Windows AzureDiskEncryption 扩展有两个架构：版本2.2，一种更新的建议架构，不使用 Azure Active Directory （AAD）属性和1.1 版，后者要求使用 AAD 属性。 必须使用对应于所使用的扩展的架构版本：架构 v2.0 AzureDiskEncryption 扩展版本2.2，架构1.1 版用于 AzureDiskEncryption 扩展版本1.1。

### <a name="schema-v22-no-aad-recommended"></a>架构版本2.2：不使用 AAD （建议）

对于所有新 Vm，建议使用 v1.0 架构，不需要 Azure Active Directory 属性。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v11-with-aad"></a>架构 v1.1：与 AAD 

1\.1 架构需要 `aadClientID`，且 `aadClientSecret` 或 `AADClientCertificate`，不建议用于新的虚拟机。

使用 `aadClientSecret`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
| typeHandlerVersion | 1.1、2。2 | 字符串 |
| （1.1 架构）AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| （1.1 架构）AADClientSecret | password | 字符串 |
| （1.1 架构）AADClientCertificate | thumbprint | 字符串 |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 字典 |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | 字符串 | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | 字符串 |
| KeyEncryptionKeyURL | url | 字符串 |
| KeyVaultURL | url | 字符串 |
| 可有可无密码 | password | 字符串 | 
| SequenceVersion | uniqueidentifier | 字符串 |
| VolumeType | OS, Data, All | 字符串 |

## <a name="template-deployment"></a>模板部署
有关模板部署的示例，请参阅[从库映像创建新的加密 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)。

## <a name="azure-cli-deployment"></a>Azure CLI 部署

可以在最新 [Azure CLI 文档](/cli/azure/vm/encryption?view=azure-cli-latest)中找到相关说明。 

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

请参阅 [Azure 磁盘加密故障排除指南](../../security/azure-security-disk-encryption-tsg.md)。

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/community/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤
有关扩展的详细信息，请参阅[适用于 Windows 的虚拟机扩展和功能](features-windows.md)。
