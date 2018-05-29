---
title: Azure 备份的 VM 快照 Linux 扩展 | Microsoft 文档
description: 使用 VM 快照扩展从 Azure 备份获取虚拟机的应用程序一致性备份
services: backup, virtual-machines-linux
documentationcenter: ''
author: trinadhk
manager: jeconnoc
editor: ''
ms.assetid: 57759670-0baa-44db-ae14-8cdc00d3a906
ms.service: backup, virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/26/2018
ms.author: trinadhk
ms.openlocfilehash: bed5716b6d4ea6d81214a95d0f2360f359048893
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944789"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Azure 备份的 VM 快照 Linux 扩展

## <a name="overview"></a>概述

Azure 备份支持从本地将工作负载备份到云以及将云资源备份到恢复服务保管库。 Azure 备份使用 VM 快照扩展即可获取 Azure 虚拟机的应用程序一致性备份，而无需关闭 VM。 Microsoft 将 VM 快照 Linux 扩展作为 Azure 备份服务的一部分发布并提供支持。 Azure 备份将安装该扩展，使其作为启用备份后触发的首个计划备份的一部分。 本文档详细介绍适用于 VM 快照扩展的受支持平台、配置和部署选项。

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统
有关支持的操作系统的列表，请参阅 [Azure 备份支持的操作系统](../../backup/backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup)

### <a name="internet-connectivity"></a>Internet 连接

VM 快照扩展要求在我们获取虚拟机的备份时，目标虚拟机已连接到 Internet。

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示 VM 快照扩展的架构。 该扩展需要任务 ID（它标识了在 VM 上已触发快照的备份作业）、状态 Blob URI（其中写入快照操作状态）、快照的计划开始时间、日志 Blob URI（写入与快照任务对应的日志），以及表示 VM 磁盘和元数据的 objstr。  由于应将这些设置视为敏感数据，因此它应存储在受保护的设置配置中。 Azure VM 扩展保护的设置数据已加密，并且只能在目标虚拟机上解密。 请注意，建议仅将这些设置作为备份作业的一部分从 Azure 备份服务传递。

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri represenattion of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | 日期 |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | 字符串 |
| commandStartTimeUTCTicks | 6.36458E+17 | 字符串 |
| 区域设置 | zh-cn | 字符串 |
| objectStr | sas uri 数组的编码 - "blobSASUri": ["https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig=5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm%2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | 字符串 |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | 字符串 |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | 字符串 |



## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 但是，建议通过在虚拟机上启用备份，将 VM 快照扩展添加到虚拟机。 可以通过资源管理器模板来实现此操作。  在虚拟机上启用备份的示例资源管理器模板可以在 [Azure 快速入门库](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/)中找到。


## <a name="azure-cli-deployment"></a>Azure CLI 部署

可以使用 Azure CLI 在虚拟机上启用备份。 启用备份之后，第一个计划的备份作业将在 VM 上安装 VM 快照扩展。

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure CLI 运行以下命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

扩展执行输出将记录到以下文件：

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>错误代码及其含义

故障排除信息可在 [Azure VM 备份故障排除指南](../../backup/backup-azure-vms-troubleshoot.md)中找到。

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
