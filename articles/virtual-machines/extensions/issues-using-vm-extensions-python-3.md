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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82120777"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>在启用 Python 3 的 Linux Azure 虚拟机系统中使用 VM 扩展时遇到的问题

> [!NOTE]
> Microsoft 鼓励用户在其系统中采用**python** 3.x，除非你的工作负载需要**python** 2.x 支持。 此要求的示例可能包括旧版管理脚本或扩展，如 Azure 磁盘加密和 Azure Monitor 。
>
> 在生产环境中安装 Python 2.x 之前，请考虑 Python 2.x 的长期支持问题，尤其是它们接收安全更新的能力。 由于产品（包括上述一些扩展）使用 Python 3.8 支持进行更新，应停止使用 Python 2.x。

某些 Linux 发行版已转换为 Python 3.8，并完全删除了 Python 的旧版 `/usr/bin/python` 入口点。 此转换会影响具备以下条件的某些虚拟机 (VM) 扩展的开箱即用、自动部署：

- 仍在转换为 Python 3.x 支持的扩展
- 使用旧版 `/usr/bin/python` 入口点的扩展

已转换为 Python 3.x 的 Linux 发行版用户在尝试将这些扩展部署到自己的 VM 之前，必须确保存在旧版 `/usr/bin/python` 入口点。 否则，扩展部署可能会失败。 

- 受影响的认可 Linux 发行版包括 Ubuntu Server 20.04 LTS 和 Ubuntu Pro 20.04 LTS 。

- 受影响的 VM 扩展包括 Azure 磁盘加密、Log Analytics、VM 访问（用于密码重置）和来宾诊断（用于其他性能计数器）   。

就地升级（如从 Ubuntu 18.04 LTS 升级到 Ubuntu 20.04 LTS）应保留 `/usr/bin/python` 符号链接，并保持不受影响 。

## <a name="resolution"></a>解决方法

在上文摘要中介绍的已知受影响的场景中部署扩展之前，请考虑以下常规建议：

1.  部署扩展之前，请使用 Linux 发行版供应商提供的方法恢复 `/usr/bin/python` 符号链接。

    - 例如，对于 Python 2.7，请使用 `sudo apt update && sudo apt install python-is-python2`

2.  如果已部署出现此问题的实例，请使用 " **VM" 边栏选项卡**中的 "**运行命令**" 功能运行上面提到的命令。 运行命令扩展本身不受向 Python 3.8 的转换的影响。

3.  如果要部署新实例，并需要在预配时设置扩展，请使用 cloud-init 用户数据来安装上述程序包。

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

      在可以使用此属性部署 VM 时禁用和启用扩展：

      ```
        "osProfile": {
          "allowExtensionOperations": false
        },
      ```

## <a name="next-steps"></a>后续步骤

请参阅[默认情况下自 18.04 LTS - Python 3 起其他基础的系统更改](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default)获取更多信息。
