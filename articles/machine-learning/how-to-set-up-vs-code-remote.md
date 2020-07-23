---
title: '交互式调试： VS Code & ML 计算实例 (预览) '
titleSuffix: Azure Machine Learning
description: 设置 VS Code Remote，使用 Azure 机器学习对代码进行交互式调试。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jmartens
author: j-martens
ms.date: 07/09/2020
ms.openlocfilehash: 46aefa62ab00c0b3bc2b90467a9fafd61439a10e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86203451"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote-preview"></a>使用 VS Code 远程 (预览版以交互方式在 Azure 机器学习计算实例上调试) 

本文介绍如何在 Azure 机器学习计算实例上设置 Visual Studio Code Remote，从而通过 VS Code 对代码进行交互式调试  。 

+ [Azure 机器学习计算实例](concept-compute-instance.md)是基于云的完全托管数据科学工作站，并为 IT 管理员提供管理功能和企业就绪功能。 


+ 通过使用 [Visual Studio Code Remote](https://code.visualstudio.com/docs/remote/remote-overview) Development，可将容器、远程计算机或适用于 Linux 的 Windows 子系统 (WSL) 作为功能完备的开发环境使用。 

## <a name="prerequisite"></a>先决条件  

在 Windows 平台上，必须[安装 OpenSSH 兼容的 SSH 客户端](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client)（如果还没有）。 

> [!Note]
> Windows 上不支持 PuTTY，因为 ssh 命令必须在路径中。 

## <a name="get-ip-and-ssh-port"></a>获取 IP 和 SSH 端口 

1. 前往 https://ml.azure.com/ ，转到 Azure 机器学习工作室。

2. 选择[工作区](concept-workspace.md)。
1. 单击“计算实例”选项卡  。
1. 在“应用程序 URI”列中，单击要用作远程计算的计算实例的 SSH 链接   。 
1. 请记下对话框中的 IP 地址和 SSH 端口。 
1. 将私钥保存到本地计算机上的 ~/.ssh/ 目录中。例如，打开新文件的编辑器，然后将密钥粘贴进来： 

   Linux  ： 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**： 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   私钥如下所示：
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. 更改文件的权限，确保只有你才能读取该文件。  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>将实例添加为主机 

在编辑器中打开文件 `~/.ssh/config` (Linux) 或 `C:\Users<username>.ssh\config` (Windows)，并添加类似以下内容的新条目：

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
|主机|对计算实例应用所需的任何简写 |
|HostName|这是计算实例的 IP 地址 |
|端口|这是上述 SSH 对话框中显示的端口 |
|User|这应为  `azureuser` |
|IdentityFile|应指向保存私钥的文件 |

现在应该能够使用之前采用的简写 `ssh azmlci1` 通过 ssh 连接到计算实例。 

## <a name="connect-vs-code-to-the-instance"></a>将 VS Code 连接到实例 

1. [安装 Visual Studio Code](https://code.visualstudio.com/)。

1. [安装远程 Remote SSH 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)。 

1. 单击左侧的“Remote-SSH”图标，显示 SSH 配置。

1. 右键单击刚创建的 SSH 主机配置。

1. 选择“连接到当前窗口中的主机”  。 

从这里开始，你会完全在计算实例上操作，可以编辑、调试、使用 git、使用扩展等，就和使用本地 Visual Studio Code 时一样。 

## <a name="next-steps"></a>后续步骤

现在，你已设置 Visual Studio Code Remote，可以将计算实例用作 Visual Studio Code 中的远程计算，从而对代码进行交互式调试。 

[教程：训练自己的首个 ML 模型](tutorial-1st-experiment-sdk-train.md)演示如何将计算实例与集成的笔记本配合使用。