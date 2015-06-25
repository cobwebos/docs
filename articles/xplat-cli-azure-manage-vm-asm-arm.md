<properties
	pageTitle="适合使用针对 Mac、Linux 和 Windows 的 Azure CLI 进行 VM 操作的等效资源管理器和服务管理命令"
	description="显示在资源管理器和服务管理模式中用于创建和管理 Azure VM 的等效 Azure CLI 命令"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.date="04/28/2015"
	wacn.date=""/>


# 适合使用针对 Mac、Linux 和 Windows 的 Azure CLI 进行 VM 操作的等效资源管理器和服务管理命令
本文介绍用于在服务管理 (asm) 模式和资源管理器 (arm) 模式下创建和管理 Azure VM 的等效 Azure 命令行接口 (Azure CLI) 命令。在将脚本从一个命令模式迁移到其他命令模式时，请使用本文作为简易指南。

* 如果你尚未安装 Azure CLI 并连接到订阅，请参阅[安装用于 Mac、Linux 和 Windows 的 Azure CLI](xplat-cli-install) 和[从用于 Mac、Linux 和 Windows 的 Azure CLI 连接到 Azure 订阅](xplat-cli-connect)。使用 arm 模式命令时，请务必使用登录方法进行连接。

* 若要开始在 Azure CLI 中使用 arm 模式和切换命令模式，请参阅[将 Azure 命令行接口用于资源管理器](xplat-cli-azure-resource-manager)。

* 如需联机命令帮助和选项，请键入 `azure <command> <subcommand> --help` 或 `azure help <command> <subcommand>`。

## 方案
下面是你可以在 asm 和 arm 模式下使用 Azure CLI 命令执行的通用 VM 操作。由于存在许多 arm 模式命令，你需要传递现有资源组的名称。

> [AZURE.NOTE]在 arm 模式下，这些示例不包含基于模板的操作。有关信息，请参阅[将 Azure 命令行接口用于资源管理器](xplat-cli-azure-resource-manager)。

方案 | asm 模式 | arm 模式
-------------- | ----------- | -------------------------
创建最基本的 VM | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`
创建 Linux VM | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
创建 Windows VM | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
列出 VM | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
获取有关 VM 的信息 | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
启动 VM | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
停止 VM | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
重新启动 VM | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
删除 VM | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
捕获 VM | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
从用户映像创建 VM | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
从专用磁盘创建 VM | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
将数据磁盘添加到 VM | `azure  vm disk attach [options] <vm-name> <disk-image-name>` -或- <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
从 VM 中删除数据磁盘 | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
将泛型扩展添加到 VM | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
将 VM 访问扩展添加到 VM | 不可用 | `azure vm reset-access [options] <resource-group> <name>`
将 Chef 扩展添加到 VM | `azure  vm extension get-chef [options] <vm-name>` | 不可用
禁用 VM 扩展 | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | 不可用
删除 VM 扩展 | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
列出 VM 扩展 | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
列出 VM 映像 | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]` -或- <br/> `azure vm image list-publishers [options] <location>` -或- <br/> `azure vm image list-offers [options] <location>` -或- <br/> `azure vm image list-skus [options] <location>`
显示 VM 映像 | `azure vm image show [options]` | 不可用


## 后续步骤

* 有关使用 Azure CLI 来处理 arm 模式下的资源的详细信息，请参阅[将 Azure 命令行接口用于资源管理器](xplat-cli-azure-resource-manager)和[使用 Azure 命令行接口管理基于角色的访问控制](role-based-access-control-xplat-cli)。
* 如需 CLI 命令的其他示例，请参阅[将 Azure 命令行接口用于 Mac、Linux 和 Windows](virtual-machines-command-line-tools) 和[通过 Azure 资源管理器将 Azure CLI 用于 Mac、Linux 和 Windows](azure-cli-arm-commands)。

<!---HONumber=61-->