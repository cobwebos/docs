---
title: 交互式调试： VS Code & ML 计算实例
titleSuffix: Azure Machine Learning
description: 设置 VS Code Remote，以通过 Azure 机器学习以交互方式调试你的代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169753"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>使用 VS Code 远程在 Azure 机器学习计算实例上交互式调试

在本文中，你将了解如何在 Azure 机器学习计算实例上设置 Visual Studio Code 远程，以便可以从 VS Code**以交互方式调试你的代码**。 

+ [Azure 机器学习计算实例](concept-compute-instance.md)是一种完全托管的基于云的数据科学家工作站，为 IT 管理员提供管理和企业就绪能力。 


+ [Visual Studio Code 远程](https://code.visualstudio.com/docs/remote/remote-overview)开发使你可以使用容器、远程计算机或适用于 Linux 的 Windows 子系统（WSL）作为功能完备的开发环境。 

## <a name="prerequisite"></a>先决条件  

在 Windows 平台上，如果尚不存在，则必须[安装与 OpenSSH 兼容的 SSH 客户端](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client)。 

> [!Note]
> Windows 上不支持 PuTTY，因为 ssh 命令必须在路径中。 

## <a name="get-ip-and-ssh-port"></a>获取 IP 和 SSH 端口 

1. 在 https://ml.azure.com/中转到 Azure 机器学习 studio。

2. 选择[工作区](concept-workspace.md)。
1. 单击 "**计算实例**" 选项卡。
1. 在 "**应用程序 URI** " 列中，单击要用作远程计算的计算实例的 " **SSH** " 链接。 
1. 在对话框中，记下 IP 地址和 SSH 端口。 
1. 将私钥保存到本地计算机上的 ~/.ssh/目录中;例如，为新文件打开一个编辑器，然后将该密钥粘贴到： 

   Linux： 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   Windows： 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   私钥看起来将类似于：
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. 更改文件的权限，以确保只有你才能读取该文件。  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>将实例添加为主机 

在编辑器中打开文件 `~/.ssh/config` （Linux）或 `C:\Users<username>.ssh\config` （Windows），并添加类似于下面的新项：

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

下面是有关这些字段的一些详细信息： 

|字段|说明|
|----|---------|
|主机|使用所需的任何速记作为计算实例 |
|HostName|这是计算实例的 IP 地址 |
|端口|这是上面的 SSH 对话框中显示的端口 |
|用户|这需要 `azureuser` |
|IdentityFile|应指向保存私钥的文件 |

现在，你应该能够使用在上面使用的速记 `ssh azmlci1`来使用 ssh 连接到计算实例。 

## <a name="connect-vs-code-to-the-instance"></a>将 VS Code 连接到实例 

1. [安装 Visual Studio Code](https://code.visualstudio.com/)。

1. [安装远程 SSH 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)。 

1. 单击左侧的 "远程 SSH" 图标以显示 SSH 配置。

1. 右键单击刚创建的 SSH 主机配置。

1. 选择 **"连接到当前窗口中的主机"** 。 

从这里开始，你完全正在处理计算实例，你现在可以编辑、调试、使用 git、使用扩展等，如你可以使用本地 Visual Studio Code。 

## <a name="next-steps"></a>后续步骤

现在，你已设置 Visual Studio Code 远程，你可以使用计算实例作为 Visual Studio Code 的远程计算，以便以交互方式调试你的代码。 

[教程：训练首个 ML 模型](tutorial-1st-experiment-sdk-train.md)介绍如何将计算实例与集成笔记本一起使用。