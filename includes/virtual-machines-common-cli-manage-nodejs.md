通过 Resource Manager 命令和模板使用 Azure CLI 以利用资源组部署 Azure 资源和工作负荷之前，需要一个 Azure 帐户。 如果没有帐户，可以[在此处获取免费 Azure 试用帐户](https://azure.microsoft.com/pricing/free-trial/)。

如果尚未安装 Azure CLI 并连接到订阅，请参阅[安装 Azure CLI](../articles/cli-install-nodejs.md) 以使用 `azure config mode arm` 将模式设置为 `arm`，并使用 `azure login` 命令连接到 Azure。

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- Azure CLI 1.0 - 用于经典部署模型和资源管理部署模型（本文）的 CLI
- [Azure CLI 2.0](../articles/virtual-machines/linux/cli-manage.md) - 适用于资源管理部署模型的下一代 CLI

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Azure CLI 中的基本 Azure Resource Manager 命令
本文介绍需要用于 Azure CLI 的基本命令，以便在 Azure 订阅中管理资源（主要 VM）并与之交互。  有关特定命令行开关和选项的详细帮助，可以通过键入 `azure <command> <subcommand> --help` 或 `azure help <command> <subcommand>` 来使用联机命令帮助和选项。

> [!NOTE]
> 这些示例不包括通常为 Resource Manager 中的 VM 部署建议的基于模板的操作。 有关信息，请参阅[将 Azure CLI 与 Azure Resource Manager 配合使用](../articles/xplat-cli-azure-resource-manager.md)和[使用 Azure Resource Manager 模板和 Azure CLI 部署和管理虚拟机](../articles/virtual-machines/linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
> 
> 

| 任务 | Resource Manager |
| --- | --- | --- |
| 创建最基本的 VM |`azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>（从 `azure vm image list` 命令获取 `image-urn`。 有关示例，请参阅[此文](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。） |
| 创建 Linux VM |`azure  vm create [options] <resource-group> <name> <location> -y "Linux"` |
| 创建 Windows VM |`azure  vm create [options] <resource-group> <name> <location> -y "Windows"` |
| 列出 VM |`azure  vm list [options]` |
| 获取有关 VM 的信息 |`azure  vm show [options] <resource_group> <name>` |
| 启动 VM |`azure vm start [options] <resource_group> <name>` |
| 停止 VM |`azure vm stop [options] <resource_group> <name>` |
| 释放 VM |`azure vm deallocate [options] <resource-group> <name>` |
| 重新启动 VM |`azure vm restart [options] <resource_group> <name>` |
| 删除 VM |`azure vm delete [options] <resource_group> <name>` |
| 捕获 VM |`azure vm capture [options] <resource_group> <name>` |
| 从用户映像创建 VM |`azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>` |
| 从专用磁盘创建 VM |`azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>` |
| 将数据磁盘添加到 VM |`azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]` |
| 从 VM 中删除数据磁盘 |`azure  vm disk detach [options] <resource-group> <vm-name> <lun>` |
| 将泛型扩展添加到 VM |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>` |
| 将 VM 访问扩展添加到 VM |`azure vm reset-access [options] <resource-group> <name>` |
| 将 Docker 扩展添加到 VM |`azure  vm docker create [options] <resource-group> <name> <location> <os-type>` |
| 删除 VM 扩展 |`azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>` |
| 获取 VM 资源的使用情况 |`azure vm list-usage [options] <location>` |
| 获取所有可用 VM 大小 |`azure vm sizes [options]` |

## <a name="next-steps"></a>后续步骤
* 有关超越基本 VM 管理的其他 CLI 命令示例，请参阅[将 Azure CLI 与 Azure Resource Manager 配合使用](../articles/virtual-machines/azure-cli-arm-commands.md)。
