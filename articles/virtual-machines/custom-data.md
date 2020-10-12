---
title: 自定义数据和 Azure 虚拟机
description: 详细介绍如何使用 Azure 虚拟机上的自定义数据和 Cloud-Init
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 2924caaac5fb8c512100d9e897f7f153af9a3b3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284908"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Azure 虚拟机上的自定义数据和 Cloud-Init

在设置时，可能需要将一个脚本或其他元数据注入到 Microsoft Azure 虚拟机。  在其他云中，此概念通常称为用户数据。  在 Microsoft Azure 中，我们有一个称为自定义数据的类似功能。 

自定义数据仅在首次启动/初始设置（称为“预配”）期间提供给 VM。 预配是指向 VM 提供“VM 创建”参数（例如主机名、用户名、密码、证书、自定义数据、密钥等）的过程，某个预配代理（例如 [Linux 代理](./extensions/agent-linux.md)和 [cloud-init](./linux/using-cloud-init.md#troubleshooting-cloud-init)）会处理这些参数。 


## <a name="passing-custom-data-to-the-vm"></a>将自定义数据传递给 VM
若要使用自定义数据，除非使用 AZ CLI 等 CLI 工具执行转换，否则在将内容传递给 API 之前，必须先对其进行 base64 编码。 大小不能超过 64 KB。

在 CLI 中，可将自定义数据作为文件传递，它将转换为 base64。
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

Azure 资源管理器 (ARM) 中有一个 [base64 函数](../azure-resource-manager/templates/template-functions-string.md#base64)。

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>处理自定义数据
VM 上安装的预配代理负责处理与平台的对接并将其放到文件系统的过程。 

### <a name="windows"></a>Windows
系统会将自定义数据以二进制文件的形式放在 *%SYSTEMDRIVE%\AzureData\CustomData.bin* 中，但不会对其进行处理。 如果你希望处理此文件，需要生成自定义映像，并编写代码来处理 CustomData.bin。

### <a name="linux"></a>Linux  
在 Linux OS 上，自定义数据将通过 ovf-env.xml 文件传递给 VM。在预配期间，该文件会复制到 */var/lib/waagent* 目录。  为了方便操作，较新版本的 Microsoft Azure Linux 代理还会将 base64 编码的数据复制到 */var/lib/waagent/CustomData*。

Azure 目前支持两个预配代理：
* Linux 代理 - 默认情况下，该代理不会处理自定义数据，你需要在启用自定义数据的情况下生成自定义映像。 [文档](https://github.com/Azure/WALinuxAgent#configuration)中介绍的相关设置为：
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

启用自定义数据并执行某个脚本时，在该脚本完成之前，会延迟 VM 的报告（指出 VM 是否已准备就绪，或预配是否成功）。 如果该脚本超过了允许的 VM 总计预配时间（40 分钟），则 VM 创建操作会失败。 请注意，如果脚本无法执行或者在执行期间出错，系统不会将此问题视为严重的预配失败，而你需要创建一个通知路径来提醒自己有关脚本的完成状态。

若要排查自定义数据执行问题，请查看 */var/log/waagent.log*

* 云初始化 - 默认情况下会处理自定义数据，云初始化接受[多种格式](https://cloudinit.readthedocs.io/en/latest/topics/format.html)的自定义数据，如云初始化配置、脚本等。当云初始化处理自定义数据时，类似于 Linux 代理。 如果在执行配置处理或脚本的过程中出现错误，则不会将其视为致命的预配失败，需要创建通知路径以提醒你脚本的完成状态。 但是，与 Linux 代理不同，cloud-init 不会等待用户自定义数据配置完成后再向平台报告 VM 已准备就绪。 有关 Azure 上的 cloud-init 的详细信息，请查看[文档](./linux/using-cloud-init.md)。


若要排查自定义数据执行问题，请查看故障排除[文档](./linux/using-cloud-init.md#troubleshooting-cloud-init)。


## <a name="faq"></a>常见问题
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>是否可以在创建 VM 后更新自定义数据？
对于单个 VM，无法更新 VM 模型中的自定义数据，但对于 VMSS，你可以通过 [REST API](/rest/api/compute/virtualmachinescalesets/update) 更新 VMSS 自定义数据（不适用于 PS 或 AZ CLI 客户端）。 在 VMSS 模型中更新自定义数据时：
* VMSS 中的现有实例只有在重置映像之后，才会获得更新的自定义数据。
* VMSS 中已升级的现有实例不会获得更新的自定义数据。
* 新实例会接收新的自定义数据。

### <a name="can-i-place-sensitive-values-in-custom-data"></a>是否可将敏感值放入自定义数据中？
建议**不要**将敏感数据存储在自定义数据中。 有关详细信息，请参阅 [Azure 安全和加密最佳做法](../security/fundamentals/data-encryption-best-practices.md)。


### <a name="is-custom-data-made-available-in-imds"></a>自定义数据在 IMDS 中是否可用？
目前不提供此功能。
