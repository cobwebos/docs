---
title: 在 Azure Migrate 中使用组依赖项映射优化评估组 | Microsoft 文档
description: 介绍如何在 Azure Migrate 服务中使用组依赖项映射优化评估。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: a91543dcea1fb9fb6a33b5a7bc3892a2bac7b241
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215766"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>使用组依赖项映射优化组

本文介绍如何通过可视化组中所有计算机的依赖项优化组。 如果想要在运行评估之前通过交叉检查组依赖项来优化现有组的成员身份，通常都会使用此方法。 通过依赖项可视化来优化组有助于有效地计划如何迁移到 Azure。可以发现所有需要一起迁移的互相依赖的系统。 它帮助确保在迁移到 Azure 的过程中不会遗留任何内容，也不会发生意外中断。 


> [!NOTE]
> 要可视化其依赖项的组所包含的计算机不应超过 10 台。 如果组中所包含的计算机超过 10 台，建议将该组拆分为更小的组，以便利用依赖项可视化功能。


# <a name="prepare-the-group-for-dependency-visualization"></a>为依赖项可视化准备组
要查看组的依赖项，需要在组中包含的每台本地计算机上下载并安装代理。 此外，如果你的计算机未连接到 Internet，你需要在计算机上下载并安装 [OMS 网关](../log-analytics/log-analytics-oms-gateway.md)。

### <a name="download-and-install-the-vm-agents"></a>下载并安装 VM 代理
1. 在“概述”中，单击“管理” > “组”，转到所需组。
2. 在计算机列表中，单击“依赖项代理”列中的“需要安装”，查看有关如何下载并安装代理的说明。
3. 在组中包含的每台 VM 上，从“依赖项”页下载并安装 Microsoft Monitoring Agent (MMA) 和依赖项代理。
4. 复制工作区 ID 和密钥。 在本地计算机上安装 MMA 时需要这些信息。

### <a name="install-the-mma"></a>安装 MMA

若要在 Windows 计算机上安装代理：

1. 双击下载的代理。
2. 在“欢迎”页面上，单击“下一步”。 在“许可条款”页面上，单击“我接受”以接受许可协议。
3. 在“目标文件夹”中，保留或修改默认安装文件夹，然后选择“下一步”。 
4. 在“代理安装选项”中，选择“Azure Log Analytics” > “下一步”。 
5. 单击“添加”以添加 Log Analytics 工作区。 粘贴从门户复制的工作区 ID 和密钥。 单击“资源组名称” 的 Azure 数据工厂。


若要在 Linux 计算机上安装代理：

1. 使用 scp/sftp 将相应的捆绑包（x86 或 x64）传输到 Linux 计算机。
2. 使用 --install 参数安装捆绑包。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>安装依赖项代理
1. 若要在 Windows 计算机上安装依赖项代理，请双击安装程序文件，然后按照向导操作。
2. 若要在 Linux 计算机上安装依赖项代理，请使用以下命令安装为根目录：

    ```sh InstallDependencyAgent-Linux64.bin```

[详细了解](../monitoring/monitoring-service-map-configure.md#supported-operating-systems)依赖项代理支持的操作系统。 

## <a name="refine-the-group-based-on-dependency-visualization"></a>通过依赖项可视化优化组
在组中的所有计算机上都安装代理后，可以可视化该组的依赖项，并通过执行以下步骤优化该组。

1. 在 Azure Migrate 项目中，在 **“管理”** 下，单击 **“组”**，然后选择组。
2. 在组页面上，单击 **“查看依赖项”**，打开组依赖项映射。
3. 组的依赖项映射显示了以下详细信息：
    - 与组中包含的所有计算机的入站（客户端）和出站（服务器）TCP 连接
        - 没有安装 MMA 和依赖项代理的依赖计算机按端口号进行分组
        - 安装了 MMA 和依赖项代理的依赖计算机显示在单独的框中 
    - 计算机内运行的进程，可以展开每个计算机框查看这些进程
    - 每台计算机的属性（如，完全限定的域名、操作系统、MAC 地址等），可单击每个计算机框查看相关详细信息

     ![查看组依赖项](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. 若要查看更精细的依赖项，请单击时间范围并进行修改。 时间范围默认为 1 小时。 你可以修改时间范围，或指定开始和结束日期以及持续时间。
4. 验证依赖计算机，以及每台计算机内运行的进程，标识应在组中添加或删除的计算机。
5. 按住 Ctrl 键单击，在映射上选择想要在组中添加或删除的计算机。
    - 你只能添加已发现的计算机。
    - 在组中添加和删除计算机会使过去对此组的评估失效。
    - 修改组时，你可以视情况创建新的评估。
5. 单击“确定”以保存组。

    ![添加或删除计算机](./media/how-to-create-group-dependencies/add-remove.png)

如果你想要检查显示在组依赖项映射中的特定计算机的依赖项，请[设置计算机依赖项映射](how-to-create-group-machine-dependencies.md)。


## <a name="next-steps"></a>后续步骤

[详细了解](concepts-assessment-calculation.md)如何计算评估。
