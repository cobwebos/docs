---
title: 部署 Azure 容器服务群集 | Microsoft Docs
description: 使用 Azure 门户、Azure CLI 或 PowerShell 部署 Azure 容器服务群集。
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Docker, 容器, 微服务, Mesos, Azure

ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle

---
# 部署 Azure 容器服务群集
在 Azure 容器服务中，可以快速部署流行的开源容器群集和协调解决方案。使用 Azure 容器服务，可以通过 Azure Resource Manager 模板或 Azure 门户部署 DC/OS 和 Docker Swarm 群集。使用 Azure 虚拟机缩放集部署这些群集，使其可以利用 Azure 网络功能和存储产品。若要访问 Azure 容器服务，需有一个 Azure 订阅。如果没有订阅，可以注册[免费试用版](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。

本文档逐步讲解如何使用 [Azure 门户](#creating-a-service-using-the-azure-portal)、[Azure 命令行接口 (CLI)](#creating-a-service-using-the-azure-cli) 和 [Azure PowerShell 模块](#creating-a-service-using-powershell)部署 Azure 容器服务群集。

## 使用 Azure 门户创建服务
登录到 Azure 门户，选择“新建”，然后在 Azure 应用商店中搜索“Azure 容器服务”。

![创建部署 1](media/acs-portal1.png) <br />

选择“Azure 容器服务”，然后单击“创建”。

![创建部署 2](media/acs-portal2.png) <br />

输入以下信息：

* **用户名**：这是 Azure 容器服务群集中每个虚拟机和虚拟机缩放集上使用的帐户的用户名。
* **订阅**：选择一个 Azure 订阅。
* **资源组**：选择现有资源组或创建新资源组。
* **位置**：选择 Azure 容器服务部署的 Azure 区域。
* **SSH 公钥**：添加用于对 Azure 容器服务虚拟机进行身份验证的公钥。请务必不要在此密钥中包含换行符，并且必须包含“ssh-rsa”前缀和“username@domain”后缀。公钥应如下所示：**ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**。有关创建安全外壳 (SSH) 密钥的指导，请参阅 [Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) 和 [Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/) 文章。

准备好继续下一步时，请单击“确定”。

![创建部署 3](media/acs-portal3.png) <br />

选择“协调”类型。选项包括：

* **DC/OS**：部署 DC/OS 群集。
* **Swarm**：部署 Docker Swarm 群集。

准备好继续下一步时，请单击“确定”。

![创建部署 4](media/acs-portal4.png) <br />

输入以下信息：

* **主机计数**：群集中主机的数目。
* **代理计数**：对于 Docker Swarm，这是代理缩放集中的初始代理数目。对于 DC/OS，这是专用缩放集中的初始代理数目。此外，将创建包含预先确定代理数目的公共缩放集。此公共缩放集中的代理数目由群集中创建的主机数目确定：一个主机需要一个公共代理，三或五个主机需要两个公共代理。
* **代理虚拟机大小**：代理虚拟机的大小。
* **DNS 前缀**：全球唯一的名称，用作服务完全限定域名关键部分的前缀。

准备好继续下一步时，请单击“确定”。

![创建部署 5](media/acs-portal5.png) <br />

服务验证完成后，请单击“确定”。

![创建部署 6](media/acs-portal6.png) <br />

单击“创建”启动部署过程。

![创建部署 7](media/acs-portal7.png) <br />

如果已选择将部署固定到 Azure 门户，可以看到部署状态。

![创建部署 8](media/acs-portal8.png) <br />

部署完成后，Azure 容器服务群集可供使用。

## 使用 Azure CLI 创建服务
若要使用命令行创建 Azure 容器服务的实例，需有一个 Azure 订阅。如果没有订阅，可以注册[免费试用版](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。还需要[安装](../xplat-cli-install.md)并[配置](../xplat-cli-connect.md) Azure CLI。

若要部署 DC/OS 或 Docker Swarm 群集，请从 GitHub 中选择以下模板之一。请注意，除了默认协调器选项有所不同外，以下两个模板完全相同。

* [DC/OS 模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm 模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

接下来，请确保 Azure CLI 已连接到 Azure 订阅。可以使用以下命令来执行此操作：

```bash
azure account show
```
如果未返回 Azure 帐户，请使用以下命令将 CLI 登录到 Azure。

```bash
azure login -u user@domain.com
```

接下来，将 Azure CLI 工具配置为使用 Azure Resource Manager。

```bash
azure config mode arm
```

使用以下命令创建 Azure 资源组和容器服务群集，其中：

* **RESOURCE\_GROUP** 是要为此服务使用的资源组名称。
* **LOCATION** 是要创建资源组和 Azure 容器服务部署的 Azure 区域。
* **TEMPLATE\_URI** 是部署文件的位置。请注意，这必须是 RAW 文件，而不是指向 GitHub UI 的指针。若要查找此 URL，请在 GitHub 中选择 azuredeploy.json 文件，然后单击“原始”按钮。

> [!NOTE]
> 运行此命令时，外壳会提示输入部署参数值。
> 
> 

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### 提供模板参数
此版本的命令需要以交互方式定义参数。若要提供参数（例如 JSON 格式的字符串），可以使用 `-p` 开关来提供。例如：

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

或者，也可以使用 `-e` 开关来提供 JSON 格式的参数文件：

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

若要查看名为 `azuredeploy.parameters.json` 的示例参数文件，请在 GitHub 中查找包含 Azure 容器服务模板的文件。

## 使用 PowerShell 创建服务
也可以使用 PowerShell 部署 Azure 容器服务群集。本文档基于 1.0 版 [Azure PowerShell 模块](https://azure.microsoft.com/blog/azps-1-0/)。

若要部署 DC/OS 或 Docker Swarm 群集，请选择以下模板之一。请注意，除了默认协调器选项有所不同外，以下两个模板完全相同。

* [DC/OS 模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm 模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

在 Azure 订阅中创建群集之前，请确认 PowerShell 会话已登录到 Azure。为此，可以使用 `Get-AzureRMSubscription` 命令：

```powershell
Get-AzureRmSubscription
```

如果需要登录到 Azure，请使用 `Login-AzureRMAccount` 命令：

```powershell
Login-AzureRmAccount
```

如果要部署到新资源组，必须先创建该资源组。若要创建新资源组，请使用 `New-AzureRmResourceGroup` 命令，并指定资源组名称和目标区域：

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

创建资源组后，可以使用以下命令创建群集。将为 `-TemplateUri` 参数指定所需模板的 URI。运行此命令时，PowerShell 会提示输入部署参数值。

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### 提供模板参数
如果熟悉 PowerShell 的话，就知道可以通过键入减号 (-) 并按 TAB 键来切换 cmdlet 的可用参数。对于模板中定义的参数，同样也可以使用此功能。只要键入模板名称，该 cmdlet 就会提取该模板、分析参数并将模板参数动态添加到该命令。这使指定模板参数值变得非常轻松。而且，如果你忘记了必需的参数值，PowerShell 会提示你输入该值。

下面是包含参数的完整命令。对于资源的名称，你可以提供自己的值。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## 后续步骤
创建一个正常运行的群集后，请参阅以下文档了解有关连接和管理的详细信息：

* [Connect to an Azure Container Service cluster](container-service-connect.md)（连接到 Azure 容器服务群集）
* [Work with Azure Container Service and DC/OS](container-service-mesos-marathon-rest.md)（使用 Azure 容器服务和 DC/OS）
* [Work with Azure Container Service and Docker Swarm](container-service-docker-swarm.md)（使用 Azure 容器服务和 Docker Swarm）

<!---HONumber=AcomDC_0921_2016-->