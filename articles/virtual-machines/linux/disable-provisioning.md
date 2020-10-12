---
title: 禁用或删除预配代理
description: 了解如何禁用或删除 Linux VM 和映像中的预配代理。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 2a17825d062496e6600966dc7c90b14749507e4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86494507"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>禁用或删除 VM 和映像中的 Linux 代理

在删除 Linux 代理之前，你必须了解在删除 Linux 代理后 VM 将无法执行哪些操作。

Azure 虚拟机 (VM) [扩展](../extensions/overview.md)是小型应用程序，可在 Azure VM 上提供部署后配置和自动化任务。扩展由 Azure 控制平面安装并管理。 [Azure Linux 代理](../extensions/agent-linux.md)的职责是处理平台扩展命令并确保 VM 中的扩展处于正确状态。

Azure 平台可承载许多扩展，其中包括 VM 配置、监视、安全性和实用工具应用程序。 第一方和第三方扩展都有很大的选择范围，使用扩展的主要方案示例包括：
* 支持第一方 Azure 服务，例如 Azure 备份、监视、磁盘加密、安全性、站点复制，等等。
* SSH/密码重置
* VM 配置 - 运行自定义脚本，安装 Chef、Puppet 代理，等等。
* 第三方产品，例如 AV 产品、VM 漏洞工具、VM 和应用监视工具。
* 可以使用新的 VM 部署捆绑扩展。 例如，它们可能属于大型部署中的一部分，在 VM 预配上配置应用程序，或针对任何受支持的扩展操作系统后部署运行。

## <a name="disabling-extension-processing"></a>禁用扩展处理

可以通过多种方式来禁用扩展处理（具体取决于你的需求），但在继续之前，**必须**删除部署到 VM 的所有扩展，例如，可以使用 AZ CLI 执行[列出](/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-list)和[删除](/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-delete)操作：

```bash
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> 如果未执行上述操作，则平台会尝试发送扩展配置并在 40 分钟后超时。

### <a name="disable-at-the-control-plane"></a>在控制平面中禁用
如果你不确定将来是否需要扩展，则可以保留 VM 上安装的 Linux 代理，然后从平台禁用扩展处理功能。 这是 `Microsoft.Compute` api 版本 `2018-06-01` 或更高版本中提供的选项，不依赖于所安装的 Linux 代理版本。

```bash
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
可以通过上述命令轻松地从平台重新启用此扩展处理，但启用时请将其设置为“true”。

## <a name="remove-the-linux-agent-from-a-running-vm"></a>从正在运行的 VM 中删除 Linux 代理

请确保先从 VM 中删除所有现有扩展，如上所述。

### <a name="step-1-remove-the-azure-linux-agent"></a>步骤 1：删除 Azure Linux 代理

如果只是删除 Linux 代理，而不删除关联的配置项目，则可在以后重新安装。 以 root 身份运行以下命令之一，以便删除 Azure Linux 代理：

#### <a name="for-ubuntu-1804"></a>对于 Ubuntu 18.04 及更高版本
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>对于 Redhat 7.7 及更高版本
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>对于 SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>步骤 2：（可选）删除 Azure Linux 代理项目
> [!IMPORTANT] 
>
> 你可以删除 Linux 代理的所有关联项目，但这意味着你以后无法重新安装它。 因此，强烈建议你首先考虑禁用 Linux 代理，只使用以上方法删除 Linux 代理。 

如果你知道以后不会再重新安装 Linux 代理，则可以运行以下命令：

#### <a name="for-ubuntu-1804"></a>对于 Ubuntu 18.04 及更高版本
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>对于 Redhat 7.7 及更高版本
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>对于 SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>准备不含 Linux 代理的映像
如果你有一个已包含 cloud-init 的映像，并且你想要删除 Linux 代理，但仍使用 cloud-init 进行预配，请以 root 身份运行步骤 2 中的步骤（步骤 3 为可选）来删除 Azure Linux 代理，然后通过以下命令删除 cloud-init 配置和缓存的数据，并准备 VM 以创建自定义映像。

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>取消预配并创建映像
Linux 代理能够通过步骤“waagent -deprovision+user”清理一些现有的映像元数据，但在将其删除后，你需要执行如下操作，并删除其中的任何其他敏感数据。

- 删除所有现有的 ssh 主机密钥

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- 删除管理员帐户

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- 删除 root 密码

   ```bash
   passwd -d root
   ```

完成上述操作后，可以使用 Azure CLI 创建自定义映像。


**创建常规托管映像**
```bash
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**在共享映像库中创建映像版本**

```bash
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>从不包含 Linux 代理的映像创建 VM
从不包含 Linux 代理的映像创建 VM 时，需要确保 VM 部署配置指示此 VM 上不支持扩展。

> [!NOTE] 
> 
> 如果未执行上述操作，则平台会尝试发送扩展配置并在 40 分钟后超时。

若要在禁用扩展的情况下部署 VM，你可以将 Azure CLI 与 [--enable-agent](/cli/azure/vm#az-vm-create) 结合使用。

```bash
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

另外，可以使用 Azure 资源管理器 (ARM) 模板，通过设置 `"provisionVMAgent": false,` 来执行此操作。

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[预配 Linux](provisioning.md)。
