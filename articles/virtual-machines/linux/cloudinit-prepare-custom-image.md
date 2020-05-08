---
title: 准备用于云初始化的 Azure VM 映像
description: 如何准备一个与 cloud-init 配合用来完成部署的现有 Azure VM 映像
author: danis
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: article
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: c41368b311708d5ead36d589cf9c320787e596ec
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792303"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>准备与 cloud-init 配合使用的现有 Linux Azure VM 映像
本文介绍如何选择一个现有的 Azure 虚拟机，使其准备好重新部署并可使用 cloud-init。 生成的映像可用于部署新的虚拟机或虚拟机规模集 - 然后，可以在部署时通过 cloud-init 进一步对其进行自定义。  Azure 预配资源后，这些 cloud-init 脚本即会在第一次启动时运行。 有关 cloud-init 如何在 Azure 以及受支持的 Linux 发行版中本机工作的详细信息，请参阅 [cloud-init 概述](using-cloud-init.md)

## <a name="prerequisites"></a>必备条件
本文档假设已有一个运行受支持 Linux 操作系统版本的 Azure 虚拟机。 已根据需要配置该计算机，已安装所需的所有模块，已处理所需的所有更新并已对其进行测试，确保其满足要求。 

## <a name="preparing-rhel-76--centos-76"></a>准备 RHEL 7.6/CentOS 7。6
需要通过 SSH 连接到 Linux VM，并运行以下命令安装 cloud-init。

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

更新 `cloud_init_modules` 中的 `/etc/cloud/cloud.cfg` 节以包含以下模块：

```bash
- disk_setup
- mounts
```

以下示例显示某个通用 `cloud_init_modules` 节的大致内容。

```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```

需要在 `/etc/waagent.conf` 中更新一些与预配和处理临时磁盘相关的任务。 运行以下命令更新相应的设置。

```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

使用所选的编辑器创建包含以下行的新文件 `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg`，以便只允许将 Azure 用作 Azure Linux 代理的数据源：

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

如果现有 Azure 映像中配置了交换文件，而你想要使用 cloud-init 更改新映像的交换文件配置，则需要删除现有的交换文件。

对于基于 RedHat 的映像，请遵照有关如何[删除交换文件](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file)的 RedHat 文档中的说明。

对于已启用交换文件的 CentOS 映像，可运行以下命令来关闭交换文件：

```bash
sudo swapoff /mnt/resource/swapfile
```

确保从 `/etc/fstab` 中删除交换文件引用 - 应返回如下所示的输出：

```output
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

若要节省空间并删除交换文件，可运行以下命令：

```bash
rm /mnt/resource/swapfile
```

## <a name="extra-step-for-cloud-init-prepared-image"></a>针对 cloud-init 准备的映像的附加步骤
> [!NOTE]
> 如果映像是事先已由 **cloud-init** 准备且配置的映像，则需要执行以下步骤。

仅当想要自定义为新专用源映像的 VM 事先已由 cloud-init 预配时，才需要使用以下三个命令。  如果映像是使用 Azure Linux 代理配置的，则不需要运行这些命令。

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>完成 Linux 代理设置 
所有 Azure 平台映像都装有 Azure Linux 代理，不管它们是否已由 cloud-init 配置。  运行以下命令完成从 Linux 计算机中取消预配用户。 

```bash
sudo waagent -deprovision+user -force
```

有关 Azure Linux 代理取消预配命令的详细信息，请参阅 [Azure Linux 代理](../extensions/agent-linux.md)。

退出 SSH 会话，然后从 bash shell 运行以下 AzureCLI 命令，以解除分配、通用化并创建新的 Azure VM 映像。  请将 `myResourceGroup` 和 `sourceVmName` 替换为反映源 VM 的相应信息。

```azurecli
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>后续步骤
有关配置更改的其他 cloud-init 示例，请参阅以下文章：
 
- [向 VM 添加其他 Linux 用户](cloudinit-add-user.md)
- [运行包管理器以在首次启动时更新现有包](cloudinit-update-vm.md)
- [更改 VM 本地主机名](cloudinit-update-vm-hostname.md) 
- [安装应用程序包、更新配置文件和注入密钥](tutorial-automate-vm-deployment.md)
