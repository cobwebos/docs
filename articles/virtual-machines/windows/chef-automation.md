---
title: 使用 Chef 部署 Azure 虚拟机 | Microsoft Docs
description: 了解如何使用 Chef 在 Microsoft Azure 中自动执行虚拟机的部署和配置
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 5cbf53da5a0af0a511350b9f30153e2fefe72dcf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080074"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>使用 Chef 自动部署 Azure 虚拟机

Chef 是一个强大的工具，用于提供自动化和所需的状态配置。

使用最新的云 API 版本，Chef 提供了与 Azure 的无缝集成，使得你能够通过单个命令预配和部署配置状态。

本文介绍如何设置 Chef 环境以预配 Azure 虚拟机，并逐步讲解如何创建一个策略或“食谱”，然后将此食谱部署到 Azure 虚拟机。

## <a name="chef-basics"></a>Chef 基础知识
在开始之前，请[复习一下 Chef 的基本概念](https://www.chef.io/chef)。

下图描绘了概要的 Chef 体系结构。

![][2]

Chef 有三个主要的体系结构组件：Chef 服务器、Chef 客户端（节点）和 Chef 工作站。

Chef 服务器是管理点，对于 Chef 服务器有两种选择：托管解决方案和本地解决方案。

Chef 客户端（节点）是位于在管理的服务器上的代理。

Chef 工作站是在其中创建策略和执行管理命令与 Chef 工具软件包的管理工作站的名称。

一般情况下，可将自己的工作站视为在其中执行操作的位置，以及用于运行软件包的 Chef 工作站。
例如，你要在 Chef 工作站中下载 knife 命令，但要从自己的工作站运行 knife 命令来管理基础结构。

Chef 还使用“食谱”和“配方”的概念，它们实际上是我们要定义并应用到服务器的策略。

## <a name="preparing-your-workstation"></a>准备工作站

首先，通过创建一个用于存储 Chef 配置文件和食谱的目录来准备工作站。

创建名为 C:\Chef 的目录。

下载最新的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 版本，并将其安装到工作站上。

## <a name="configure-azure-service-principal"></a>配置 Azure 服务主体

用最简单的术语来说，Azure 服务主体就是一个服务帐户。   我们将使用一个服务主体来帮助我们从 Chef 工作站创建 Azure 资源。  若要创建具有所需权限的相关服务主体，我们需要在 PowerShell 中运行以下命令：
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

请记下 SubscriptionID、TenantID、ClientID 和客户端密码（前面设置的密码），稍后将需要这些项。 

## <a name="setup-chef-server"></a>设置 Chef 服务器

本指南假设你要注册托管 Chef。

如果尚未使用 Chef 服务器，可以：

* 注册[托管 Chef](https://manage.chef.io/signup)，这是 Chef 的最快入门方法。
* 遵照 [Chef 文档](https://docs.chef.io/)中的[安装说明](https://docs.chef.io/install_server.html)，在基于 Linux 的计算机上安装独立的 Chef 服务器。

### <a name="creating-a-hosted-chef-account"></a>创建托管 Chef 帐户

在[此处](https://manage.chef.io/signup)注册托管 Chef 帐户。

在注册过程中，系统会要求创建新的组织。

![][3]

在创建组织后，下载初学者工具包。

![][4]

> [!NOTE]
> 如果收到提示，警告将重置密钥，可以继续操作，因为我们尚未配置现有基础结构。
>

此初学者工具包 zip 文件的 `.chef` 目录中包含组织配置文件和用户密钥。

必须单独下载 `organization-validator.pem`，因为它是一个私钥，而私钥不应存储在 Chef 服务器上。 在 [Chef 管理](https://manage.chef.io/)中，进入“管理”部分并选择“重置验证密钥”，此时会提供一个文件供你单独下载。 将该文件保存到 c:\chef。

### <a name="configuring-your-chef-workstation"></a>配置 Chef 工作站

将 chef-starter.zip 的内容解压缩到 c:\chef。

将 chef-starter\chef-repo\.chef 下的所有文件都复制到 c:\chef 目录中。

如果 `organization-validator.pem` 文件保存在 c:\Downloads 中，请将它复制到 c:\chef

现在目录看起来应当与以下示例类似。

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

现在，c:\chef 的根目录中应有 5 个文件和 4 个目录（包括 chef-repo 空目录）。

### <a name="edit-kniferb"></a>编辑 knife.rb

PEM 文件包含用于通信的组织私钥和管理私钥，knife.rb 文件包含 knife 配置。 需要编辑 knife.rb 文件。

在所选的编辑器中打开 knife.rb 文件。 未经更改的文件应如下所示：

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

将以下信息添加到 knife.rb：

validation_client_name   "myorg-validator"

validation_key           "#{current_dir}/myorg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


这些行将确保 Knife 引用 c:\chef\cookbooks 下的 cookbooks 目录，并且还使用你在 Azure 操作期间创建的 Azure 服务主体。

knife.rb 文件现在应类似于以下示例：

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>安装 Chef 工作站

接下来，[下载并安装](https://downloads.chef.io/chef-workstation/) Chef 工作站。
将 Chef 工作站安装在默认位置。 安装可能需要几分钟时间。

桌面上会显示“CW PowerShell”，这是使用与 Chef 产品交互时所需的工具加载的环境。 在 CW PowerShell 中可以使用新的临时命令（例如 `chef-run`），以及传统的 Chef CLI 命令（例如 `chef`）。 使用 `chef -v` 查看安装的 Chef 工作站和 Chef 工具版本。 也可以通过在 Chef 工作站应用中选择“关于 Chef 工作站”来检查工作站版本。

`chef --version` 应返回如下所示的输出：

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> 该路径的顺序非常重要！ 如果 opscode 路径未采用正确的顺序，会遇到问题。
>

在继续操作之前重新启动工作站。

### <a name="install-knife-azure"></a>安装 Knife Azure

本教程假设使用 Azure 资源管理器来与虚拟机交互。

安装 Knife Azure 扩展。 这会为 Knife 提供“Azure 插件”。

运行以下命令。

    chef gem install knife-azure ––pre

> [!NOTE]
> –pre 参数确保收到 Knife Azure 插件的最新 RC 版本，该版本提供对最新 API 集的访问。
>
>

同时，可能还会安装许多依赖项。

![][8]

若要确保所有项都已正确配置，请运行以下命令。

    knife azurerm server list

如果所有项都已正确配置，用户可看到可用 Azure 映像的列表滚动显示。

祝贺。 现已设置好工作站！

## <a name="creating-a-cookbook"></a>创建食谱

食谱由 Chef 用来定义希望在托管客户端上执行的一组命令。 创建食谱的过程十分直截了当，只需使用 **chef generate cookbook** 命令生成食谱模板即可。 此食谱适用于自动部署 IIS 的 Web 服务器。

在 C:\Chef 目录下运行以下命令。

    chef generate cookbook webserver

此命令在 C:\Chef\cookbooks\webserver 目录下生成一组文件。 接下来，定义 Chef 客户端要在托管虚拟机上执行的命令集。

这些命令存储在文件 default.rb 中。 在此文件中，定义一组命令用于安装 IIS、启动 IIS 并将模板文件复制到 wwwroot 文件夹中。

修改 C:\chef\cookbooks\webserver\recipes\default.rb 文件并添加以下行。

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

在完成后，保存该文件。

## <a name="creating-a-template"></a>创建模板
此步骤生成用作 default.html 页面的模板文件。

运行以下命令来生成模板：

    chef generate template webserver Default.htm

导航到 `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` 文件。 通过添加一些简单的“Hello World”html 代码来编辑该文件，并保存该文件。

## <a name="upload-the-cookbook-to-the-chef-server"></a>将食谱上传到 Chef 服务器
此步骤生成在本地计算机上创建的食谱的副本，并将其上传到 Chef 托管服务器。 上传后，食谱将显示在“策略”选项卡下。

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>使用 Knife Azure 部署虚拟机
部署 Azure 虚拟机，并应用“Webserver”食谱来安装 IIS Web 服务和默认网页。

若要执行此操作，请使用 **knife azurerm server create** 命令。

下面显示了命令示例。

    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"


上面的示例将创建一个 Standard_DS2_v2 虚拟机, 并在美国西部区域中安装 Windows Server 2016。 替换特定变量并运行。

> [!NOTE]
> 通过命令行，还使用 –tcp-endpoints 参数自动执行终结点网络筛选规则。 我已打开端口80和 3389, 以提供对网页和 RDP 会话的访问。
>
>

运行该命令后，请转到 Azure 门户，此时会看到计算机已开始预配。

![][15]

接下来显示命令提示符。

![][16]

部署完成后，新虚拟机的公共 IP 地址将在部署完成时显示，你可以复制此地址并将其粘贴到 Web 浏览器中，然后查看你部署的网站。 我们部署虚拟机时，打开了端口 80，因此它应该在外部可用。   

![][11]

此示例使用了富有创造性的 HTML 代码。

你还可以查看节点的状态 [Chef 管理](https://manage.chef.io/)。 

![][17]

请记住，也可以在 Azure 门户中使用端口 3389 通过 RDP 会话进行连接。

谢谢！ 现在就使用 Azure 开始基础结构即代码之旅吧！

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->
