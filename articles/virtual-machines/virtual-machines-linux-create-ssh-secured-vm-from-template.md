<properties
	pageTitle="使用 Azure 模板创建 Linux VM | Microsoft Azure"
	description="使用 Azure Resource Manager 模板在 Azure 上创建 Linux VM。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/17/2016"
	ms.author="v-livech"/>

# 使用 Azure 模板创建 Linux VM

本文说明如何使用 Azure 模板在 Azure 上快速部署 Linux 虚拟机。本文中的操作需要 Azure 帐户（[获取免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)）和已登录 (`azure login`) 且处于 Resource Manager 模式下 (`azure config mode arm`) 的 [Azure CLI](../xplat-cli-install.md)。也可以使用 [Azure 门户](virtual-machines-linux-quick-create-portal.md)或 [Azure CLI](virtual-machines-linux-quick-create-cli.md) 快速部署 Linux VM。

## 快速命令摘要

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## 详细演练

模板可让你在 Azure 上使用要在启动期间自定义的设置（如用户名和主机名设置）创建 VM。对于本文，我们将利用 Ubuntu VM 和对 SSH 开放了端口 22 的网络安全组 (NSG) 启动 Azure 模板。

Azure Resource Manager 模板是可用于一次性简易任务（启动 Ubuntu VM）的 JSON 文件，如本文所述。Azure 模板还可用于构建整个环境（如测试、开发或生产部署堆栈）的复杂 Azure 配置。

## 创建 Linux VM

以下代码示例演示了如何在使用[此 Azure Resource Manager 模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)的同时，调用 `azure group create` 创建资源组和部署受 SSH 保护的 Linux VM。请记住，在示例中必须使用环境中唯一的名称。本示例使用 `quicksecuretemplate` 作为资源组名称，使用 `securelinux` 作为 VM 名称，使用 `quicksecurelinux` 作为子域名称。

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

输出

```bash
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== vlivech@azure
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

该示例使用 `--template-uri` 参数部署了一台 VM。还可以使用以模板文件路径作为参数的 `--template-file` 参数在本地下载或创建模板并传递模板。Azure CLI 将提示你输入模板所需的参数。

## 后续步骤

请搜索[模板库](https://azure.microsoft.com/documentation/templates/)，了解接下来要部署哪些应用框架。

<!---HONumber=AcomDC_0921_2016-->