---
title: "使用 SCP 将文件移到 Azure Linux VM 和从 Azure Linux VM 移动文件 | Microsoft Docs"
description: "使用 SCP 和 SSH 密钥对安全地将文件移到 Azure Linux VM 和从 Azure Linux VM 移动文件。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: danlep
ms.openlocfilehash: 736f7c11ec3de04f1ad52ee29d0a4c952c9b0545
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>使用 SCP 将文件移到 Linux VM 和从 Linux VM 移动文件

本文说明如何使用安全复制 (SCP) 将文件从工作站向上移到 Azure Linux VM，或从 Azure Linux VM 向下移到工作站。 在工作站和 Linux VM 之间快速安全地移动文件是管理 Azure 基础结构的关键。 

对于本文，需要使用 [SSH 公钥和私钥文件](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)在 Azure 中部署的 Linux VM。 还需要用于本地计算机的 SCP 客户端。 它基于 SSH 生成，包括在大多数 Linux 和 Mac 计算机以及部分 Windows Shell 的默认 Bash Shell 中。

## <a name="quick-commands"></a>快速命令

将文件向上复制到 Linux VM

```bash
scp file azureuser@azurehost:directory/targetfile
```

从 Linux VM 向下复制文件

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>详细演练

作为示例，我们将 Azure 配置文件向上移到 Linux VM，并向下拉取日志文件目录，这两项操作都使用 SCP 和 SSH 密钥完成。   

## <a name="ssh-key-pair-authentication"></a>SSH 密钥对身份验证

SCP 将 SSH 用于传输层。 SSH 处理目标主机上的身份验证，同时还在 SSH 默认提供的加密隧道中移动文件。 对于 SSH 身份验证，可以使用用户名和密码。 但是，建议将 SSH 公钥和私钥身份验证作为安全性最佳做法。 SSH 对连接进行身份验证后，SCP 将开始复制文件。 借助正确配置的 `~/.ssh/config` 以及 SSH 公钥和私钥，仅使用服务器名称（或 IP 地址）即可建立 SCP 连接。 如果只有一个 SSH 密钥，SCP 会在 `~/.ssh/` 目录中查找它，并在默认情况下使用它登录到 VM。

有关配置 `~/.ssh/config` 以及 SSH 公钥和私钥的详细信息，请参阅[创建 SSH 密钥](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="scp-a-file-to-a-linux-vm"></a>通过 SCP 将文件复制到 Linux VM

在第一个示例中，我们将 Azure 配置文件向上复制到用于部署自动化的 Linux VM。 由于此文件包含 Azure API 凭据，其中包括机密，因此安全性非常重要。 SSH 提供的加密隧道可保护文件的内容。

以下命令通过 FQDN myserver.eastus.cloudapp.azure.com 将本地 .azure/config 文件复制到 Azure VM。Azure VM 上的管理员用户名为 azureuser。 该文件指向 /home/azureuser/directory。 在此命令中替换自己的值。

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>通过 SCP 从 Linux VM 复制目录

在此示例中，我们将日志文件的目录从 Linux VM 复制到工作站。 日志文件可能或可能不包含敏感或机密数据。 但是，使用 SCP 可确保加密日志文件内容。 使用 SCP 传输文件是将日志目录和文件获取到工作站上同时确保安全的最简单方法。

以下命令可将 Azure VM 上位于 /home/azureuser/log/ 目录中的文件复制到本地 /tmp 目录中：

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

`-r` cli 标志指示 SCP 从命令中列出目录的时点起以递归方式复制文件和目录。  另请注意，命令行语法类似于 `cp` 复制命令。

## <a name="next-steps"></a>后续步骤

* [管理用户、SSH，并使用 VMAccess 扩展检查或修复 Azure Linux VM 上的磁盘](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)