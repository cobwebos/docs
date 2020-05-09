---
title: 在 Azure 中的 Linux VM 上运行自定义脚本
description: 使用自定义脚本扩展 v2 自动化 Linux VM 配置任务
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: 92bb254873669ae7c0894d633f17b5701b7ddc97
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594723"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>在 Linux 虚拟机上使用 Azure 自定义脚本扩展版本 2
自定义脚本扩展版本 2 在 Azure 虚拟机上下载和运行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 可以从 Azure 存储或其他可访问的 Internet 位置下载脚本，或者将脚本提供给扩展运行时。 

将自定义脚本扩展与 Azure 资源管理器模板集成。 也可使用 Azure CLI、PowerShell 或 Azure 虚拟机 REST API 来运行它。

本文详细介绍如何使用 Azure CLI 中的自定义脚本扩展以及如何使用 Azure 资源管理器模板运行扩展。 本文还提供针对 Linux 系统的疑难解答步骤。


存在两个 Linux 自定义脚本扩展：
* 版本 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* 版本 2 - Microsoft.Azure.Extensions.CustomScript

请切换新部署和现有部署，改用新的版本 2。 新版本可作为一个简易的替代版本。 因此，迁移时只需更改名称和版本，无需更改扩展配置。


### <a name="operating-system"></a>操作系统

适用于 Linux 的自定义脚本扩展将在扩展支持的扩展 OS 上运行，有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。

### <a name="script-location"></a>脚本位置

可使用扩展，利用 Azure Blob 存储凭据来访问 Azure Blob 存储。 或者，脚本位置可以是任何位置，只要 VM 可以路由到该终结点（如 GitHub、内部文件服务器等）即可。

### <a name="internet-connectivity"></a>Internet 连接
如果需要从外部（例如 GitHub 或 Azure 存储）下载脚本，则需要打开其他防火墙/网络安全组端口。 例如，如果脚本位于 Azure 存储中，可以使用[存储](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)的 Azure NSG 服务标记来允许访问。

如果脚本位于本地服务器上，则可能仍需要打开其他防火墙/网络安全组端口。

### <a name="tips-and-tricks"></a>提示和技巧
* 脚本中的语法错误会导致此扩展失败率最高，应测试脚本运行正确无误，同时在脚本中添加其他日志记录，以便更轻松地找到失败位置。
* 编写幂等性的脚本，这样一来，如果脚本意外多次运行，将不会导致系统更改。
* 确保这些脚本在运行时不需要用户输入。
* 脚本可以运行 90 分钟，若运行时间超过 90 分钟，将导致扩展的预配失败。
* 请勿将 reboot 置于脚本中，这会导致正在安装的其他扩展出现问题，并且在重启后，该扩展将不会继续。 
* 如果脚本会导致重启，则安装应用程序并运行脚本等。应该使用 Cron 作业或者使用 DSC 或 Chef、Puppet 扩展等工具来计划重启。
* 该扩展只会运行一个脚本一次，如果想要在每次启动时运行一个脚本，则可以使用 [cloud-init 映像](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)和 [Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) 模块。 或者，可以使用脚本创建 SystemD 服务单元。
* 如果想要计划脚本何时运行，应使用扩展创建一个 Cron 作业。 
* 脚本运行时，Azure 门户或 CLI 中只会显示“正在转换”扩展状态。 如果希望更频繁地更新正在运行的脚本的状态，需要创建自己的解决方案。
* 自定义脚本扩展本身不支持代理服务器，但可以使用脚本中支持代理服务器的文件传输工具，如 Curl  。 
* 请注意脚本或命令可能依赖的非默认目录位置，按逻辑对其进行处理。
*  将自定义脚本部署到生产 VMSS 实例时，建议通过 json 模板进行部署，并将脚本存储帐户存储在你可以控制 SAS 令牌的位置。 


## <a name="extension-schema"></a>扩展架构

自定义脚本扩展配置指定脚本位置和要运行命令等设置。 可将此配置存储在配置文件中、在命令行中指定，或者在 Azure 资源管理器模板中指定该配置。 

可将敏感数据存储在受保护的配置中，此配置经过加密，只能在虚拟机内部解密。 当执行命令包含机密（例如密码）时，受保护的配置相当有用。

这些项目应视为敏感数据，并在扩展的受保护设置配置中指定。 Azure VM 扩展的受保护设置数据已加密，并且只能在目标虚拟机上解密。

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."],
       "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> managedIdentity 属性**不能**与 storageAccountName 或 storageAccountKey 属性结合使用

### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publisher | Microsoft.Compute.Extensions | string |
| type | CustomScript | string |
| typeHandlerVersion | 2.1 | int |
| fileUris（例如） | `https://github.com/MyProject/Archive/MyPythonScript.py` | array |
| commandToExecute（例如） | python MyPythonScript.py \<my-param1> | string |
| 脚本 | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix（示例） | false | boolean |
| timestamp（示例） | 123456789 | 32-bit integer |
| storageAccountName（例如） | examplestorageacct | string |
| storageAccountKey（例如） | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |
| managedIdentity（例如） | { } 或 { "clientId":"31b403aa-c364-4240-a7ff-d85fb6cd7232" } 或 { "objectId":"12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json 对象 |

### <a name="property-value-details"></a>属性值详细信息
* `apiVersion`：可以使用[资源浏览器](https://resources.azure.com/)或从 Azure CLI 使用以下命令找到最新的 apiVersion`az provider list -o json`
* `skipDos2Unix`：（可选，布尔值）跳过对基于脚本的文件 URL 或脚本进行的 dos2unix 转换。
* `timestamp`（可选，32 位整数）仅当需要更改此字段的值来触发脚本的重新运行时，才使用此字段。  任何整数值都是可以接受的，前提是必须不同于以前的值。
* `commandToExecute`：（在脚本未设置的情况下为**必需**，字符串）要执行的入口点脚本。 如果命令包含机密（例如密码），请改用此字段。
* `script`：（在 commandToExecute 未设置的情况下为**必需**，字符串）base64 编码（可以选择执行 gzip 操作）的脚本，通过 /bin/sh 来执行。
* `fileUris`：（可选，字符串数组）要下载的文件的 URL。
* `storageAccountName`：（可选，字符串）存储帐户的名称。 如果指定存储凭据，所有 `fileUris` 都必须是 Azure Blob 的 URL。
* `storageAccountKey`：（可选，字符串）存储帐户的访问密钥
* `managedIdentity`：（可选，json 对象）用于下载文件的[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
  * `clientId`：（可选，字符串）托管标识的客户端 ID
  * `objectId`：（可选，字符串）托管标识的对象 ID


可以在公共设置或受保护设置中设置以下值，但扩展会拒绝任何同时在公共设置和受保护设置中设置以下值的配置。
* `commandToExecute`
* `script`
* `fileUris`

虽然使用公共设置可能对调试很有用，但强烈建议使用受保护设置。

公共设置会以明文形式发送到将执行脚本的 VM。  受保护设置使用只有 Azure 和 VM 知道的密钥进行加密。 这些设置会在发送时保存到 VM 中，也就是说，如果设置已加密，则会在 VM 上加密保存。 用于对已加密值解密的证书存储在 VM 上，该证书用于在运行时对设置解密（如必要）。

#### <a name="property-skipdos2unix"></a>属性：skipDos2Unix

默认值为 false，这意味着执行 dos2unix 转换。 

旧版 CustomScript (Microsoft.OSTCExtensions.CustomScriptForLinux) 会将 `\r\n` 转换为 `\n`，从而将 DOS 文件自动转换为 UNIX 文件。 此转换仍然存在，并且默认为启用状态。 此转换适用于从 fileUris 下载的所有文件或基于任何下述标准的脚本设置。

* 如果扩展名为 `.sh`、`.txt`、`.py` 或 `.pl` 之一，则会进行转换。 脚本设置将始终符合此条件，因为已经假定该脚本是使用 /bin/sh 执行的脚本，并且会在 VM 上将它另存为 script.sh。
* 如果文件以 `#!` 开头。

将 skipDos2Unix 设置为 true 即可跳过 dos2unix 转换。

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>属性：脚本

CustomScript 支持执行用户定义的脚本。 可将 commandToExecute 和 fileUris 组合到单个设置中的脚本设置。 可以直接将脚本编码为设置，不必设置一个需要从 Azure 存储或 GitHub 主题下载的文件。 可以使用脚本来替换 commandToExecute 和 fileUris。

脚本**必须**进行 base64 编码。  可以**选择**对脚本执行 gzip 操作。 脚本设置可以用在公共设置或受保护的设置中。 脚本参数数据的最大大小为 256 KB。 如果脚本超出此大小，则不会执行该脚本。

以保存到 /script.sh/ 文件的以下脚本为例。

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

正确的 CustomScript 脚本设置可以使用以下命令的输出来构造。

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

可以选择对脚本执行 gzip 操作，以便进一步缩小其大小（适用于大多数情况）。 （CustomScript 自动检测是否使用了 gzip 压缩。）

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript 使用以下算法来执行脚本。

 1. 断言脚本值的长度不得超过 256 KB。
 1. base64 对脚本的值进行解码
 1.  尝试对 base64 解码的值执行 gunzip 操作
 1. 将解码（以及可以选择进行解压缩）的值写入磁盘 (/var/lib/waagent/custom-script/#/script.sh)
 1. 使用 _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh 执行脚本。

####  <a name="property-managedidentity"></a>属性：managedIdentity
> [!NOTE]
> 只能在受保护的设置中指定**此属性。**

CustomScript （版本2.1 以上版本）支持用于从 "fileUris" 设置中提供的 Url 下载文件的[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。 它允许 CustomScript 访问 Azure 存储专用 blob 或容器，而用户无需传递机密（如 SAS 令牌或存储帐户密钥）。

若要使用此功能，用户必须将[系统分配](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity)的或[用户分配](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity)的标识添加到要在其中运行 CUSTOMSCRIPT 的 VM 或 VMSS，并向[托管标识授予对 Azure 存储容器或 blob 的访问权限](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access)。

若要在目标 VM/VMSS 上使用系统分配的标识，请将 "对 microsoft.managedidentity" 字段设置为空的 json 对象。 

> 示例：
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

若要在目标 VM/VMSS 上使用用户分配的标识，请将 "对 microsoft.managedidentity" 字段配置为具有托管标识的客户端 ID 或对象 ID。

> 示例:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> 对 microsoft.managedidentity 属性**不**能与 StorageAccountName 或 storageAccountKey 属性一起使用

## <a name="template-deployment"></a>模板部署
可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在 Azure 资源管理器模板部署过程中运行自定义脚本扩展。 可在此处（[GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)）中找到包含自定义脚本扩展的示例模板。


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>这些属性名称区分大小写。 要避免部署问题，请使用如下所示的名称。

## <a name="azure-cli"></a>Azure CLI
在使用 Azure CLI 运行自定义脚本扩展时，请创建一个或多个配置文件。 至少必须具有“commandToExecute”。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

（可选）可以在命令中以 JSON 格式字符串的形式指定设置。 这样，便可以在执行期间指定配置，而无需使用单独的配置文件。

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI 示例

#### <a name="public-configuration-with-script-file"></a>包含脚本文件的公共配置

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLI 命令：

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>疑难解答
运行自定义脚本扩展时，会创建脚本，或将脚本下载到类似于以下示例的目录中。 命令输出也会保存到此目录中的 `stdout` 和 `stderr` 文件中。

```bash
/var/lib/waagent/custom-script/download/0/
```

要排除故障，请首先查看 Linux 代理日志，确保扩展运行，并检查：

```bash
/var/log/waagent.log 
```

应该查找如下所示的扩展执行：

```output
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```

一些需要注意的要点：
1. Enable 表示该命令何时开始运行。
2. Download 涉及下载 Azure 中的 CustomScript 扩展包，而非 fileUris 中指定的脚本文件。


Azure 脚本扩展生成一个日志，位置如下：

```bash
/var/log/azure/custom-script/handler.log
```

应该查找如下所示的个别执行：

```output
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```

可在此处看到：
* 此日志表示 Enable 命令开始执行
* 设置已传递到扩展
* 扩展下载文件及其结果。
* 正在运行的命令及结果。

你还可以使用 Azure CLI 检索自定义脚本扩展的执行状态，包括作为传递的`commandToExecute`实际参数：

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

输出类似于以下文本：

```output
[
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/customscript",
    "resourceGroup": "rgname",
    "settings": {
      "commandToExecute": "sh script.sh > ",
      "fileUris": [
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh",
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh"
      ]
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "2.0",
    "virtualMachineExtensionType": "CustomScript"
  },
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/OmsAgentForLinux",
    "instanceView": null,
    "location": "eastus",
    "name": "OmsAgentForLinux",
    "protectedSettings": null,
    "provisioningState": "Succeeded",
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "resourceGroup": "rgname",
    "settings": {
      "workspaceId": "workspaceid"
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "1.0",
    "virtualMachineExtensionType": "OmsAgentForLinux"
  }
]
```

## <a name="next-steps"></a>后续步骤
若要查看代码、当前问题和版本，请参阅 [custom-script-extension-linux 存储库](https://github.com/Azure/custom-script-extension-linux)。
