---
title: "在 Azure Migrate 中使用计算机依赖项分组计算机 | Microsoft 文档"
description: "介绍了如何在 Azure Migrate 服务中使用计算机依赖项创建评估。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/25/2017
ms.author: raynew
ms.openlocfilehash: 720380fd14d9eaf4856ad75269a80f2b63a4725f
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="group-machines-using-machine-dependency-mapping"></a>使用计算机依赖项映射分组计算机

本文介绍如何通过可视化计算机的依赖项为 [Azure Migrate](migrate-overview.md) 评估创建计算机组。 当你想要在运行评估之前通过交叉检查计算机依赖项评估可信度较高的 VM 组时，通常都会使用此方法。 依赖项可视化有助于有效地计划如何迁移到 Azure。 它帮助确保在迁移到 Azure 的过程中不会遗留任何内容，也不会发生意外中断。 可以发现所有需要一起迁移的互相依赖的系统，并识别运行中的系统仍然为用户提供服务还是在等待解除授权而非迁移。 


## <a name="prepare-machines-for-dependency-mapping"></a>准备计算机以进行依赖项映射
要查看计算机的依赖项，需要在想要评估的每台本地计算机上下载并安装代理。 此外，如果你的计算机未连接到 Internet，你需要在计算机上下载并安装 [OMS 网关](../log-analytics/log-analytics-oms-gateway.md)。

### <a name="download-and-install-the-vm-agents"></a>下载并安装 VM 代理
1. 在“概述”中，单击“管理” > “计算机”，然后选择所需的计算机。
2. 在“依赖项”列中，单击“安装代理”。 
3. 在要评估的每台 VM 上，从“依赖项”页下载并安装 Microsoft 监视代理 (MMA) 和依赖项代理。
4. 复制工作区 ID 和密钥。 在本地计算机上安装 MMA 时将需要这些信息。

### <a name="install-the-mma"></a>安装 MMA

若要在 Windows 计算机上安装代理：

1. 双击下载的代理。
2. 在“欢迎”页面上，单击“下一步”。 在“许可条款”页面上，单击“我接受”以接受许可协议。
3. 在“目标文件夹”中，保留或修改默认安装文件夹，然后选择“下一步”。 
4. 在“代理安装选项”中，选择“Azure Log Analytics (OMS)” > “下一步”。 
5. 单击“添加”以添加新 OMS 工作区。 粘贴从门户复制的工作区 ID 和密钥。 单击“资源组名称” 的 Azure 数据工厂。


若要在 Linux 计算机上安装代理：

1. 使用 scp/sftp 将相应的捆绑包（x86 或 x64）传输到 Linux 计算机。
2. 使用 --install 参数安装捆绑包。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>安装依赖项代理
1. 若要在 Windows 计算机上安装依赖项代理，请双击安装程序文件，然后按照向导操作。
2. 若要在 Linux 计算机上安装依赖项代理，请使用以下命令安装为根目录：

    ```sh InstallDependencyAgent-Linux64.bin```

[详细了解](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems)依赖项代理支持的操作系统。 

## <a name="create-a-group"></a>创建组

1. 安装代理后，请转到门户并单击“管理” > “计算机”。
2. 搜索在其中安装代理的计算机。
3. 计算机的“依赖项”列现在应显示为“查看依赖项”。 单击该列，查看计算机的依赖项。
4. 计算机的依赖项映射显示以下详细信息：
    - 与计算机的入站（客户端）和出站（服务器）TCP 连接
        - 没有安装 MMA 和依赖项代理的依赖计算机按端口号进行分组
        - 安装了 MMA 和依赖项代理的依赖计算机显示在单独的框中 
    - 计算机内运行的进程，可以展开每个计算机框查看这些进程
    - 每台计算机的属性（如，完全限定的域名、操作系统、MAC 地址等），可单击每个计算机框查看相关详细信息

 ![查看计算机依赖项](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. 可通过单击时间范围标签中的持续时间，查看不同持续时间的依赖项。 时间范围默认为 1 小时。 你可以修改时间范围，或指定开始和结束日期以及持续时间。
5. 确定想要分成一组的依赖计算机之后，按住 Ctrl 键单击，在映射上选择多台计算机，然后单击“分组计算机”。
6. 指定组名。 确认 Azure Migrate 已发现依赖计算机。 

    > [!NOTE]
    > 如果 Azure Migrate 没有发现依赖计算机，则无法将其添加到组中。 要将此类计算机添加到组中，需要再次在 vCenter Server 的正确范围内运行发现进程，确保 Azure Migrate 发现该计算机。  

7. 如果想要为该组创建评估，请选中相应复选框，为该组创建新的评估。
8. 单击“确定”以保存组。

创建组后，建议在该组中的所有计算机上安装代理，然后通过可视化整个组的依赖项优化该组。

## <a name="next-steps"></a>后续步骤

- [了解如何](how-to-create-group-dependencies.md)通过可视化组依赖项优化该组
- [详细了解](concepts-assessment-calculation.md)如何计算评估。
