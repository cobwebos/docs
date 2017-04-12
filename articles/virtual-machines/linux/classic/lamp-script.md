---
title: "在 Linux VM 上使用 CustomScript 扩展 | Microsoft Docs"
description: "了解如何在 Azure 中使用经典部署模型创建的 Linux 虚拟机使用 CustomScript 扩展部署应用程序。"
editor: tysonn
manager: timlt
documentationcenter: 
services: virtual-machines-linux
author: gbowerman
tags: azure-service-management
ms.assetid: e535241d-feca-4412-b07a-67c936ba88a0
ms.service: virtual-machines-linux
ms.workload: multiple
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1c1591079bf09da6fbe50d848b05ec7791657e04
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>使用适用于 Linux 的 Azure CustomScript 扩展部署 LAMP 应用程序
> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 有关使用 Resource Manager 模型部署 LAMP 堆栈的信息，请参阅[此处](../create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

适用于 Linux 的 Microsoft Azure CustomScript 扩展提供了一种方式来通过运行以 VM 支持的任何脚本语言（例如 Python 和 Bash）编写的任意代码来自定义虚拟机 (VM)。 这提供了一种非常灵活的方式来在多台计算机上自动执行应用程序部署。

可以使用 Azure 经典门户、Windows PowerShell 或 Azure 命令行接口 (Azure CLI) 部署 CustomScript 扩展。

本文使用 Azure CLI 将一个简单的 LAMP 应用程序部署到使用经典部署模型创建的 Ubuntu VM。

## <a name="prerequisites"></a>先决条件
在本示例中，请先创建两个运行 Ubuntu 14.04 或更高版本的 Azure VM。 VM 名为 *script-vm* 和 *lamp-vm*。 创建 VM 时请使用唯一名称。 其中一个 VM 用于运行 CLI 命令，另一个用于部署 LAMP 应用。

还可能需要 Azure 存储帐户和密钥（可以从 Azure 经典门户获取此信息）来访问该应用。

在 Azure 上创建 Linux VM 时如需帮助，请参阅[创建运行 Linux 的虚拟机](createportal.md)。

安装命令假设使用的是 Ubuntu，不过，你可以针对任何受支持的 Linux 分发版改动安装。

script-vm VM 需要使用与 Azure 之间的有效连接安装 Azure CLI。 有关这方面的帮助，请参阅 [Install and Configure the Azure Command-Line Interface](../../../cli-install-nodejs.md)（安装和配置 Azure 命令行接口）。

## <a name="upload-a-script"></a>上载脚本
我们将使用 CustomScript 扩展在远程 VM 上运行脚本，以便安装 LAMP 堆栈并创建 PHP 页。 为了能够从任何位置访问该脚本，我们将其上载为 Azure blob。

### <a name="script-overview"></a>脚本概述
脚本示例将 LAMP 堆栈安装到 Ubuntu（包括设置 MySQL 的无提示安装类）、编写简单的 PHP 文件并启动 Apache。

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>上载脚本
将脚本另存为文本文件，例如 *install_lamp.sh*，然后将其上载到 Azure 存储。 你可以使用 Azure CLI 轻松执行此操作。 以下示例将文件上载到名为“scripts”的存储容器。 如果该容器不存在，你需要先创建它。

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

还要创建一个描述如何从 Azure 存储下载脚本的 JSON 文件。 将该文件另存为 *public_config.json*（使用存储帐户的名称替换“mystorage”）：

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>部署扩展
现在，可以在 Azure CLI 中使用下一条命令将 Linux CustomScript 扩展部署到远程 VM。

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

上述命令在名为 *lamp-vm* 的 VM 上下载并运行 *install_lamp.sh* 脚本。

由于该应用包含 Web 服务器，因此请记得使用以下命令在远程 VM 上打开 HTTP 侦听端口。

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>监视和故障排除
可以通过查看远程 VM 上的日志文件来检查自定义脚本的运行情况。 通过 SSH 连接到 *lamp-vm*，使用下一条命令显示日志的尾部。

    cd /var/log/azure/customscript
    tail -f handler.log

运行 CustomScript 扩展后，可以浏览到创建的 PHP 页获取信息。 本文中的示例 PHP 页是 *http://lamp-vm.cloudapp.net/phpinfo.php*。

## <a name="additional-resources"></a>其他资源
你可以使用相同的基本步骤来部署较复杂的应用程序。 在本例中，安装脚本已保存为 Azure 存储中的公共 blob。 比较安全的选择是使用[安全访问签名](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS) 将安装脚本存储为安全 Blob。

下面列出了 Azure CLI、Linux 和 CustomScript 扩展的其他资源。

[使用 CustomScript 扩展自动执行 Linux VM 自定义任务](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Azure Linux 扩展 (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Azure 上的 Linux 和开源计算](../opensource-links.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


