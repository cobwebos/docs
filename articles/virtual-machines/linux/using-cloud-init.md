---
title: "cloud-init 对 Azure 中 Linux 虚拟机的支持的概述 | Microsoft Docs"
description: "Microsoft Azure 中的 cloud-init 功能的概述"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 5c1d4eb0825d132037cc3a20a17c1f417578d35d
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>cloud-init 对 Azure 中虚拟机的支持
本文介绍了在 Azure 中使用 [cloud-init](https://cloudinit.readthedocs.io) 在预配时间配置虚拟机 (VM) 或虚拟机缩放集 (VMSS) 的现有支持。 Azure 预配资源后，这些 cloud-init 脚本即会在第一次启动时运行。  

## <a name="cloud-init-overview"></a>Cloud-init 概述
[Cloud-init](https://cloudinit.readthedocs.io) 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 安装程序包和写入文件，或者配置用户和安全。 由于是在初始启动过程中调用 cloud-init，因此无需额外的步骤且无需代理来应用配置。  有关如何正确设置 `#cloud-config` 文件格式的详细信息，请参阅 [cloud-init 文档站点](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)。  `#cloud-config` 文件是采用 base64 编码的文本文件。

Cloud-init 还支持不同的分发。 例如，不要使用 apt-get 安装或 yum 安装来安装包。 可定义要安装的程序包的列表。 Cloud-init 将为所选发行版自动使用本机包管理工具。

 我们正在积极地与我们认可的 Linux 发行版合作伙伴合作，以便在 Azure Marketplace 中提供已启用 cloud-init 的映像。 这些映像可使 cloud-init 部署和配置无缝地应用于 VM 和 VM 规模集 (VMSS)。 下表概述了当前启用了 cloud-init 的映像在 Azure 平台上的可用性：

| 发布者 | 产品 | SKU | 版本 | cloud-init 就绪
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |最新 |是 | 
|Canonical |UbuntuServer |14.04.5-LTS |最新 |是 |
|CoreOS |CoreOS |Stable |最新 |是 |
|OpenLogic |CentOS |7-CI |最新 |预览 |
|RedHat |RHEL |7-RAW-CI |最新 |预览 |

目前，Azure Stack 不支持使用 cloud-init 预配 RHEL 7.4 和 CentOS 7.4。

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init 和 Linux 代理 (WALA) 之间的区别是什么？
WALA 是一种特定于 Azure 平台的代理，用于预配和配置 VM 并处理 Azure 扩展。 我们正在优化配置 VM 的任务以使用 cloud-init 代替 Linux 代理，目的是让现有 cloud-init 客户可以使用他们当前的 cloud-init 脚本。  如果当前已使用 cloud-init 脚本来配置 Linux 系统，则不需要任何额外的设置就可以启用它们。 

如果在预配时未提供 Azure CLI `--custom-data` 开关，WALA 将采用所需的最小 VM 预配参数来预配 VM 并使用默认值完成部署。  如果引用 cloud-init `--custom-data` 开关，自定义数据中包含的任何内容（单个设置或完整脚本）都将替代 WALA 默认值。 

VM 的 WALA 配置的时限为最大 VM 预配时间。  cloud-init 配置应用于 VM 时没有时限，也不会因为超时导致部署失败。 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>部署已启用 cloud-init 的虚拟机
部署已启用 cloud-init 的虚拟机就和在部署期间引用已启用 cloud-init 的分发一样简单。  Linux 分发 Maintainer 需要选择启用 cloud-init，并将 cloud-init 集成到其基本 Azure 已发布映像中。 确认想要部署的映像已启用 cloud-init 之后，就可以使用 AzureCLI 部署映像。 

部署此映像的第一步是使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
下一步是在当前 shell 中创建名为 cloud-init.txt 的文件并粘贴以下配置。 对于此示例，请在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 输入 `sensible-editor cloud-init.txt` 以创建文件并查看可用编辑器的列表。 选择 #1 以使用 nano 编辑器。 请确保已正确复制整个 cloud-init 文件，尤其是第一行：

```yaml
#cloud-config
package_upgrade: true
packages:
  -httpd
```
按 `ctrl-X` 退出该文件，键入 `y` 以保存文件，并按 `enter` 确认退出时的文件名。

最后一步是使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建 VM。 

以下示例创建一个名为 centos74 的 VM，并且在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  使用 `--custom-data` 参数传递到 cloud-init 配置文件中。 如果未将 cloud-init.txt 配置文件保存在现有工作目录中，请提供该文件的完整路径。 以下示例创建一个名为 centos74 的 VM：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

创建 VM 后，Azure CLI 会显示部署的特定信息。 记下 `publicIpAddress`。 此地址用于访问 VM。  创建 VM、安装程序包和启动应用需要一些时间。 在 Azure CLI 向你返回提示之后，仍然存在继续运行的后台任务。 你可以使用 SSH 连接到 VM 并使用故障排除部分中所述的步骤来查看 cloud-init 日志。 

## <a name="troubleshooting-cloud-init"></a>对 cloud-init 进行故障排除
VM 预配完成后，会在 `--custom-data` 中定义的所有模块和脚本上运行 cloud-init，以便配置 VM。  若要对配置中存在的任何错误或遗漏进行故障排除，需要在位于 /var/log/cloud-init.log 的 cloud-init 日志中搜索模块名称（例如 `disk_setup` 或 `runcmd`）。

> [!NOTE]
> 并不是每个模块故障都会导致严重的 cloud-init 整体配置故障。 例如使用 `runcmd` 模块，如果脚本发生故障，cloud-init 依然会报告预配成功，因为 runcmd 模块已执行。

有关 cloud-init 日志的更多详细信息，请参阅 [cloud-init 文档](http://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>后续步骤
有关配置更改的 cloud-init 示例，请参阅以下文档：
 
- [向 VM 添加其他 Linux 用户](cloudinit-add-user.md)
- [运行包管理器以在首次启动时更新现有包](cloudinit-update-vm.md)
- [更改 VM 本地主机名](cloudinit-update-vm-hostname.md) 
- [安装应用程序包、更新配置文件和注入密钥](tutorial-automate-vm-deployment.md)
