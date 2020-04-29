---
title: 在启用 Python 3 的 Linux Azure 虚拟机系统中使用 VM 扩展时遇到的问题
description: 了解如何在启用 Python 3 的 Linux 系统中使用 VM 扩展
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: 944abc62f25473ea52836af7dc1fdcd1e16d9269
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82120777"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>在启用 Python 3 的 Linux Azure 虚拟机系统中使用 VM 扩展时遇到的问题

> [!NOTE]
> Microsoft 鼓励用户在其系统中采用**python** 3.x，除非你的工作负载需要**python** 2.x 支持。 此要求的示例可能包括旧的管理脚本或**Azure 磁盘加密**等扩展和**Azure Monitor**。
>
> 在生产环境中安装**python** 2.x 之前，请考虑对 Python 2.x 提供长期支持的问题，尤其是他们接收安全更新的能力。 作为产品（包括提到的一些扩展）使用**python 3.8**支持进行更新，你应停止使用 python 2.x。

某些 Linux 分发版已转换为 Python 3.8，并完全`/usr/bin/python`删除了适用于 python 的旧入口点。 此转换会影响特定虚拟机（VM）扩展的现成、自动部署，但存在以下情况：

- 仍过渡到 Python 1.x 支持的扩展
- 使用旧`/usr/bin/python`入口点的扩展

已转换为**Python** 3.x 的 Linux 分发用户必须确保旧`/usr/bin/python`入口点存在，然后再尝试将这些扩展部署到其 vm。 否则，扩展部署可能会失败。 

- 受认可的受影响 Linux 发行版包括**Ubuntu Server 20.04 LTS**和**UBUNTU Pro 20.04 LTS**。

- 受影响的 VM 扩展包括**Azure 磁盘加密**、 **Log Analytics**、 **VM 访问**（用于密码重置）和**来宾诊断**（用于其他性能计数器）。

就地升级（例如从**Ubuntu 18.04 LTS**升级到**ubuntu 20.04 LTS**）应保留`/usr/bin/python`符号，并不受影响。

## <a name="resolution"></a>解决方法

在以下摘要中的前面所述的已知受影响的方案中部署扩展之前，请考虑以下常规建议：

1.  在部署扩展之前，请使用`/usr/bin/python` Linux 分发供应商提供的方法恢复符号链接。

    - 例如，对于**Python 2.7**，使用：`sudo apt update && sudo apt install python-is-python2`

2.  如果已部署出现此问题的实例，请使用 " **VM" 边栏选项卡**中的 "**运行命令**" 功能运行上面提到的命令。 运行命令扩展本身不受到 Python 3.8 的转换影响。

3.  如果要部署新的实例，并需要在预配时设置扩展，请使用**云初始化**用户数据安装上面提到的包。

    例如，对于 Python 2.7：

    ```
    # create cloud-init config
    cat > cloudinitConfig.json <<EOF
    #cloud-config
    package_update: true
    
    runcmd:
    - sudo apt update
    - sudo apt install python-is-python2 
    EOF
    
    # create VM
    az vm create \
        --resource-group <resourceGroupName> \
        --name <vmName> \
        --image <Ubuntu 20.04 Image URN> \
        --admin-username azadmin \
        --ssh-key-value "<sshPubKey>" \
        --custom-data ./cloudinitConfig.json
    ```

4.  如果你的组织的策略管理员确定不应将扩展部署到 Vm 中，你可以在预配时禁用扩展支持：

    - REST API

      若要在你可以部署具有此属性的 VM 时禁用和启用扩展，请执行以下操作：

      ```
        "osProfile": {
          "allowExtensionOperations": false
        },
      ```

## <a name="next-steps"></a>后续步骤

有关其他信息，请参阅[自 18.04 LTS 以来的其他基础系统更改](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default)。
