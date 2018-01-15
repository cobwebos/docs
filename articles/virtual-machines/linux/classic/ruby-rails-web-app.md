---
title: "在 Linux VM 上托管 Ruby on Rails 网站 | Microsoft Docs"
description: "在 Azure 上使用 Linux 虚拟机设置和托管基于 Ruby on Rails 的网站。"
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: fb6ded1dcba2ac0f78fc6f1f4f7de9238cd752bd
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2018
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Azure 虚拟机上的 Ruby on Rails Web 应用程序
本教程介绍如何在 Azure 中使用 Linux 虚拟机托管 Ruby on Rails 网站。  

本教程使用 Ubuntu Server 14.04 LTS 进行了验证。 如果使用不同 Linux 分发，则可能需要修改安装 Rails 的步骤。

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[Resource Manager 和经典](../../../azure-resource-manager/resource-manager-deployment-model.md)。  本文介绍使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]
>

## <a name="create-an-azure-vm"></a>创建 Azure VM
首先，使用 Linux 映像创建 Azure VM。

若要创建 VM，可以使用 Azure 门户或 Azure 命令行接口 (CLI)。

### <a name="azure-portal"></a>Azure 门户
1. 登录到 [Azure 门户](https://portal.azure.com)
2. 单击“新建”，再在搜索框中键入“Ubuntu Server 14.04”。 单击搜索返回的条目。 对于部署模型，选择“经典”，再单击“创建”。
3. 在“基本信息”边栏选项卡中，输入以下必填字段的值：“名称”（对于 VM）、“用户名”、“身份验证类型”和相应凭据、Azure “订阅”、“资源组”和“位置”。

   ![创建新的 Ubuntu 映像](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. 预配 VM 后，依次单击 VM 名称和“设置”类别中的“终结点”。 找到“独立”下列出的 SSH 终结点。

   ![默认终结点](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Azure CLI
执行[创建运行 Linux 的虚拟机][vm-instructions]中的步骤。

预配 VM 后，可以通过运行以下命令来获取 SSH 终结点：

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>在 Rails 上安装 Ruby
1. 使用 SSH 连接到 VM。
2. 从 SSH 会话中，使用以下命令在虚拟机上安装 Ruby：

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    安装可能需要几分钟时间。 安装完成后，使用以下命令验证 Ruby 是否已安装：

        ruby -v

3. 使用以下命令安装 Rails：

        sudo gem install rails --no-rdoc --no-ri -V

    使用 --no-rdoc 和 --no-ri 标志可跳过安装文档，这样可加快速度。
    执行此命令可能需要花费较长时间，添加 -V 可显示有关安装进度的信息。

## <a name="create-and-run-an-app"></a>创建并运行应用
在仍已通过 SSH 登录的情况下，运行以下命令：

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

[new](http://guides.rubyonrails.org/command_line.html#rails-new) 命令创建新的 Rails 应用。 [server](http://guides.rubyonrails.org/command_line.html#rails-server) 命令启动 Rails 附带的 WEBrick Web 服务器。 （在生产环境中使用时，你可能需要使用其他服务器，例如 Unicorn 或 Passenger。）

应该会看到与下面类似的输出。

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>添加终结点
1. 转到 [Azure 门户] [https://portal.azure.com] ，并选择 VM。

2. 选择页面左侧“设置”中的“终结点”。

3. 单击页面顶部的“添加”。

4. 在“添加终结点”对话框中，输入以下信息：

   * **名称**：HTTP
   * **协议**：TCP
   * **公用端口**：80
   * **专用端口**：3000
   * **浮动 IP 地址**：已禁用
   * **访问控制列表 - 顺序**：1001，或设置此访问规则优先级的其他值。
   * **访问控制列表 - 名称**：allowHTTP
   * **访问控制列表 - 操作**：允许
   * **访问控制列表 - 远程子网**：1.0.0.0/16

     此终结点有一个公用端口 80，可以将流量路由到专用端口 3000，即 Rails 服务器侦听的端口。 访问控制列表规则允许端口 80 上有公用流量。

     ![new-endpoint](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. 单击“确定”，保存此终结点。

6. 应该会看到一条内容为“正在保存虚拟机终结点”的消息。 此消息消失后，终结点即处于活动状态。 现在可以通过导航到虚拟机的 DNS 名称来测试应用程序。 网站应显示如下：

    ![默认 rails 页面][default-rails-cloud]

## <a name="next-steps"></a>后续步骤
在本教程中，手动执行大多数步骤。 在生产环境中，会在开发计算机上编写应用，然后将其部署到 Azure VM。 此外，大多数生产环境都结合其他服务器进程（如 Apache 或 NginX）来托管 Rails 应用程序，这些进程会处理路由到多个 Rails 应用程序实例的请求并提供静态资源。 有关详细信息，请参阅 http://rubyonrails.org/deploy/。

若要详细了解 Ruby on Rails，请参阅 [Ruby on Rails 指南][rails-guides]。

若要从 Ruby 应用程序使用 Azure 服务，请参阅：

* [使用 Blob 存储非结构化数据][blobs]
* [使用表存储键/值对][tables]
* [使用内容交付网络提供高带宽内容][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
