---
title: "在 Azure 上的 Linux (Ubuntu) VM 中创建第一个 Jenkins 主机"
description: "利用解决方案模板部署 Jenkins。"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: multiple
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 748f1994d0ee5b6c6a988bca8e5b694b29c0b8c5
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017

---

# 在 Azure 上的 Linux (Ubuntu) VM 中创建第一个 Jenkins 主机
<a id="create-your-first-jenkins-master-on-a-linux-ubuntu-vm-on-azure" class="xliff"></a>

本快速入门演示如何在 Linux (Ubuntu 14.04 LTS) VM 上同时安装最新稳定版 Jenkins 以及已配置为适用于 Azure 的工具和插件。 这些工具包括：
<ul>
<li>用于源控件的 Git</li>
<li>用于进行安全连接的 Azure 凭据插件</li>
<li>用于弹性生成、测试和持续集成的 Azure VM 代理插件</li>
<li>用于存储项目的 Azure 存储插件</li>
<li>用于使用脚本部署应用的 Azure CLI</li>
</ul>

本教程将介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建免费 Azure 帐户。
> * 使用解决方案模板在 Azure VM 上创建 Jenkins 主机。 
> * 执行 Jenkins 的初始配置。
> * 安装建议的插件。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## 通过部署用于 Jenkins 的解决方案模板在 Azure 中创建 VM
<a id="create-the-vm-in-azure-by-deploying-the-solution-template-for-jenkins" class="xliff"></a>

Azure 快速入门模板使用户能够快速、可靠地在 Azure 上部署复杂的技术。  Azure Resource Manager 允许用户使用[声明性模板](https://azure.microsoft.com/en-us/resources/templates/?term=jenkins)预配应用程序。 在单个模板中，可以部署多个服务及其依赖项。 在应用程序生命周期的每个阶段，可使用相同模板重复部署应用程序。

查看此模板的[计划和定价](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.jenkins?tab=PlansAndPrice)信息，了解成本选项。

转到[适用于 Jenkins 应用商店映像](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview)，单击“立即获取”  

在 Azure 门户中，单击“创建”。  此模板需要使用 Resource Manager，以便禁用模板模型下拉列表。
   
![Azure 门户对话框](./media/install-jenkins-solution-template/ap-create.png)

在“配置基本设置”选项卡中：

![配置基本设置](./media/install-jenkins-solution-template/ap-basic.png)

* 提供 Jenkins 实例的名称。
* 选择 VM 磁盘类型。  对于生产工作负荷，选择较大的 VM 和 SSD，以便获得更好的性能。  有关 Azure 磁盘类型的详细信息，可阅读[此文](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage)
* 用户名：必须满足长度要求，并且不能包含保留字或不支持的字符。 类似“管理员”的名称是不允许的。  有关详细信息，请参阅[此处](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq)，了解用户名和密码要求。
* 身份验证类型：创建用密码或 [SSH 公钥](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows)保护的实例。 如果使用密码，则它必须同时满足以下要求中的 3 个：1 个小写字符、1 个大写字符、1 个数字和 1 个特殊字符。
* Jenkins 发行类型始终为 **LTS**
* 选择一个订阅。
* 创建资源组或使用现有资源组。
* 选择一个位置。

在“配置更多选项”选项卡中：

![设置其他选项](./media/install-jenkins-solution-template/ap-addtional.png)

* 提供域名标签，用于唯一标识 Jenkins 主机。

单击“确定”转到下一步。 

通过验证后，单击“确定”以下载模板和参数。 

接下来，选择“购买”以预配所有资源。

## 设置 SSH 端口转发
<a id="setup-ssh-port-forwarding" class="xliff"></a>

默认情况下，Jenkins 实例使用 http 协议，并且在端口 8080 上侦听。 用户不应通过不安全的协议进行身份验证。
    
设置端口转发以查看本地计算机上的 Jenkins UI。

### 如果使用 Windows：
<a id="if-you-are-using-windows" class="xliff"></a>

如果使用密码保护 Jenkins，请安装 PuTTY 并运行此命令：
```
putty.exe -ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* 要登录，请输入密码。

![要登录，请输入密码。](./media/install-jenkins-solution-template/jenkins-pwd.png)

如果使用 SSH，请运行此命令：
```
putty -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

### 如果使用 Linux 或 Mac：
<a id="if-you-are-using-linux-or-mac" class="xliff"></a>

如果使用密码保护 Jenkins 主机，请运行此命令：
```
ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* 要登录，请输入密码。

如果使用 SSH，请运行此命令：
```
ssh -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

## 连接到 Jenkins
<a id="connect-to-jenkins" class="xliff"></a>
启动隧道后，导航到本地计算机上的 http://localhost:8080/。

首次解锁 Jenkins 仪表板时，请使用初始管理员密码。

![解锁 Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

若要获取令牌，请通过 SSH 访问 VM，然后运行 `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`。

![解锁 Jenkins](./media/install-jenkins-solution-template/jenkins-ssh.png)

系统会要求安装建议的插件。

接下来，为 Jenkins 主机创建管理员用户。

现在即可使用 Jenkins 实例！ 可以通过转到 http://\<刚创建的实例的公共 DNS 名称\> 访问只读视图。

![Jenkins 已准备就绪！](./media/install-jenkins-solution-template/jenkins-welcome.png)

## 后续步骤
<a id="next-steps" class="xliff"></a>

在本教程中：

> [!div class="checklist"]
> * 使用解决方案模板创建了 Jenkins 主机。
> * 执行了 Jenkins 的初始配置。
> * 安装了插件。

点击此链接了解如何使用 Azure VM 代理实现与 Jenkins 的持续集成。

> [!div class="nextstepaction"]
> [将 Azure VM 用作 Jenkins 代理](jenkins-azure-vm-agents.md)

