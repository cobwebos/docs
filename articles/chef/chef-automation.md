---
title: 快速入门 - 使用 Chef 在 Azure 中配置 Windows 虚拟机
description: 本快速入门介绍如何使用 Chef 在 Azure 中部署和配置 Windows 虚拟机
keywords: chef, azure, devops, 虚拟机
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589491"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>快速入门 - 使用 Chef 在 Azure 中配置 Windows 虚拟机

使用 Chef 可以提供自动化和所需状态配置。

使用最新的云 API 版本，Chef 提供了与 Azure 的无缝集成，使得你能够通过单个命令预配和部署配置状态。

本文介绍如何设置 Chef 环境以预配 Azure 虚拟机，并逐步讲解如何创建一个策略或“食谱”，然后将此食谱部署到 Azure 虚拟机。

## <a name="chef-basics"></a>Chef 基础知识

在开始阅读本文之前，请[查看 Chef 的基本概念](https://www.chef.io/chef)。

下图显示了概要的 Chef 体系结构。

![Chef 体系结构](media/chef-automation/chef-architecure.png)

Chef 有三个主要的体系结构组件： 
- Chef 服务器 - 充当管理点。对于 Chef 服务器有两个选项：托管解决方案和本地解决方案。
- Chef 客户端（节点）- 位于在管理的服务器上的代理。
- Chef 工作站 - 管理工作站（在其中创建策略和运行管理命令）和 Chef 工具软件包的名称。

一般情况下，可将**自己的工作站**视为运行命令的位置，以及软件包的 **Chef 工作站**。

例如，你要在 **Chef 工作站**中下载 knife 命令，但要从**自己的工作站**运行 knife 命令来管理基础结构。

Chef 还使用“食谱”和“配方”的概念。   这些术语分别是定义的并应用到服务器的策略。

## <a name="preparing-your-workstation"></a>准备工作站

首先，通过创建一个用于存储 Chef 配置文件和食谱的目录来准备工作站。

创建名为 `C:\Chef` 的目录。

在工作站上下载并安装最新的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 版本。

## <a name="configure-azure-service-principal"></a>配置 Azure 服务主体

我们将在 Chef 工作站中借助一个服务主体来创建 Azure 资源。  若要创建拥有所需权限的相关服务主体，请在 PowerShell 中运行以下命令：
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

记下 SubscriptionID、TenantID、ClientID 和客户端机密（前面在本教程中设置的密码），因为稍后需要用到这些值。 

## <a name="setup-chef-server"></a>设置 Chef 服务器

本指南假设你要注册托管 Chef。

如果尚未使用 Chef 服务器，可以：

* 注册[托管 Chef](https://manage.chef.io/signup)，这是 Chef 的最快入门方法。
* 遵照 [Chef 文档](https://docs.chef.io/)中的[安装说明](https://docs.chef.io/install_server.html)，在基于 Linux 的计算机上安装独立的 Chef 服务器。

### <a name="creating-a-hosted-chef-account"></a>创建托管 Chef 帐户

在[此处](https://manage.chef.io/signup)注册托管 Chef 帐户。

在注册过程中，系统会要求创建新的组织。

![“创建组织”窗口](media/chef-automation/create-organization.png)

在创建组织后，下载初学者工具包。

![配置 Chef](media/chef-automation/configure-chef.png)

> [!NOTE]
> 如果收到提示，警告将重置密钥，可以继续操作，因为我们尚未配置现有基础结构。
>

此初学者工具包 zip 文件的 `.chef` 目录中包含组织配置文件和用户密钥。

必须单独下载 `organization-validator.pem`，因为它是一个私钥，而私钥不应存储在 Chef 服务器上。 在 [Chef 管理](https://manage.chef.io/)中转到“管理”部分，选择“重置验证密钥”，此时会提供一个可单独下载的文件。 将该文件保存到 c:\chef。

### <a name="configuring-your-chef-workstation"></a>配置 Chef 工作站

将 `chef-starter.zip` 的内容提取到 `c:\chef`。

将 `chef-starter\chef-repo\.chef` 下的所有文件复制到 `c:\chef` 目录。

如果 `organization-validator.pem` 保存在 `c:\Downloads` 中，请将它复制到 `c:\chef`。

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

将以下信息添加到 knife.rb（请将占位符替换为自己的信息）：

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

这些代码行确保 Knife 引用 `c:\chef\cookbooks` 下的 cookbooks 目录。

现在，`knife.rb` 文件现在应类似于以下示例：

![Knife 文件示例](./media/chef-automation/knife-file-example.png)

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

接下来，[下载并安装 Chef 工作站](https://downloads.chef.io/chef-workstation/)。

将 Chef 工作站安装到默认位置。

在桌面上会看到 CW PowerShell。 此工具用来与 Chef 产品交互。 在 CW PowerShell 中可以使用新命令（例如 `chef-run`）和 Chef CLI 命令（例如 `chef`）。 使用 `chef -v` 查看安装的 Chef 工作站和 Chef 工具版本。 也可以通过在 Chef 工作站应用中选择“关于 Chef 工作站”来检查工作站版本。 

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
> 该路径的顺序非常重要！ 如果 opscode 路径未采用正确的顺序，则会出现问题。
>

在继续操作之前重新启动工作站。

### <a name="install-knife-azure"></a>安装 Knife Azure

本教程假设使用 Azure 资源管理器来与虚拟机交互。

安装包含 Azure 插件的 Knife Azure 扩展。

运行以下命令。

    chef gem install knife-azure ––pre

> [!NOTE]
> `–-pre` 参数确保收到 Knife Azure 插件的最新 RC 版本，在该版本中可以访问最新的 API 集。
>
>

同时，可能还会安装许多依赖项。

![安装 knife-azure 后的输出](./media/chef-automation/install-knife-azure.png)

若要确保所有项都已正确配置，请运行以下命令。

    knife azurerm server list

如果所有项都已正确配置，会看到可用 Azure 映像的列表滚动显示。

祝贺。 现已设置好工作站！

## <a name="creating-a-cookbook"></a>创建食谱

Chef 使用食谱来定义你要在托管客户端上运行的一组命令。 创建食谱的过程十分直截了当，只需使用 `chef generate cookbook` 命令生成食谱模板即可。 此食谱适用于自动部署 IIS 的 Web 服务器。

在 `C:\Chef directory` 下运行以下命令。

    chef generate cookbook webserver

此命令在 C:\Chef\cookbooks\webserver 目录下生成一组文件。 接下来，定义 Chef 客户端要在托管虚拟机上运行的命令集。

这些命令存储在文件 default.rb 中。 在此文件中，定义一组命令用于安装 IIS、启动 IIS 并将模板文件复制到 `wwwroot` 文件夹中。

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

此步骤生成用作 `default.html` 页面的模板文件。

运行以下命令来生成模板：

    chef generate template webserver Default.htm

导航到 `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` 文件。 通过添加一些简单的 *Hello World* HTML 代码来编辑该文件，然后保存该文件。

## <a name="upload-the-cookbook-to-the-chef-server"></a>将食谱上传到 Chef 服务器

此步骤生成在本地计算机上创建的食谱的副本，并将其上传到 Chef 托管服务器。 上传后，食谱将显示在“策略”选项卡下。 

    knife cookbook upload webserver

![将食谱安装到 Chef 服务器后的结果](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>使用 Knife Azure 部署虚拟机

部署 Azure 虚拟机，并使用 `knife` 命令应用 `Webserver` 食谱。

`knife` 命令还会安装 IIS Web 服务和默认网页。

```bash
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
```

`knife` 命令示例在美国西部区域创建装有 Windows Server 2016 的 *Standard_DS2_v2* 虚拟机。 请根据应用的需要修改这些值。

运行该命令后，浏览到 Azure 门户，此时会看到计算机已开始预配。

![正在预配虚拟机](./media/chef-automation/virtual-machine-being-provisioned.png)

接下来显示命令提示符。

![创建虚拟机时的 Knife 输出](./media/chef-automation/knife-output-when-creating-vm.png)

部署完成后，将显示新虚拟机的公共 IP 地址。 请将此值粘贴到 Web 浏览器中，以查看新网站。 部署虚拟机时，我们已打开端口 80，使其可在外部使用。   

![测试虚拟机](./media/chef-automation/testing-the-virtual-machine.png)

此示例使用了富有创造性的 HTML 代码。

还可以在 [Chef 管理](https://manage.chef.io/)中查看节点的状态。 

![查看节点状态](./media/chef-automation/viewing-node-status.png)

请记住，也可以在 Azure 门户中使用端口 3389 通过 RDP 会话进行连接。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [Azure 上的 Chef](/azure/chef/)