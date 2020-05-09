---
title: 在 Azure 中的 Linux Vm 上运行自定义脚本
description: 使用自定义脚本扩展 v1 自动化 Linux VM 配置任务
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: 1ca20f2c8cda84c241391f67ac542faa4a1f5ecd
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594706"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>在 Linux 虚拟机上使用 Azure 自定义脚本扩展版本 1

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

自定义脚本扩展版本 1 在 Azure 虚拟机上下载和运行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或其他可访问的 Internet 位置下载脚本，或者将脚本提供给扩展运行时。

将自定义脚本扩展与 Azure 资源管理器模板集成。 也可使用 Azure CLI、PowerShell、Azure 门户或 Azure 虚拟机 REST API 来运行它。

本文详细介绍如何使用 Azure CLI 中的自定义脚本扩展以及如何使用 Azure 资源管理器模板运行扩展。 本文还提供针对 Linux 系统的疑难解答步骤。

存在两个 Linux 自定义脚本扩展：

* 版本 1 - Microsoft.OSTCExtensions.CustomScriptForLinux

* 版本 2 - Microsoft.Azure.Extensions.CustomScript

请切换新部署和现有部署，改用新版本 ([Microsoft.Azure.Extensions.CustomScript](custom-script-linux.md))。 新版本可作为一个简易的替代版本。 因此，迁移时只需更改名称和版本，无需更改扩展配置。

### <a name="operating-system"></a>操作系统

支持的 Linux 分发：

* CentOS 6.5 和更高版本
* Debian 8 和更高版本
  * Debian 8.7 未在最新映像中随附 Python2，这将中断 CustomScriptForLinux。
* FreeBSD
* OpenSUSE 13.1 和更高版本
* Oracle Linux 6.4 和更高版本
* SUSE Linux Enterprise Server 11 SP3 和更高版本
* Ubuntu 12.04 和更高版本

### <a name="script-location"></a>脚本位置

可使用扩展，利用 Azure Blob 存储凭据来访问 Azure Blob 存储。 或者，脚本位置可以是任何位置，只要 VM 可以路由到该终结点（如 GitHub、内部文件服务器等）即可。

### <a name="internet-connectivity"></a>Internet 连接

如果需要从外部（例如 GitHub 或 Azure 存储）下载脚本，则需要打开其他防火墙/网络安全组端口。 例如，如果脚本位于 Azure 存储中，可以使用[存储](../../virtual-network/security-overview.md#service-tags)的 Azure NSG 服务标记来允许访问。

如果脚本位于本地服务器上，则可能仍需要打开其他防火墙/网络安全组端口。

### <a name="tips-and-tricks"></a>提示和技巧

* 脚本中的语法错误会导致此扩展失败率最高，应测试脚本运行正确无误，同时在脚本中添加其他日志记录，以便更轻松地找到失败位置。
* 编写幂等性的脚本，这样一来，如果脚本意外多次运行，将不会导致系统更改。
* 确保这些脚本在运行时不需要用户输入。
* 脚本可以运行 90 分钟，若运行时间超过 90 分钟，将导致扩展的预配失败。
* 请勿将 reboot 置于脚本中，这会导致正在安装的其他扩展出现问题，并且在重启后，该扩展将不会继续。 
* 如果你的脚本将导致重新启动，则安装应用程序并运行脚本等。你应使用 Cron 作业或使用 DSC、Chef、Puppet 扩展等工具来计划重启。
* 该扩展只会运行一个脚本一次，如果想要在每次启动时运行一个脚本，则可以使用 [cloud-init 映像](../linux/using-cloud-init.md)和 [Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) 模块。 或者，可以使用脚本创建 Systemd 服务单元。
* 如果想要计划脚本何时运行，应使用扩展创建一个 Cron 作业。
* 脚本运行时，Azure 门户或 CLI 中只会显示“正在转换”扩展状态。 如果希望更频繁地更新正在运行的脚本的状态，需要创建自己的解决方案。
* 自定义脚本扩展不能以本机方式支持代理服务器，但您可以使用支持您的脚本中的代理服务器的文件传输工具（如 "*卷*"）。
* 请注意脚本或命令可能依赖的非默认目录位置，按逻辑对其进行处理。

## <a name="extension-schema"></a>扩展架构

自定义脚本扩展配置指定脚本位置和要运行命令等设置。 可将此配置存储在配置文件中、在命令行中指定，或者在 Azure 资源管理器模板中指定该配置。 

可将敏感数据存储在受保护的配置中，此配置经过加密，只能在虚拟机内部解密。 当执行命令包含机密（例如密码）时，受保护的配置相当有用。

这些项应视为敏感数据，并且应在扩展保护的设置配置中指定。 Azure VM 扩展的受保护设置数据已加密，并且只能在目标虚拟机上解密。

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.OSTCExtensions | string |
| type | CustomScriptForLinux | string |
| typeHandlerVersion | 1.5 | int |
| fileUris（例如） | `https://github.com/MyProject/Archive/MyPythonScript.py` | array |
| commandToExecute（例如） | python MyPythonScript.py \<my-param1\> | string |
| enableInternalDNSCheck | 是 | boolean |
| storageAccountName（例如） | examplestorageacct | string |
| storageAccountKey（例如） | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>属性值详细信息

* `fileUris`：（可选，字符串数组）脚本的 URI 列表
* `enableInternalDNSCheck`：（可选，布尔型）默认为 True，设置为 False 可禁用 DNS 检查。
* `commandToExecute`：（可选，字符串）要执行的入口点脚本
* `storageAccountName`：（可选，字符串）存储帐户的名称
* `storageAccountKey`：（可选，字符串）存储帐户的访问密钥

可以在公共设置或受保护设置中设置以下值，但不得同时在公共设置和受保护设置中设置以下值。

* `commandToExecute`

虽然使用公共设置可能对调试很有用，但强烈建议使用受保护设置。

公共设置会以明文形式发送到将执行脚本的 VM。  受保护设置使用只有 Azure 和 VM 知道的密钥进行加密。 这些设置会在发送时保存到 VM 中，也就是说，如果设置已加密，则会在 VM 上加密保存。 用于对已加密值解密的证书存储在 VM 上，该证书用于在运行时对设置解密（如必要）。

## <a name="template-deployment"></a>模板部署

可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure 资源管理器模板部署过程中运行自定义脚本扩展。

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>这些属性名称区分大小写。 要避免部署问题，请使用如下所示的名称。

## <a name="azure-cli"></a>Azure CLI

在使用 Azure CLI 运行自定义脚本扩展时，请创建一个或多个配置文件。 至少必须具有“commandToExecute”。

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

（可选）可以在命令中以 JSON 格式字符串的形式指定设置。 这样，便可以在执行期间指定配置，而无需使用单独的配置文件。

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI 示例

#### <a name="public-configuration-with-no-script-file"></a>不包含脚本文件的公共配置

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI 命令：

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>公共和受保护的配置文件

使用公共配置文件来指定脚本文件 URI。 使用受保护的配置文件来指定要运行的命令。

公共配置文件：

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

受保护的配置文件：  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLI 命令：

```azurecli
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>疑难解答

运行自定义脚本扩展时，会创建脚本，或将脚本下载到类似于以下示例的目录中。 命令输出也会保存到此目录中的 `stdout` 和 `stderr` 文件中。

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

要排除故障，请首先查看 Linux 代理日志，确保扩展运行，并检查：

```bash
/var/log/waagent.log
```

应该查找如下所示的扩展执行：

```output
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```

一些需要注意的要点：

1. Enable 表示该命令何时开始运行。
1. Download 涉及下载 Azure 中的 CustomScript 扩展包，而非 fileUris 中指定的脚本文件。
1. 还可查看它写出到 `/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log` 的日志文件

下一步是查看以下格式的日志文件：

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

应该查找如下所示的个别执行：

```output
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
```

可在此处看到：

* 此日志表示 Enable 命令开始执行
* 设置已传递到扩展
* 扩展下载文件及其结果。
* 正在运行的命令及结果。

也可使用 Azure CLI 来检索自定义脚本扩展的执行状态：

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

输出类似于以下文本：

```output
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>后续步骤

若要查看代码、当前问题和版本，请参阅 [CustomScript 扩展存储库](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)。
