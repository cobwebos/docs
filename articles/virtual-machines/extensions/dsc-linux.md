---
title: 适用于 Linux 的 Azure DSC 扩展
description: 安装 OMI 和 DSC 包，以便能够使用 Desired State Configuration 来配置 Azure Linux VM。
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 2f04b5ecb2019a77bbb38e97c3869cc0a9447955
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921515"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>适用于 Linux 的 DSC 扩展（Microsoft.ostcextensions. DSCForLinux）

Desired State Configuration （DSC）是一个管理平台，可用于使用配置作为代码来管理 IT 和开发基础结构。

> [!NOTE]
> 适用于 linux 的 DSC 扩展和适用于[linux 的 Azure Monitor 虚拟机扩展](/azure/virtual-machines/extensions/oms-linux)当前存在冲突，并行配置中不支持。 不要在同一 VM 上一起使用这两个解决方案。

DSCForLinux 扩展已发布并受 Microsoft 支持。 该扩展在 Azure 虚拟机上安装 OMI 和 DSC 代理。 DSC 扩展还可以执行以下操作：


- 将 Linux VM 注册到 Azure 自动化帐户，以从 Azure 自动化服务中提取配置（注册 ExtensionAction）。
- 将 MOF 配置推送到 Linux VM （Push ExtensionAction）。
- 将元 MOF 配置应用到 Linux VM，以配置请求服务器以便请求节点配置（Pull ExtensionAction）。
- 将自定义 DSC 模块安装到 Linux VM （安装 ExtensionAction）。
- 从 Linux VM 删除自定义 DSC 模块（删除 ExtensionAction）。

 

## <a name="prerequisites"></a>必备条件

### <a name="operating-system"></a>操作系统

DSC Linux 扩展支持所有[在 Azure 上认可的 Linux 分发版](/azure/virtual-machines/linux/endorsed-distros)，除了以下这些：

| 分发 | 版本 |
|---|---|
| Debian | 所有版本 |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Internet 连接

DSCForLinux 扩展要求目标虚拟机连接到 internet。 例如，注册扩展需要连接到自动化服务。 对于其他操作，例如请求、请求、安装，需要连接到 Azure 存储和 GitHub。 这取决于客户提供的设置。

## <a name="extension-schema"></a>扩展架构

### <a name="public-configuration"></a>公共配置

下面是所有支持的公共配置参数：

* `FileUri`：（可选，string） MOF 文件、元 MOF 文件或自定义资源 zip 文件的 uri。
* `ResourceName`：（可选，string）自定义资源模块的名称。
* `ExtensionAction`：（可选，字符串）指定扩展的功能。 有效值为 Register、Push、Pull、Install 和 Remove。 如果未指定此值，则默认情况下将其视为推送操作。
* `NodeConfigurationName`：（可选，string）要应用的节点配置的名称。
* `RefreshFrequencyMins`：（可选，int）指定 DSC 尝试从请求服务器获取配置的频率（以分钟为单位）。 
       如果请求服务器上的配置不同于目标节点上的配置，则会将其复制到挂起存储区并应用。
* `ConfigurationMode`：（可选，字符串）指定 DSC 如何应用配置。 有效值为 ApplyOnly、ApplyAndMonitor 和 ApplyAndAutoCorrect。
* `ConfigurationModeFrequencyMins`：（可选，整数）指定 DSC 确保配置处于所需状态的频率（以分钟为单位）。

> [!NOTE]
> 如果使用早于2.3 的版本，则模式参数与 ExtensionAction 相同。 模式似乎是重载的字词。 为了避免混淆，ExtensionAction 从版本2.3 开始使用。 为了向后兼容，扩展支持 mode 和 ExtensionAction。 
>

### <a name="protected-configuration"></a>受保护的配置

下面是所有支持的受保护配置参数：

* `StorageAccountName`：（可选，string）包含该文件的存储帐户的名称
* `StorageAccountKey`：（可选，string）包含该文件的存储帐户的密钥
* `RegistrationUrl`：（可选，string） Azure Automation 帐户的 URL
* `RegistrationKey`：（可选，string） Azure Automation 帐户的访问密钥


## <a name="scenarios"></a>方案

### <a name="register-an-azure-automation-account"></a>注册 Azure 自动化帐户
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell 格式
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>将 MOF 配置文件（在 Azure 存储帐户中）应用于 VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell 格式
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>将 MOF 配置文件（在公共存储中）应用于 VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell 格式
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>将元 MOF 配置文件（在 Azure 存储帐户中）应用于 VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell 格式
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>将元 MOF 配置文件（在公共存储中）应用到 VM
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
PowerShell 格式
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>将自定义资源模块（Azure 存储帐户中的 zip 文件）安装到 VM
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell 格式
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>将自定义资源模块（公用存储中的 zip 文件）安装到 VM
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
PowerShell 格式
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>从 VM 中删除自定义资源模块
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
PowerShell 格式
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 部署需要部署后配置的一个或多个虚拟机（例如载入 Azure Automation）时，模板是理想选择。 

[201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) 和 [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu) 是示例资源管理器模板。

有关 Azure 资源管理器模板的详细信息，请参阅[创作 Azure 资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)。


## <a name="azure-cli-deployment"></a>Azure CLI 部署

### <a name="use-azure-cliazure-cli"></a>使用 [Azure CLI] [Azure-CLI]
在部署 DSCForLinux 扩展之前，请根据第3部分中的不同方案配置 `public.json` 和 `protected.json`。

#### <a name="classic"></a>经典

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

经典部署模式也称为 Azure 服务管理模式。 可运行以下命令切换到该模式：
```
$ azure config mode asm
```

可以通过运行以下内容来部署 DSCForLinux 扩展：
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

若要了解最新可用的扩展版本，请运行：
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>资源管理器
可运行以下命令切换到 Azure 资源管理器模式：
```
$ azure config mode arm
```

可以通过运行以下内容来部署 DSCForLinux 扩展：
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> 在 Azure 资源管理器模式下，`azure vm extension list` 目前不可用。
>

### <a name="use-azure-powershellazure-powershell"></a>使用 [Azure PowerShell] [Azure-PowerShell]

#### <a name="classic"></a>经典

可以通过运行以下操作，在 Azure 服务管理模式下登录到 Azure 帐户：

```powershell>
Add-AzureAccount
```

并通过运行以下内容来部署 DSCForLinux 扩展：

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

根据上一节中的不同方案更改 $privateConfig 和 $publicConfig 的内容。
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>资源管理器

可以通过运行以下操作，在 Azure 资源管理器模式下登录 Azure 帐户：

```powershell>
Login-AzAccount
```

若要详细了解如何使用 Azure 资源管理器 Azure PowerShell，请参阅[使用 Azure PowerShell 管理 Azure 资源](../../azure-resource-manager/management/manage-resources-powershell.md)。

可以通过运行以下内容来部署 DSCForLinux 扩展：

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

根据上一节中的不同方案更改 $privateConfig 和 $publicConfig 的内容。
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure CLI 运行以下命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

扩展执行输出将记录到以下文件：

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

错误代码：51表示不支持的分发或不受支持的扩展操作。
在某些情况下，当计算机中已存在 OMI 的更高版本时，DSC Linux 扩展无法安装 OMI。 [错误响应: (000003)不允许降级]



### <a name="support"></a>支持

如果在本文的任何位置需要更多帮助，请联系 MSDN Azure 上的 Azure 专家[并 Stack Overflow 论坛](https://azure.microsoft.com/support/community/)。 或者，你可以提供 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "**获取支持**"。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤
有关扩展的详细信息，请参阅[适用于 Linux 的虚拟机扩展和功能](features-linux.md)。
