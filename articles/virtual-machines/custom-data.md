---
title: 自定义数据和 Azure 虚拟机
description: 有关在 Azure 虚拟机上使用自定义数据和云 Init 的详细信息
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: aadac082e90a19d1a185dd7e6181a490adb70a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109623"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Azure 虚拟机上的自定义数据和云 Init

## <a name="what-is-custom-data"></a>什么是自定义数据？

客户经常询问如何在预配时将脚本或其他元数据注入 Microsoft Azure 虚拟机。  在其他云中，这个概念通常称为用户数据。  在 Microsoft Azure 中，我们有一个类似的功能称为自定义数据。 

自定义数据仅在首次启动/初始设置期间提供给 VM，我们称之为"预配"。 预配是 VM 提供 VM 的 VM 创建参数（例如，主机名、用户名、密码、证书、自定义数据、密钥等）的过程，预配代理处理它们，如[Linux 代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)和[云 init。](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init) 


## <a name="passing-custom-data-to-the-vm"></a>将自定义数据传递到 VM
要使用自定义数据，必须先对内容进行基础编码，然后再将其传递给 API，除非您使用的是为您进行转换的 CLI 工具，例如 AZ CLI。 大小不能超过 64 KB。

在 CLI 中，您可以将自定义数据作为文件传递，并将它转换为 base64。
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

在 Azure 资源管理器 （ARM） 中，有一个[base64 函数](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64)。

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
        "customDataBase64": "[variables('customData')]"
        },
```

## <a name="processing-custom-data"></a>处理自定义数据
安装在 VM 上的预配代理处理与平台的接口并将其放置在文件系统上。 

### <a name="windows"></a>Windows
自定义数据作为二进制文件放置在 *%SYSTEMDRIVE%\AzureData_CustomData.bin*中，但不会处理。 如果要处理此文件，则需要构建自定义映像，并编写代码来处理 CustomData.bin。

### <a name="linux"></a>Linux  
在 Linux 操作系统上，自定义数据通过 ovf-env.xml 文件传递到 VM，该文件在预配期间复制到 */var/lib/waagent*目录。  较新版本的 Microsoft Azure Linux 代理也将将基本编码的数据复制到 */var/lib/waagent/自定义数据*，以方便。

Azure 当前支持两个预配代理：
* Linux 代理 - 默认情况下，代理不会处理自定义数据，您需要在启用自定义映像后构建自定义映像。 根据[文档](https://github.com/Azure/WALinuxAgent#configuration)，相关设置包括：
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

启用自定义数据并执行脚本时，它将延迟 VM 报告，该报告已准备就绪，或者预配成功，直到脚本完成。 如果脚本超过 40 分钟的总 VM 预配时间限制，VM Create 将失败。 请注意，如果脚本在执行失败或执行过程中出现错误，则不被视为致命预配失败，则需要创建通知路径来提醒您脚本的完成状态。

要排除自定义数据执行的故障，请查看 */var/log/waagent.log*

* 云 - 默认情况下，云 init 默认处理自定义数据，云 init 接受[多种格式](https://cloudinit.readthedocs.io/en/latest/topics/format.html)的自定义数据，如云 init 配置、脚本等。与 Linux 代理类似，当云 init 处理自定义数据时。 如果在执行配置处理或脚本期间出现错误，则不被视为致命预配失败，您需要创建通知路径来提醒您脚本的完成状态。 但是，与 Linux 代理不同，云 init 不会等待用户自定义数据配置完成，然后再向 VM 已准备好的平台报告。 有关 azure 上的云 init 的详细信息，请查看[文档](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)。


要对自定义数据执行进行故障排除，请查看故障排除[文档](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)。


## <a name="faq"></a>FAQ
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>创建 VM 后，是否可以更新自定义数据？
对于单个 VM，无法更新 VM 模型中的自定义数据，但对于 VMSS，您可以通过 REST API 更新 VMSS 自定义数据（不适用于 PS 或 AZ CLI 客户端）。 在 VMSS 模型中更新自定义数据时：
* VMSS 中的现有实例只有在重新映像之前才能获取更新的自定义数据。
* 升级的 VMSS 中的现有实例将得不到更新的自定义数据。
* 新实例将接收新的自定义数据。

### <a name="can-i-place-sensitive-values-in-custom-data"></a>是否可以在自定义数据中放置敏感值？
我们建议**不要**将敏感数据存储在自定义数据中。 有关详细信息，请参阅[Azure 安全和加密最佳做法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices)。


### <a name="is-custom-data-made-available-in-imds"></a>自定义数据是否在 IMDS 中提供？
否，此功能当前不可用。
