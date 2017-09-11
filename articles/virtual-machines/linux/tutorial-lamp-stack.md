---
title: "在 Azure 中的 Linux 虚拟机上部署 LAMP | Microsoft 文档"
description: "教程 - 在 Azure 中的 Linux VM上安装 LAMP 堆栈"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 9148ac9646e4e1cfeff8f20c096e390499437e78
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="install-a-lamp-web-server-on-an-azure-vm"></a>在 Azure VM 上安装 LAMP Web 服务器
本文逐步讲解如何在 Azure 中的 Ubuntu VM 上部署 Apache Web 服务器、MySQL 和 PHP（LAMP 堆栈）。 如果想要部署 NGINX Web 服务器，请参阅 [LEMP 堆栈](tutorial-lemp-stack.md)教程。 若要了解 LAMP 服务器的运作情况，可以选择性地安装并配置 WordPress 站点。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 Ubuntu VM（LAMP 堆栈中的“L”）
> * 为 Web 流量打开端口 80
> * 安装 Apache、MySQL 和 PHP
> * 验证安装和配置
> * 在 LAMP 服务器上安装 WordPress


有关 LAMP 堆栈的详细信息，包括针对生产环境的建议，请参阅 [Ubuntu 文档](https://help.ubuntu.com/community/ApacheMySQLPHP)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>安装 Apache、MySQL 和 PHP

运行以下命令更新 Ubuntu 包源并安装 Apache、MySQL 和 PHP。 请注意命令末尾的插入符号 (^)。


```bash
sudo apt update && sudo apt install lamp-server^
```



系统会提示安装包和其他依赖项。 出现提示时，请为 MySQL 设置 root 密码，并按 [Enter] 继续。 遵照剩余的提示操作。 此股从会安装最低要求的 PHP 扩展，这些扩展是通过 MySQL 使用 PHP 所必需的。 

![MySQL root 密码页][1]

## <a name="verify-installation-and-configuration"></a>验证安装和配置


### <a name="apache"></a>Apache

使用以下命令检查 Apache 版本：
```bash
apache2 -v
```

安装 Apache 并向 VM 打开端口 80 之后，即可通过 Internet 访问 Web 服务器。 若要查看 Apache2 Ubuntu 默认页，请打开 Web 浏览器并输入 VM 的公共 IP 地址。 使用通过 SSH 连接到 VM 时所用的公共 IP 地址：

![Apache 默认页][3]


### <a name="mysql"></a>MySQL

使用以下命令检查 MySQL 版本（请注意大写的 `V` 参数）：

```bash
msql -V
```

建议运行以下脚本来帮助保护 MySQL 的安装：

```bash
mysql_secure_installation
```

输入 MySQL root 密码，并配置环境的安全设置。

如果想要创建 MySQL 数据库，请添加用户或更改配置设置，并登录到 MySQL：

```bash
mysql -u root -p
```

完成后，键入 `\q` 退出 mysql 提示符。

### <a name="php"></a>PHP

使用以下命令检查 PHP 版本：

```bash
php -v
```

如果想要进一步测试，请创建一个可在浏览器中查看的快速 PHP 信息页。 以下命令创建 PHP 信息页：

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

现在，可以检查创建的 PHP 信息页。 打开浏览器并转到 `http://yourPublicIPAddress/info.php`。 替换 VM 的公共 IP 地址。 应如下图所示。

![PHP 信息页][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]


## <a name="next-steps"></a>后续步骤

本教程在 Azure 中部署了一台 LAMP 服务器。 你已了解如何：

> [!div class="checklist"]
> * 创建 Ubuntu VM
> * 为 Web 流量打开端口 80
> * 安装 Apache、MySQL 和 PHP
> * 验证安装和配置
> * 在 LAMP 服务器上安装 WordPress

转到下一教程，了解如何使用 SSL 证书保护 Web 服务器。

> [!div class="nextstepaction"]
> [使用 SSL 保护 Web 服务器](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lamp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
