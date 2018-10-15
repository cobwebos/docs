---
title: Azure 快速入门 - 创建 Batch AI 群集 - 门户 | Microsoft Docs
description: 快速入门 - 为训练机器学习和 AI 模型创建 Batch AI 群集 - Azure 门户
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ms.openlocfilehash: 8b9daa0fbbf84e0f602498a0847c9e120f709b17
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057433"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建用于 Batch AI 训练作业的群集

本快速入门演示如何使用 Azure 门户创建 Batch AI 群集，以便将其用于训练 AI 和机器学习模型。 Batch AI 是一项托管的服务，可以让数据科学家和 AI 研究人员在 Azure 虚拟机群集上大规模训练 AI 和机器学习模型。

群集刚开始有一个 GPU 节点和一个附加的文件服务器。 完成本快速入门后，你会有一个可以纵向扩展的群集，用于训练深度学习模型。 使用 Batch AI、[Azure 机器学习](../machine-learning/service/overview-what-is-azure-ml.md)工具或 [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai) 将训练作业提交到群集。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-ssh-key-pair"></a>创建 SSH 密钥对

需要一个 SSH 密钥对才能完成本快速入门。 如果有现成的 SSH 密钥对，则可跳过此步骤。

若要创建 SSH 密钥对，请从 Bash Shell 运行以下命令并根据屏幕上的说明操作。 例如，可以使用 [Azure Cloud Shell](../cloud-shell/overview.md)，或者在 Windows 上使用[适用于 Linux 的 Windows 子系统](/windows/wsl/install-win10)。 命令输出包括公钥文件的文件名。 将公钥文件 (`cat ~/.ssh/id_rsa.pub`) 的内容复制到剪贴板或其他可以在后面的步骤中访问的位置。

```bash
ssh-keygen -t rsa -b 2048
```

若要更详细地了解如何创建 SSH 密钥对，请参阅[创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥-私钥对](../virtual-machines/linux/mac-create-ssh-keys.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-batch-ai-workspace"></a>创建 Batch AI 工作区

从创建组织 Batch AI 资源所需的 Batch AI 工作区着手。 工作区可以包含一个或多个群集，或者包含其他 Batch AI 资源。

1. 选择“所有服务”，然后针对 **Batch AI** 进行筛选。

2. 选择“添加工作区”。

3. 在“工作区名称”和“资源组”中输入值。 可以视需要为工作区的“订阅”和“位置”选择不同的选项。 选择“创建工作区”。

  ![创建 Batch AI 工作区](./media/quickstart-create-cluster-portal/create-workspace.png)

出现“部署成功”消息后，转到所创建的资源，然后选择工作区。

## <a name="create-a-file-server"></a>创建文件服务器

Batch AI 文件服务器是单节点 NFS，可以自动装载在群集节点上。 若要为训练作业的输入数据和输出提供存储，则它是多种可选方式之一。

1. 在工作区中，选择“文件服务器” > “添加 Batch AI 文件服务器”。

2. 输入“文件服务器名称”和“VM 大小”的值。 就本快速入门来说，建议对文件服务器使用 *Standard D1_v2* 作为 VM 大小。 若需为训练作业存储更大量的输入或输出数据，请选择其他大小。

3. 输入**管理员用户名**，将 SSH 公钥文件的内容复制到 **SSH 密钥**。 至于其余值，请接受默认设置，然后选择“创建文件服务器”。

  ![创建 Batch AI 文件服务器](./media/quickstart-create-cluster-portal/create-file-server.png)

部署文件服务器需要几分钟时间。

创建服务器以后，请单击“属性”，记下“装载设置”。 可以通过 SSH 连接到服务器的公共 IP 地址，以便通过指示的目录 (*/data*) 上传和下载训练数据和输出文件。

![文件服务器属性](./media/quickstart-create-cluster-portal/file-server-properties.png)

## <a name="create-a-cluster"></a>创建群集

以下步骤创建包含单个 GPU 节点的群集。 群集节点运行一个默认的 Ubuntu Server 映像，该映像旨在托管基于容器的应用程序，而此类应用程序可以用于大多数训练工作负荷。 群集节点将文件服务器装载在其装入点上。 

1. 在 Batch AI 工作区中，选择“群集” > “添加 Batch AI 群集”。

2. 输入“群集名称”的值和以下设置。 建议的 VM 大小有一个 NVIDIA Tesla K80 GPU。
  
   |设置  |值  |
   |---------|---------|
   |**VM 大小**     |标准 NC6|
   |**节点目标数目**     |1|

3. 输入**管理员用户名**，将 SSH 公钥文件的内容复制到 **SSH 密钥**。 至于此页上的其余值，请接受默认设置，然后选择“下一步: 节点设置”。

   ![输入基本的群集信息](./media/quickstart-create-cluster-portal/create-cluster.png)

4. 在“装载卷”下选择“文件服务器引用” > “添加”。 选择以前创建的文件服务器。 输入一个**相对装载路径**，这是在每个群集节点上装载文件服务器的位置。 选择“保存并继续”。

   ![添加文件服务器引用](./media/quickstart-create-cluster-portal/file-server-reference.png)

保存节点设置，然后选择“创建群集”。

Batch AI 需要数分钟来分配此节点。 在此期间，群集的“分配状态”为“正在重设大小”。 数分钟后，群集的状态为“稳定”，节点启动。

![群集正在启动](./media/quickstart-create-cluster-portal/cluster-resizing.png)

选择群集名称，查看节点的状态。 节点在状态为“空闲”时可以运行训练作业。

### <a name="list-cluster-nodes"></a>列出群集节点

如需连接到群集节点（在此示例中为单个节点）来安装应用程序或执行维护，请在门户中获取连接信息。 创建群集以后，请单击“节点”，记下 SSH 的“连接”设置（IP 地址和端口号）。

![群集节点](./media/quickstart-create-cluster-portal/cluster-nodes.png)

根据此信息进行 SSH 连接，以便连接到节点。 例如，在以下命令中使用节点的正确 IP 地址和端口号来替换相应值：

```bash
ssh myusername@137.135.82.15 -p 50000
``` 

### <a name="resize-the-cluster"></a>重设群集大小

使用群集来训练模型时，可能需要更多计算资源。 例如，若要针对某个分布式训练作业将大小增加到 2 个节点，请选择“规模”，然后将“目标节点数”设置为 2。 保存配置。

![缩放群集](./media/quickstart-create-cluster-portal/scale-cluster.png)

重设群集大小需要数分钟。

## <a name="clean-up-resources"></a>清理资源

若要继续学习 Batch AI 教程和示例，请使用在本快速入门中创建的 Batch AI 工作区、文件服务器和群集。

只要有基础虚拟机在运行，就会对 Batch AI 群集和文件服务器收费，即使没有计划作业。 若要在没有作业运行的情况下保留群集配置，请将群集大小重设为 0 个节点。 随后请根据运行作业的需要，将其大小重设为 1 个或多个节点。 

不再需要包含群集和文件服务器的 Batch AI 工作区时，请将其删除。 为此，请选择 Batch AI 工作区，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure 门户创建 Batch AI 群集和附加的文件服务器。 若要了解如何使用 Batch AI 群集来训练模型，请转到介绍如何训练深度学习模型的快速入门。

> [!div class="nextstepaction"]
> [训练深度学习模型](./quickstart-tensorflow-training-cli.md)
