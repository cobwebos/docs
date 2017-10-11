---
title: "使用故障排除与 Azure CLI 1.0 的 VM 的 Linux 的 |Microsoft 文档"
description: "了解如何连接到恢复使用 Azure CLI 1.0 VM 的 OS 磁盘 Linux VM 问题进行故障排除"
services: virtual-machines-linux
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: d817358211f123c96d899c5cff88cc47aeb5c9c1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-cli-10"></a>Linux VM 的附加到恢复使用 Azure CLI 1.0 VM 的 OS 磁盘进行故障排除
如果你的 Linux 虚拟机 (VM) 遇到了启动或磁盘错误，你可能需要在虚拟硬盘本身上执行故障排除步骤。 一个常见示例是中的无效条目`/etc/fstab`阻止 VM 能够成功启动。 本文详细介绍如何使用 Azure CLI 1.0 连接到另一个 Linux VM，以修复任何错误，然后重新创建原始 VM 的虚拟硬盘的步骤。


## <a name="cli-versions-to-complete-the-task"></a>要完成任务的 CLI 版本
你可以完成该任务使用以下的 CLI 版本之一：

- [Azure CLI 1.0](#recovery-process-overview) – 我们适用于经典部署模型和资源管理部署模型 （本文） CLI
- [Azure CLI 2.0](../windows/troubleshoot-recovery-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) -我们下一代 CLI 用于资源管理部署模型


## <a name="recovery-process-overview"></a>恢复过程概述
进行故障排除过程如下所示：

1. 删除 VM 遇到问题，从而使虚拟硬盘。
2. 连接和进行故障排除安装到另一个 Linux VM 虚拟硬盘的步骤。
3. 连接到故障排除的 VM。 编辑文件或运行任何工具用于原始虚拟硬盘上修复问题。
4. 卸载并分离该虚拟硬盘从故障排除的虚拟机。
5. 创建使用原始虚拟硬盘的 VM。

请确保你具有[最新的 Azure CLI 1.0](../../cli-install-nodejs.md)安装和登录和使用资源管理器模式：

```azurecli
azure config mode arm
```

在以下示例中，将参数名称替换你自己的值。 示例的参数名称包含`myResourceGroup`， `mystorageaccount`，和`myVM`。


## <a name="determine-boot-issues"></a>确定启动问题
检查串行输出来确定你的 VM 不能正常启动的原因。 一个常见示例是中的无效条目`/etc/fstab`，或与底层的虚拟硬盘被删除或移动。

下面的示例从名为的 VM 获取串行输出`myVM`资源组中名为`myResourceGroup`:

```azurecli
azure vm get-serial-output --resource-group myResourceGroup --name myVM
```

检查串行输出，以确定失败的 VM 启动的原因。 如果串行输出不提供任何指示，你可能需要查看中的日志文件`/var/log`一旦连接到故障排除的 VM 的磁盘的虚拟硬盘。


## <a name="view-existing-virtual-hard-disk-details"></a>查看现有虚拟硬盘的步骤的详细信息
你可以将该虚拟硬盘附加到另一个 VM 之前，需要标识虚拟硬盘 (VHD) 的名称。 

下面的示例获取名为的 VM 的信息`myVM`资源组中名为`myResourceGroup`:

```azurecli
azure vm show --resource-group myResourceGroup --name myVM
```

查找`Vhd URI`前一个命令的输出中。 以下截断示例输出所示`Vhd URI`的最后一行：

```azurecli
info:    Executing command vm show
+ Looking up the VM "myVM"
+ Looking up the NIC "myNic"
+ Looking up the public ip "myPublicIP"
...
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :myVM
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/myVM201610292712.vhd
```


## <a name="delete-existing-vm"></a>删除现有的 VM
虚拟硬盘和虚拟机是在 Azure 中的两个不同的资源。 虚拟硬盘是操作系统本身，应用程序和配置的存储位置。 在 VM 本身是只是定义的大小或位置，并引用资源，如虚拟硬盘或虚拟网络接口卡 (NIC) 的元数据。 每个虚拟硬盘已附加到 VM 时分配的租约。 虽然可以附加数据磁盘，并且将其分离即使在 VM 正在运行，除非删除 VM 资源，否则无法分离操作系统磁盘。 租约继续将操作系统磁盘与 VM 关联，即使该 VM 处于已停止和释放的状态。

恢复你的 VM 的第一步是删除 VM 资源本身。 删除 VM 使你的存储帐户中的虚拟硬盘。 删除 VM 后，你将虚拟硬盘附加到另一个 VM 排除故障并解决这些错误。

下面的示例删除名为的 VM`myVM`从资源组名为`myResourceGroup`:

```azurecli
azure vm delete --resource-group myResourceGroup --name myVM 
```

等待，直到 VM 完成删除之前将虚拟硬盘附加到另一个 VM。 将其与 VM 关联的虚拟硬盘上的租约需要释放，然后可以将虚拟硬盘附加到另一个 VM。


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>将现有虚拟硬盘附加到另一个 VM
对于下一步的几个步骤，可以使用另一个虚拟机进行故障排除。 将现有虚拟硬盘附加到此故障排除的 VM，以浏览和编辑磁盘的内容。 此过程，可更正任何配置错误或查看其他应用程序或系统日志文件，例如。 选择或创建另一个 VM 使用用于进行故障排除。

附加现有的虚拟硬盘时，指定在前面获取的磁盘的 URL`azure vm show`命令。 下面的示例将现有的虚拟硬盘附加到名为的故障排除 VM`myVMRecovery`资源组中名为`myResourceGroup`:

```azurecli
azure vm disk attach --resource-group myResourceGroup --name myVMRecovery \
    --vhd-url https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>安装附加的数据磁盘

> [!NOTE]
> 以下示例详细说明在 Ubuntu VM 上所需的步骤。 如果你使用不同的 Linux 发行版，如 Red Hat Enterprise Linux 或 SUSE 的日志文件位置和`mount`命令都可能稍有不同。 请参阅命令中的相应更改特定的分发版的文档。

1. Ssh 连接到你故障排除的 VM，使用相应的凭据。 如果此磁盘已附加到你故障排除的 VM 的第一个数据磁盘，磁盘可能连接到`/dev/sdc`。 使用`dmseg`若要查看附加的磁盘：

    ```bash
    dmesg | grep SCSI
    ```

    输出为类似于下面的示例：

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    在前面的示例中，操作系统磁盘位于`/dev/sda`和临时磁盘提供的每个虚拟机位于`/dev/sdb`。 如果你具有多个数据磁盘，它们都应位于`/dev/sdd`， `/dev/sde`，依次类推。

2. 创建一个目录来装载你现有的虚拟硬盘。 下面的示例创建一个名为目录`troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. 如果你现有的虚拟硬盘上有多个分区，装入所需的分区。 下面的示例装载在第一主分区`/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > 最佳做法是在 Azure 中使用虚拟硬盘的全局唯一标识符 (UUID) 中的 Vm 上安装数据磁盘。 对于此短的故障排除方案，装载虚拟硬盘使用 UUID 不需要。 但是，在正常使用，编辑`/etc/fstab`装载虚拟硬盘使用的设备名称，而不是 UUID 可能会导致 VM 无法引导。


## <a name="fix-issues-on-original-virtual-hard-disk"></a>原始虚拟硬盘上修复问题
使用现有虚拟硬盘装载，现在可以执行任何维护和故障排除步骤，根据需要。 一旦已经解决了问题，继续执行以下步骤。


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>卸载并分离原始虚拟硬盘
解决你的错误后，你卸载并从你的故障排除 VM 分离现有虚拟硬盘。 直到释放租约将虚拟硬盘附加到故障排除的 VM，不能与任何其他 VM 使用该虚拟硬盘。

1. 从 SSH 会话到疑难解答 VM 中，卸载现有的虚拟硬盘。 第一次更改超出你的装入点的父目录：

    ```bash
    cd /
    ```

    现在卸载现有的虚拟硬盘。 下面的示例会在设备中卸载`/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. 现在分离磁盘的虚拟硬盘从虚拟机。 到疑难解答 VM 退出 SSH 会话。 在 Azure CLI，首先列出到疑难解答 VM 的附加的数据磁盘。 下面的示例列出连接到名为的 VM 的数据磁盘`myVMRecovery`资源组中名为`myResourceGroup`:

    ```azurecli
    azure vm disk list --resource-group myResourceGroup --vm-name myVMRecovery
    ```

    请注意`Lun`为你现有的虚拟硬盘的值。 以下示例命令输出显示在 LUN 0 附加的现有虚拟磁盘：

    ```azurecli
    info:    Executing command vm disk list
    + Looking up the VM "myVMRecovery"
    data:    Name              Lun  DiskSizeGB  Caching  URI
    data:    ------            ---  ----------  -------  ------------------------------------------------------------------------
    data:    myVM              0                None     https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    info:    vm disk list command OK
    ```

    分离数据磁盘与你使用适用的 VM`Lun`值：

    ```azurecli
    azure vm disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --lun 0
    ```


## <a name="create-vm-from-original-hard-disk"></a>从原始硬盘创建 VM
若要从原始虚拟硬盘创建 VM，使用[此 Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd)。 实际的 JSON 模板位于以下链接：

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json

该模板将 VM 部署到现有的虚拟网络，使用较早的命令中的 VHD URL。 下面的示例将模板部署到名为的资源组`myResourceGroup`:

```azurecli
azure group deployment create --resource-group myResourceGroup --name myDeployment \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

如 VM 名称模板中回答提示 (`myDeployedVM`下面的示例)，OS 类型 (`Linux`)，和 VM 大小 (`Standard_DS1_v2`)。 `osDiskVhdUri`相同的以前所用时将现有虚拟硬盘附加到故障排除的 VM。 命令输出和提示的一个示例是，如下所示：

```azurecli
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName:  myDeployedVM
osType:  Linux
osDiskVhdUri:  https://mystorageaccount.blob.core.windows.net/vhds/myVM201610292712.vhd
vmSize:  Standard_DS1_v2
existingVirtualNetworkName:  myVnet
existingVirtualNetworkResourceGroup:  myResourceGroup
subnetName:  mySubnet
dnsNameForPublicIP:  mypublicipdeployed
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "mydeployment"
+ Waiting for deployment to complete
+
```


## <a name="re-enable-boot-diagnostics"></a>重新启用引导诊断

在你的 VM 创建从现有虚拟硬盘时，启动可能不会自动启用诊断。 以下示例启用名为的 VM 上的诊断扩展`myDeployedVM`资源组中名为`myResourceGroup`:

```azurecli
azure vm enable-diag --resource-group myResourceGroup --name myDeployedVM
```

## <a name="next-steps"></a>后续步骤
如果你有连接到你的 VM 的问题，请参阅[解决 SSH 连接到 Azure VM](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 有关访问在你的 VM 上运行的应用程序问题，请参阅[排除的 Linux VM 上的应用程序连接问题](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。