---
title: 自定义数据和 Azure 虚拟机
description: 有关在 Azure 虚拟机上使用自定义数据和云初始化的详细信息
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 9497e665d024b583c261ade3e6fb5393a9322ce0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759139"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Azure 虚拟机上的自定义数据和云初始化

## <a name="what-is-custom-data"></a>什么是自定义数据？

客户经常会询问如何在预配时将脚本或其他元数据注入到 Microsoft Azure 的虚拟机。  在其他云中，此概念通常称为用户数据。  在 Microsoft Azure 中，我们有一个称为自定义数据的类似功能。 

自定义数据仅在第一次启动/初始设置期间可用于 VM，我们称之为 "预配"。 预配是 vm 创建参数（例如，主机名、用户名、密码、证书、自定义数据、密钥等）可用于 VM 的过程，预配代理处理这些参数，如[Linux 代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)和[云初始化](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)。 


## <a name="passing-custom-data-to-the-vm"></a>将自定义数据传递到 VM
若要使用自定义数据，必须在将内容传递到 API 之前对其进行 base64 编码，除非使用的是为你执行转换的 CLI 工具，如 AZ CLI。 大小不能超过 64 KB。

在 CLI 中，可以将自定义数据作为文件传递，并将其转换为 base64。
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

在 Azure 资源管理器（ARM）中，有一个[base64 函数](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64)。

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
Vm 上安装的预配代理处理与平台的交互，并将其放在文件系统上。 

### <a name="windows"></a>Windows
自定义数据作为二进制文件放置在 *%SYSTEMDRIVE%\AzureData\CustomData.bin*中，但不会进行处理。 如果希望处理此文件，则需要生成自定义映像，并编写代码来处理 CustomData。

### <a name="linux"></a>Linux  
在 Linux 操作系统上，自定义数据通过 ovf-env 文件传递给 VM，该文件在预配过程中将复制到 */var/lib/waagent*目录。  较新版本的 Microsoft Azure Linux 代理还会将 base64 编码的数据复制到 */var/lib/waagent/CustomData* ，以便于方便。

Azure 当前支持两个设置代理：
* Linux 代理-默认情况下，代理将不会处理自定义数据，你将需要生成启用了它的自定义映像。 相关设置，如[文档](https://github.com/Azure/WALinuxAgent#configuration)所示：
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

启用自定义数据并执行脚本时，它会将已准备就绪的 VM 报告延迟，或在脚本完成之前，预配已成功。 如果脚本的总 VM 预配时间超过40分钟，则 VM 创建将失败。 请注意，如果脚本无法执行，或者执行过程中出现错误，则不会将其视为致命的预配失败，你将需要创建通知路径以提醒你脚本的完成状态。

若要解决自定义数据执行问题，请查看 */var/log/waagent.log*

* 默认情况下，云初始化默认情况下会处理自定义数据，cloud init 接受多种自定义数据[格式](https://cloudinit.readthedocs.io/en/latest/topics/format.html)，如云初始化配置、脚本等。当云初始化处理自定义数据时，类似于 Linux 代理。 如果在执行配置处理或脚本的过程中出现错误，则不会将其视为致命的预配失败，你将需要创建通知路径以提醒你脚本的完成状态。 但是，与 Linux 代理不同，云初始化不会等待用户自定义数据配置完成，然后再向平台报告 VM 已准备就绪。 有关 azure 上的云初始化的详细信息，请参阅[文档](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)。


若要解决自定义数据执行问题，请查看故障排除[文档](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)。


## <a name="faq"></a>常见问题解答
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>是否可以在创建 VM 后更新自定义数据？
对于单个 Vm，无法更新 VM 模型中的自定义数据，但对于 VMSS，你可以通过 REST API （不适用于 PS 或 AZ CLI 客户端）更新 VMSS 自定义数据。 在 VMSS 模型中更新自定义数据时：
* VMSS 中的现有实例将不会获取已更新的自定义数据，只是在重置映像之前。
* 升级的 VMSS 中的现有实例将不会获取更新的自定义数据。
* 新实例将接收新的自定义数据。

### <a name="can-i-place-sensitive-values-in-custom-data"></a>是否可以将敏感值放入自定义数据？
建议**不要**将敏感数据存储在自定义数据中。 有关详细信息，请参阅[Azure 安全和加密最佳实践](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices)。


### <a name="is-custom-data-made-available-in-imds"></a>自定义数据在 IMDS 中是否可用？
不可以，此功能当前不可用。
