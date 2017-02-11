---
title: "将 Node.js 应用程序部署到 Azure 中的 Linux 虚拟机"
description: "了解如何将 Node.js 应用程序部署到 Azure 中的 Linux 虚拟机。"
services: 
documentationcenter: nodejs
author: stepro
manager: dmitryr
editor: 
ms.assetid: 857a812d-c73e-4af7-a985-2d0baf8b6f71
ms.service: multiple
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2016
ms.author: stephpr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 044007fc099007ea64d958fb6c15a6ed29eccdcc


---
# <a name="deploy-a-nodejs-application-to-linux-virtual-machines-in-azure"></a>将 Node.js 应用程序部署到 Azure 中的 Linux 虚拟机
本教程演示如何获取 Node.js 应用程序并将其部署到在 Azure 中运行的 Linux 虚拟机。 本教程中的说明适用于任何能够运行 Node.js 的操作系统。

你将了解如何执行以下操作：

* 分叉和克隆包含一个简单 TODO 应用程序的 GitHub 存储库；
* 在 Azure 中创建和配置两台 Linux 虚拟机，以运行该应用程序；
* 通过将更新推送到 Web 前端虚拟机来循环访问该应用程序。

> [!NOTE]
> 完成本教程需要一个 GitHub 帐户和一个 Microsoft Azure 帐户，并要求能够从开发计算机使用 Git。
> 
> 如果没有 GitHub 帐户，可以在[此处](https://github.com/join)注册。
> 
> 如果没有 [Microsoft Azure](https://azure.microsoft.com/) 帐户，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)注册免费试用版。 如果还没有 Microsoft 帐户，这也将引导用户完成 [Microsoft 帐户](http://account.microsoft.com)的注册过程。 或者，如果是 Visual Studio 订户，则可以[激活 MSDN 权益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。
> 
> 如果开发计算机上没有 Git，并且使用的是 Macintosh 或 Windows 计算机，请从[此处](http://www.git-scm.com)安装 Git。 如果你使用的是 Linux，请使用最适合你的机制安装 Git，如 `sudo apt-get install git`。
> 
> 

## <a name="forking-and-cloning-the-todo-application"></a>分叉并克隆 TODO 应用程序
本教程中使用的 TODO 应用程序在 MongoDB 实例上实现简单的 Web 前端，用于跟踪 TODO 列表。 登录到 GitHub 后，通过右上角链接转到[此处](https://github.com/stepro/node-todo)，以查找该应用程序并对其进行分叉。 这将在你的名为 *accountname*/node-todo 的帐户中创建一个存储库。

然后，在你的开发计算机上，克隆此存储库：

    git clone https://github.com/accountname/node-todo.git

我们将在稍后对源代码进行更改时使用存储库的此本地克隆。

## <a name="creating-and-configuring-the-linux-virtual-machines"></a>创建和配置 Linux 虚拟机
Azure 大力支持使用 Linux 虚拟机进行原始计算。 教程的这一部分演示如何轻松启动两台 Linux 虚拟机，并将 TODO 应用程序部署到这两台虚拟机，其中一台运行运行 Web 前端，另一台运行 MongoDB 实例。

### <a name="creating-virtual-machines"></a>创建虚拟机
在 Azure 中创建新虚拟机的最简单方法是使用 Azure 门户。 单击[此处](https://portal.azure.com)登录并在 Web 浏览器中启动 Azure 门户。 加载 Azure 门户后，完成以下步骤：

* 单击“+ 新建”链接；
* 选择“计算”类别，然后选择“Ubuntu Server 14.04 LTS”；
* 选择“Resource Manager”部署模型，然后单击“创建”；
* 根据以下准则填写基本内容：
  * 指定一个之后可以轻松识别的名称；
  * 本教程选择的是“密码”身份验证；
  * 创建具有可识别名称的新资源组。
* 对于虚拟机大小，本教程合理选择“A1 标准”。
* 对于其他设置，确保磁盘类型为“标准”，并接受其他所有默认值。
* 在摘要页上开始创建。

执行上述过程两次以创建两台 Linux 虚拟机，一台运行 Web 前端，另一台运行 MongoDB 实例。 创建虚拟机需要 5-10 分钟左右的时间。

### <a name="assigning-a-dns-entry-for-virtual-machines"></a>为虚拟机分配 DNS 条目
在 Azure 中创建的虚拟机默认只能通过公共 IP 地址（如 1.2.3.4）访问。 向虚拟机分配 DNS 条目，以使计算机更易于识别。

一旦门户指示已创建虚拟机，单击左侧导航栏中的“虚拟机”链接，然后找到你的计算机。 对于每台计算机：

* 找到“Essentials”选项卡，然后单击公共 IP 地址；
* 在公共 IP 地址配置中，分配 DNS 名称标签并保存。

门户将确保你指定的名称可用。 保存配置后，你的虚拟机将具有类似于 `machinename.region.cloudapp.azure.com` 的主机名称。

### <a name="connecting-to-the-virtual-machines"></a>连接到虚拟机
设置虚拟机后，会对它们进行预配置，以允许通过 SSH 建立远程连接。 这是我们将用来配置虚拟机的机制。 如果你使用 Windows 进行开发，则需要获取 SSH 客户端（如果还没有）。 常见的选择是 PuTTY，可以从[此处](http://www.chiark.greenend.org.uk/~sgtatham/putty/)下载。 Macintosh 和 Linux 操作系统附带预安装的 SSH 版本。

### <a name="configuring-the-web-frontend-virtual-machine"></a>配置 Web 前端虚拟机
使用 PuTTY、ssh 命令行或你喜欢的其他 SSH 工具通过 SSH 连接到你创建的 Web 前端计算机。 此时将看到一条欢迎消息，后跟命令提示符。

首先，确保安装了 Git 和节点：

    sudo apt-get install -y git
    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs

由于应用程序的 Web 前端依赖于某些本机 Node.js 模块，因此还需要安装基本的生成工具集：

    sudo apt-get install -y build-essential

最后，安装名为 *forever* 的 Node.js 应用程序，以便运行 Node.js 服务器应用程序：

    sudo npm install -g forever

这些是为确保此虚拟机能够运行应用程序的 Web 前端所需的全部依赖项，请先运行。 若要执行此操作，请先创建之前已分叉的 GitHub 存储库的裸机克隆，以便可以轻松将更新发布到虚拟机（之后将介绍此更新方案），然后克隆该裸机克隆以提供实际可执行的存储库版本。

从主 (~) 目录开始，运行以下命令（将 *accountname* 替换为 GitHub 用户帐户名）：

    git clone --bare https://github.com/accountname/node-todo.git
    git clone node-todo.git

然后，输入 node-todo 目录并运行以下命令：

    npm install
    forever start server.js

此时将运行应用程序的 Web 前端，但还需要执行一个步骤才能从 Web 浏览器访问该应用程序。 创建的虚拟机受名为 *network security group* 的 Azure 资源保护，该资源是在预配虚拟机时为用户创建的。 目前，该资源仅允许到端口 22 的外部请求路由到虚拟机，这将实现与计算机的 SSH 通信，但也仅限于此。 因此，为了查看 TODO 应用程序（配置为在端口 8080 上运行），还需要打开此端口。

返回到 Azure 门户并完成以下步骤：

* 单击左侧导航栏中的“资源组”；
* 选择包含你虚拟机的资源组；
* 在生成的资源列表中，选择网络安全组（带安全盾形标志图标）；
* 在属性中，选择“入站安全规则”；
* 在工具栏中，单击“添加”；
* 提供一个名称，如“default-allow-todo”；
* 将协议设置为“TCP”；
* 将目标端口范围设置为“8080”；
* 单击“确定”并等待创建安全规则。

创建此安全规则后，TODO 应用程序将在 Internet 上公开可见，你可以通过浏览找到它，例如使用以下 URL：

    http://machinename.region.cloudapp.azure.com:8080

你会发现即使我们尚未配置 MongoDB 虚拟机，TODO 应用程序看起来也可以正常运行。 这是因为源存储库硬编码为使用预部署的 MongoDB 实例。 配置 MongoDB 虚拟机后，我们将回过头来更改源代码，以改为使用我们专用的 MongoDB 实例。

### <a name="configuring-the-mongodb-virtual-machine"></a>配置 MongoDB 虚拟机
使用 PuTTY、ssh 命令行或你喜欢的其他 SSH 工具通过 SSH 连接到你创建的第二台计算机。 看到欢迎消息和命令提示符之后，安装 MongoDB（可从[此处](https://docs.mongodb.org/master/tutorial/install-mongodb-on-ubuntu/)获取操作说明）：

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
    echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

默认情况下，MongoDB 配置为只能从本地访问。 在本教程中，我们将配置 MongoDB 为可以从应用程序的虚拟机访问。 在 sudo 上下文中，打开 /etc/mongod.conf 文件并找到 `# network interfaces` 部分。 将 `net.bindIp` 配置值更改为 `0.0.0.0`。

> [!NOTE]
> 此配置仅适用于本教程。 它**不**是建议的安全做法，不应在生产环境中使用。
> 
> 

然后，确保已启动 MongoDB 服务：

    sudo service mongod restart

MongoDB 默认通过端口 27017 进行操作。 因此，与我们需要在 Web 前端虚拟机上打开端口 8080 一样，我们需要在 MongoDB 虚拟机上打开端口 27017。

返回到 Azure 门户并完成以下步骤：

* 单击左侧导航栏中的“资源组”；
* 选择包含 MongoDB 虚拟机的资源组；
* 在生成的资源列表中，选择名称与你提供给 MongoDB 虚拟机的名称相同的网络安全组（带安全盾形标志图标）；
* 在属性中，选择“入站安全规则”；
* 在工具栏中，单击“添加”；
* 提供一个名称，如“default-allow-mongo”；
* 将协议设置为“TCP”；
* 将目标端口范围设置为“27017”；
* 单击“确定”并等待创建安全规则。

## <a name="iterating-on-the-todo-application"></a>循环访问 TODO 应用程序
到目前为止，我们已经设置了两台 Linux 虚拟机：一台运行应用程序的 Web 前端，另一台运行 MongoDB 实例。 但有一个问题，即 Web 前端实际尚未使用设置的 MongoDB 实例。 若要修复该问题，可以将 Web 前端代码更新为使用环境变量，而不是使用硬编码的实例。

### <a name="changing-the-todo-application"></a>更改 TODO 应用程序
在你首次克隆 node-todo 存储库的开发计算机上，在你喜欢的编辑器中打开 `node-todo/config/database.js` 文件，并将 URL 值从硬编码的值（如 `mongodb://...`）更改为 `process.env.MONGODB`。

提交所做的更改并推送到 GitHub 主机：

    git commit -am "Get MongoDB instance from env"
    git push origin master

遗憾的是，这不会将更改发布到 Web 前端虚拟机。 接下来，对该虚拟机进行其他几项更改，实现一种简单而有效的更新发布机制，以便可以在实时环境中快速观察到更改的效果。

### <a name="configuring-the-web-frontend-virtual-machine"></a>配置 Web 前端虚拟机
还记得我们之前在 Web 前端虚拟机上创建了 node-todo 存储库的裸机克隆。 事实证明，此操作将创建一个新的 Git 远程存储库，可向该远程存储库推送更改。 但是，仅推送到此远程存储库并不提供开发人员在处理其代码时所需的快速循环访问模型。

我们希望实现的目标是，确保在推送到虚拟机上的远程存储库时，运行的 TODO 应用程序将自动更新。 幸运的是，这可以通过 Git 轻松实现。

Git 公开了大量挂钩，这些挂钩在特定时间的调用以响应对存储库执行的操作。 这些挂钩使用存储库的 `hooks` 文件夹中的 shell 脚本指定。 最适用于自动更新方案的挂钩是 `post-update` 事件。

在与 Web 前端虚拟机的 SSH 会话中，更改为 `~/node-todo.git/hooks` 目录，并将以下内容添加到名为 `post-update` 的文件（将 `machinename` 和 `region` 替换为你的 MongoDB 虚拟机信息）：

    #!/bin/bash

    forever stopall
    unset 'GIT_DIR'
    export MONGODB="mongodb://machinename.region.cloudapp.azure.com:27017/tododb"
    cd ~/node-todo && git fetch origin && git pull origin master && npm install && forever start ~/node-todo/server.js
    exec git update-server-info

确保此文件可通过运行以下命令执行：

    chmod 755 post-update

此脚本将确保停止当前服务器应用程序、克隆存储库中的代码更新为最新、满足任何更新的依赖项，以及重启服务器。 它还将确保已配置了环境，做好了接收我们第一个应用程序更新的准备，以从环境变量获取 MongoDB 实例。

### <a name="configuring-your-development-machine"></a>配置你的开发计算机
接下来，我们将尝试把你的开发计算机连接到 Web 前端虚拟机。 这与将虚拟机上的裸机存储库添加为远程存储库一样简单。 运行以下命令以执行此操作（根据需要将 *user* 替换为 Web 前端虚拟机登录名，并替换 *machinename* 和 *region*）：

    git remote add azure user@machinename.region.cloudapp.azure.com:node-todo.git

这是启用推送所需的全部操作，或在实际发布时，更改为 Web 前端虚拟机。

### <a name="publishing-updates"></a>发布更新
发布到目前为止进行的一项更改，以便应用程序使用我们自己的 MongoDB 实例：

    git push azure master

此时将看到与下面类似的输出：

    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (4/4), 406 bytes | 0 bytes/s, done.
    Total 4 (delta 1), reused 0 (delta 0)
    remote: info:    Forever stopped processes:
    remote: data:        uid  command         script    forever pid  id logfile                         uptime
    remote: data:    [0] 0Lyh /usr/bin/nodejs server.js 9064    9301    /home/username/.forever/0Lyh.log 0:0:3:17.487
    remote: From /home/username/node-todo
    remote:    5f31fd7..5bc7be5  master     -> origin/master
    remote: From /home/username/node-todo
    remote:  * branch            master     -> FETCH_HEAD
    remote: Updating 5f31fd7..5bc7be5
    remote: Fast-forward
    remote:  config/database.js | 2 +-
    remote:  1 file changed, 1 insertion(+), 1 deletion(-)
    remote: npm WARN package.json node-todo@0.0.0 No repository field.
    remote: npm WARN package.json node-todo@0.0.0 No license field.
    remote: warn:    --minUptime not set. Defaulting to: 1000ms
    remote: warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
    remote: info:    Forever processing file: /home/username/node-todo/server.js
    To username@machinename.region.cloudapp.azure.com:node-todo.git
    5f31fd7..5bc7be5  master -> master

此命令完成后，尝试在 Web 浏览器中刷新应用程序。 此时将能够看到此处呈现的 TODO 列表为空，无法再绑定到共享的已部署 MongoDB 实例。

在本教程中，我们将进行另一项更明显的更改。 在你的开发计算机上，使用你喜欢的编辑器打开 node-todo/public/index.html 文件。 找到 jumbotron 标头，并将标题从“I'm a Todo-aholic”更改为“I'm a Todo-aholic on Azure!”。

然后进行提交：

    git commit -am "Azurify the title"

这一次，先将更改发布到 Azure，再将其推送回 GitHub 存储库：

    git push azure master

此命令完成后，刷新网页，你将看到所做的更改。 这些更改看起来都不错，因此将更改推送回原点远程存储库： 

    git push origin master

## <a name="next-steps"></a>后续步骤
本文演示如何获取 Node.js 应用程序并将其部署到在 Azure 中运行的 Linux 虚拟机。 若要了解有关 Azure 中的 Linux 虚拟机的详细信息，请参阅 [Azure 上的 Linux 简介](/documentation/articles/virtual-machines-linux-introduction/)。

有关如何在 Azure 上开发 Node.js 应用程序的详细信息，请参阅 [Node.js 开发人员中心](/develop/nodejs/)。




<!--HONumber=Nov16_HO3-->


