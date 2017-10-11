Azure CLI 2.0，可创建和管理在 macOS、 Linux 和 Windows Azure 资源。 本文详细介绍某些最常见的命令来创建和管理虚拟机 (Vm)。

本文需要 Azure CLI 版本 2.0.4 或更高版本。 运行`az --version`来查找版本。 如果你需要升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 你还可以使用[云 Shell](/azure/cloud-shell/quickstart)从浏览器。

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Azure CLI 中的基本 Azure 资源管理器命令
有关详细使用特定的命令行开关和选项的帮助，你可以通过键入使用联机命令帮助和选项`az <command> <subcommand> --help`。

### <a name="create-vms"></a>创建 Vm
| 任务 | Azure CLI 命令 |
| --- | --- |
| 创建资源组 | `az group create --name myResourceGroup --location eastus` |
| 创建 Linux VM | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| 创建 Windows VM | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>管理 VM 状态
| 任务 | Azure CLI 命令 |
| --- | --- |
| 启动 VM | `az vm start --resource-group myResourceGroup --name myVM` |
| 停止 VM | `az vm stop --resource-group myResourceGroup --name myVM` |
| 释放 VM | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| 重新启动 VM | `az vm restart --resource-group myResourceGroup --name myVM` |
| 重新部署 VM | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| 删除 VM | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>获取 VM 信息
| 任务 | Azure CLI 命令 |
| --- | --- |
| 列出 Vm | `az vm list` |
| 获取有关 VM 的信息 | `az vm show --resource-group myResourceGroup --name myVM` |
| 获取 VM 资源的使用 | `az vm list-usage --location eastus` |
| 获取所有可用 VM 大小 | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>磁盘和映像
| 任务 | Azure CLI 命令 |
| --- | --- |
| 将数据磁盘添加到 VM | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new ` |
| 从 VM 中删除数据磁盘 | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| 调整磁盘的大小 | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| 快照磁盘 | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| 创建 VM 的映像 | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| 从映像创建 VM | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>后续步骤
有关 CLI 命令的其他示例，请参阅[创建和使用 Azure CLI 管理 Linux Vm](../articles/virtual-machines/linux/tutorial-manage-vm.md)教程。

