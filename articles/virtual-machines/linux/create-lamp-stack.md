---
title: "在 Azure 中的 Linux 虚拟机上部署 LAMP | Microsoft 文档"
description: "了解如何在 Azure 中的 Linux VM 上安装 LAMP 堆栈"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jluk
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: NA
ms.topic: article
ms.date: 2/21/2017
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 174490bec1aea20abf2c2bd465c7aa2c8590e3e2
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-lamp-stack-on-azure"></a>在 Azure 上部署 LAMP 堆栈
本文介绍如何在 Azure 上部署 Apache web 服务器、MySQL 和 PHP（LAMP 堆栈）。 用户需要 Azure 帐户（[获取免费试用版](https://azure.microsoft.com/pricing/free-trial/)）和 [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)。 还可以使用 [Azure CLI 1.0](create-lamp-stack-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 执行这些步骤。

## <a name="quick-command-summary"></a>快速命令摘要

1. 在本地计算机上根据需要保存和编辑 [azuredeploy.parameters.json 文件](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json)。
2. 通过运行以下两个命令创建资源组，然后部署模板：

```azurecli
az group create -l westus -n myResourceGroup
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

### <a name="deploy-lamp-on-existing-vm"></a>在现有 VM 上部署 LAMP
以下命令更新包，然后安装 Apache、MySQL 和 PHP：

```bash
sudo apt-get update
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>在新的 VM 上部署 LAMP 的演练

1. 使用 [az group create](/cli/azure/group#create) 创建资源组，使之包含新的 VM：

```azurecli
az group create -l westus -n myResourceGroup
```
若要创建 VM 本身，可以使用在 [GitHub 上的此处](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)找到的已编写好的 Azure Resource Manager 模板。

2. 在本地计算机上保存 [azuredeploy.parameters.json 文件](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json)。
3. 将 **azuredeploy.parameters.json** 文件编辑成首选的输入。
4. 使用 [az group deployment create] 部署模板，引用下载的 json 文件：

```azurecli
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

输出类似于以下示例：

```json
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Resources/deployments/azuredeploy",
"name": "azuredeploy",
"properties": {
    "correlationId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "debugSetting": null,
}
...
"provisioningState": "Succeeded",
"template": null,
"templateLink": {
    "contentVersion": "1.0.0.0",
    "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json"
    },
    "timestamp": "2017-02-22T00:05:51.860411+00:00"
},
"resourceGroup": "myResourceGroup"
}
```

现在你已创建已安装 LAMP 的 Linux VM。 可以根据需要跳转到[验证是否已成功安装 LAMP](#verify-lamp-successfully-installed) 来验证此安装。

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>在现有 VM 上部署 LAMP 的演练
如果需要有关创建 Linux VM 方面的帮助，可以转到[此处了解如何创建 Linux VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-cli)。 接下来，需通过 SSH 登录 Linux VM。 如果需要有关创建 SSH 密钥方面的帮助，可以转到[此处了解如何在 Linux/Mac 上创建 SSH 密钥](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
如果已有 SSH 密钥，请继续操作，使用 `ssh azureuser@mypublicdns.westus.cloudapp.azure.com` 从命令行通过 SSH 登录 Linux VM。

现在你是在 Linux VM 中操作，我们可以指导你在基于 Debian 的分发版上安装 LAMP 堆栈。 对于其他 Linux 分发版，确切的命令可能会有所不同。

#### <a name="installing-on-debianubuntu"></a>在 Debian/Ubuntu 上安装
需要安装以下程序包：`apache2`、`mysql-server`、`php5`、`php5-mysql`。 可以直接使用这些包来安装，也可以使用 Tasksel 来安装。
在安装之前，需要下载并更新包列表。

```bash
sudo apt-get update
```

##### <a name="individual-packages"></a>单个包
使用 apt-get：

```bash
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

##### <a name="using-tasksel"></a>使用 tasksel
此外，你可以下载 Tasksel，它是一个 Debian/Ubuntu 工具，可将多个相关包作为协同“任务”安装到你的系统中。

```bash
sudo apt-get install tasksel
sudo tasksel install lamp-server
```

运行上述任一选项以后，系统会提示用户安装这些包以及各种其他的依赖项。 若要为 MySQL 设置一个管理密码，可按“y”再按“Enter”继续，然后再遵循任何其他的提示进行操作。 此股从会安装最低要求的 PHP 扩展，这些扩展是通过 MySQL 使用 PHP 所必需的。 

![][1]

运行以下命令即可查看以程序包形式提供的其他 PHP 扩展：

```bash
apt-cache search php5
```

#### <a name="create-infophp-document"></a>创建 info.php 文档
现在应可以通过在命令行中键入 `apache2 -v`、`mysql -v` 或 `php -v` 来查看 Apache、MySQL 和 PHP 的版本。

如果你想要进行进一步的检测，可以创建在浏览器中查看的快速 PHP 信息页。 通过以下命令使用 Nano 文本编辑器创建一个文件：

```bash
sudo nano /var/www/html/info.php
```

在 GNU Nano 文本编辑器中，添加以下行：

```php
<?php
phpinfo();
?>
```

然后保存并退出文本编辑器。

使用此命令重启 Apache，以便所有新安装都生效。

```bash
sudo service apache2 restart
```

## <a name="verify-lamp-successfully-installed"></a>验证是否已成功安装 LAMP
现在，用户可以查看所创建的 PHP 信息页，只需打开浏览器并转到 http://youruniqueDNS/info.php 即可。 应如下图所示。

![][2]

可以通过转到 http://youruniqueDNS/ 查看 Apache2 Ubuntu 默认页来检查 Apache 安装。 输出类似于以下示例：

![][3]

恭喜，你刚刚在 Azure VM 上安装了 LAMP 堆栈！

## <a name="next-steps"></a>后续步骤
签出 LAMP 堆栈上的 Ubuntu 文档：

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/deploy-lamp-stack/apachesuccesspage.png

