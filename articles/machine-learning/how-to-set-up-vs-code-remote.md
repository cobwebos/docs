---
title: 连接到 Visual Studio Code 中的计算实例（预览）
titleSuffix: Azure Machine Learning
description: 了解如何连接到 Visual Studio Code 中的 Azure 机器学习计算实例
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jmartens
author: j-martens
ms.date: 09/03/2020
ms.openlocfilehash: 6ac116b315d4a11b51b37c5b51edf35aa0676713
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708367"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>连接到 Visual Studio Code 中的 Azure 机器学习计算实例（预览）

在本文中，你将学习如何使用 Visual Studio Code 连接到 Azure 机器学习计算实例。

[Azure 机器学习计算实例](concept-compute-instance.md)是基于云的完全托管数据科学工作站，并为 IT 管理员提供管理功能和企业就绪功能。

有两种方法可从 Visual Studio Code 连接到计算实例：

* 远程 Jupyter Notebook 服务器。 通过此选项，可将计算实例设置为远程 Jupyter Notebook 服务器。
* [Visual Studio Code 远程开发](https://code.visualstudio.com/docs/remote/remote-overview)。 通过 Visual Studio Code 远程开发，可将容器、远程计算机或适用于 Linux 的 Windows 子系统 (WSL) 作为功能完备的开发环境使用。

## <a name="configure-compute-instance-as-remote-notebook-server"></a>将计算实例配置为远程笔记本服务器

若要将计算实例配置为远程 Jupyter Notebook 服务器，需要满足以下先决条件：

* Azure 机器学习 Visual Studio Code 扩展。 有关详细信息，请参阅 [Azure 机器学习 Visual Studio Code 扩展安装指南](tutorial-setup-vscode-extension.md)。
* Azure 机器学习工作区。 如果还没有工作区，请[使用 Azure 机器学习 Visual Studio Code 扩展创建新工作区](how-to-manage-resources-vscode.md#create-a-workspace)。

若要连接到计算实例：

1. 在 Visual Studio Code 中打开 Jupyter Notebook。
1. 加载集成笔记本体验时，选择“Jupyter 服务器”。

    > [!div class="mx-imgBorder"]
    > ![启动 Azure 机器学习远程 Jupyter 笔记本服务器下拉列表](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    此外，还可使用命令面板：

    1. 从菜单栏选择“视图”>“命令面板”，打开命令面板。
    1. 在文本框中输入 `Azure ML: Connect to Compute instance Jupyter server`。

1. 从 Jupyter 服务器选项列表中选择 `Azure ML Compute Instances`。
1. 从订阅列表中选择你的订阅。 如果之前配置过默认 Azure 机器学习工作区，则跳过此步骤。
1. 选择工作区。
1. 从列表中选择你的计算实例。 如果没有计算实例，请选择“创建新的 Azure ML 计算实例”，并按照提示进行创建。
1. 要使更改生效，必须重新加载 Visual Studio Code。
1. 打开 Jupyter Notebook 并运行一个单元。

> [!IMPORTANT]
> 必须运行一个单元才能建立连接。

此时，你可继续在 Jupyter 笔记本中运行单元。

> [!TIP]
> 也可使用包含类似 Jupyter 的代码单元的 Python 脚本文件 (.py)。 有关详细信息，请参阅 [Visual Studio Code Python 交互文档](https://code.visualstudio.com/docs/python/jupyter-support-py)。

## <a name="configure-compute-instance-remote-development"></a>配置计算实例远程开发

若要获得功能完备的远程开发体验，需要满足下面几项先决条件：

* [Visual Studio Code 远程 SSH 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)。
* 启用了 SSH 的计算实例。 有关详细信息，请参阅[创建计算实例指南](how-to-create-manage-compute-instance.md)。

> [!NOTE]
> 在 Windows 平台上，必须[安装 OpenSSH 兼容的 SSH 客户端](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client)（如果还没有）。 Windows 上不支持 PuTTY，因为 ssh 命令必须在路径中。

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>获取计算实例的 IP 和 SSH 端口

1. 前往 https://ml.azure.com/，转到 Azure 机器学习工作室。
2. 选择[工作区](concept-workspace.md)。
1. 单击“计算实例”选项卡****。
1. 在“应用程序 URI”列中，单击要用作远程计算的计算实例的 SSH 链接********。 
1. 请记下对话框中的 IP 地址和 SSH 端口。 
1. 将私钥保存到本地计算机上的 ~/.ssh/ 目录中。例如，打开新文件的编辑器，然后将密钥粘贴进来： 

   Linux：

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**：

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   私钥如下所示：

   ```text
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

### <a name="add-instance-as-a-host"></a>将实例添加为主机

在编辑器中打开文件 `~/.ssh/config` (Linux) 或 `C:\Users<username>.ssh\config` (Windows)，并添加类似以下内容的新条目：

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

下面是有关这些字段的一些详细信息：

|字段|描述|
|----|---------|
|主机|对计算实例应用所需的任何简写 |
|HostName|这是计算实例的 IP 地址 |
|Port|这是上述 SSH 对话框中显示的端口 |
|用户|这应为  `azureuser` |
|IdentityFile|应指向保存私钥的文件 |

现在应该能够使用之前采用的简写 `ssh azmlci1` 通过 ssh 连接到计算实例。

### <a name="connect-vs-code-to-the-instance"></a>将 VS Code 连接到实例

1. 单击 Visual Studio Code 活动栏中的远程 SSH 图标，调出 SSH 配置。

1. 右键单击刚创建的 SSH 主机配置。

1. 选择“连接到当前窗口中的主机”****。 

从这里开始，你会完全在计算实例上操作，可以编辑、调试、使用 git、使用扩展等，就和使用本地 Visual Studio Code 时一样。

## <a name="next-steps"></a>后续步骤

现在，你已设置 Visual Studio Code 远程，可以将计算实例用作 Visual Studio Code 中的远程计算，从而[对代码进行交互式调试](how-to-debug-visual-studio-code.md)。

[教程：训练自己的首个 ML 模型](tutorial-1st-experiment-sdk-train.md)演示如何将计算实例与集成的笔记本配合使用。