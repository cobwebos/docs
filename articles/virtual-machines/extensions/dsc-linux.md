---
title: Linux 的 Azure DSC 扩展
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250616"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Linux 的 DSC 扩展（微软.OSTC 扩展.DSCForLinux）

所需的状态配置 （DSC） 是一个管理平台，您可以使用该平台以代码配置来管理 IT 和开发基础结构。

> [!NOTE]
> Linux 的 DSC 扩展和[Linux 的 Azure 监视器虚拟机扩展](/azure/virtual-machines/extensions/oms-linux)当前存在冲突，在并行配置中不受支持。 不要在同一 VM 上同时使用这两个解决方案。

DSCForLinux 扩展由微软发布和支持。 该扩展在 Azure 虚拟机上安装 OMI 和 DSC 代理。 DSC 扩展还可以执行以下操作：


- 将 Linux VM 注册到 Azure 自动化帐户，以便从 Azure 自动化服务（注册扩展操作）中提取配置。
- 将 MOF 配置推送到 Linux VM（推送扩展操作）。
- 将元 MOF 配置应用于 Linux VM 以配置拉取服务器以拉取节点配置（拉扩展操作）。
- 将自定义 DSC 模块安装到 Linux VM（安装扩展操作）。
- 从 Linux VM 中删除自定义 DSC 模块（删除扩展操作）。

 

## <a name="prerequisites"></a>先决条件

### <a name="operating-system"></a>操作系统

DSC Linux 扩展支持所有[在 Azure 上认可的 Linux 分发版](/azure/virtual-machines/linux/endorsed-distros)，除了以下这些：

| 分发 | 版本 |
|---|---|
| Debian | 所有版本 |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Internet 连接

DSCForLinux 扩展要求目标虚拟机连接到互联网。 例如，注册扩展需要连接到自动化服务。 对于其他操作（如拉取、拉取、安装）需要连接到 Azure 存储和 GitHub。 这取决于客户提供的设置。

## <a name="extension-schema"></a>扩展架构

### <a name="public-configuration"></a>公共配置

下面是所有支持的公共配置参数：

* `FileUri`：（可选，字符串）MOF 文件的 uri、元 MOF 文件或自定义资源 zip 文件。
* `ResourceName`：（可选，字符串）自定义资源模块的名称。
* `ExtensionAction`：（可选，字符串）指定扩展的功能。 有效值为"注册、推送、拉取、安装"和"删除"。 如果未指定，默认情况下将被视为推送操作。
* `NodeConfigurationName`：（可选，字符串）要应用的节点配置的名称。
* `RefreshFrequencyMins`：（可选，int） 指定 DSC 尝试从拉取服务器获取配置的频率（以分钟）表示。 
       如果拉取服务器上的配置与目标节点上的当前配置不同，则将其复制到挂起的存储并应用。
* `ConfigurationMode`：（可选，字符串）指定 DSC 如何应用配置。 有效值为"仅应用、应用和监视"以及应用和自动更正。
* `ConfigurationModeFrequencyMins`：（可选，整数）指定 DSC 确保配置处于所需状态的频率（以分钟为单位）。

> [!NOTE]
> 如果使用的版本早于 2.3，则模式参数与扩展操作相同。 Mode（模式）看上去像是一个重载的术语。 为了避免混淆，从版本 2.3 开始使用扩展操作。 为了向后兼容，扩展支持 mode 和 ExtensionAction。 
>

### <a name="protected-configuration"></a>受保护的配置

下面是所有支持的受保护配置参数：

* `StorageAccountName`：（可选，字符串）包含文件的存储帐户的名称
* `StorageAccountKey`：（可选，字符串）包含文件的存储帐户的键
* `RegistrationUrl`：（可选，字符串）Azure 自动化帐户的 URL
* `RegistrationKey`：（可选，字符串）Azure 自动化帐户的访问密钥


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

电源外壳格式
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

电源外壳格式
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

电源外壳格式
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

电源外壳格式
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
电源外壳格式
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

电源外壳格式
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

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>将自定义资源模块（公共存储中的 zip 文件）安装到 VM
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
电源外壳格式
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
电源外壳格式
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。 当您部署一个或多个需要部署后配置的虚拟机（如载入 Azure 自动化）时，模板是理想的选择。 

[201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) 和 [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu) 是示例资源管理器模板。

有关 Azure 资源管理器模板的详细信息，请参阅创作[Azure 资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)。


## <a name="azure-cli-deployment"></a>Azure CLI 部署

### <a name="use-azure-cliazure-cli"></a>使用 [Azure CLI][azure-cli]
在部署 DSCForLinux 扩展之前，请`public.json``protected.json`根据第 3 节中的不同方案配置 和。

#### <a name="classic"></a>经典

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

经典部署模式也称为 Azure 服务管理模式。 可运行以下命令切换到该模式：
```
$ azure config mode asm
```

您可以通过运行以下功能部署 DSCForLinux 扩展：
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

您可以通过运行以下功能部署 DSCForLinux 扩展：
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> 在 Azure 资源管理器模式下`azure vm extension list`，现在不可用。
>

### <a name="use-azure-powershellazure-powershell"></a>使用 [Azure 电源外壳][azure 电源外壳]

#### <a name="classic"></a>经典

您可以通过运行：

```powershell>
Add-AzureAccount
```

并通过运行 DSCForLinux 扩展：

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

根据上一节中的不同方案更改$privateConfig和$publicConfig的内容。
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

您可以通过运行：

```powershell>
Login-AzAccount
```

要了解有关如何在 Azure 资源管理器中使用 Azure PowerShell 的更多信息，请参阅[使用 Azure PowerShell 管理 Azure 资源](../../azure-resource-manager/management/manage-resources-powershell.md)。

您可以通过运行以下功能部署 DSCForLinux 扩展：

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

根据上一节中的不同方案更改$privateConfig和$publicConfig的内容。
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

### <a name="troubleshoot"></a>疑难解答

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 检索。 要查看给定 VM 的扩展的部署状态，请使用 Azure CLI 运行以下命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

扩展执行输出将记录到以下文件：

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

错误代码：51 表示不支持的分发或不支持的扩展操作。
在某些情况下，当计算机中已存在更高版本的 OMI 时，DSC Linux 扩展无法安装 OMI。 [错误响应: (000003)不允许降级]



### <a name="support"></a>支持

如果本文中的任何一点都需要更多帮助，请与[MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/community/)上的 Azure 专家联系。 或者，您可以提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)，然后选择 **"获取支持**"。 有关使用 Azure 支持的信息，请阅读[Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤
有关扩展的详细信息，请参阅[适用于 Linux 的虚拟机扩展和功能](features-linux.md)。
