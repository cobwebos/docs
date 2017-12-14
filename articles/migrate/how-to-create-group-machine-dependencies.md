---
title: "在 Azure Migrate 中使用计算机依赖项分组计算机 | Microsoft 文档"
description: "介绍了如何在 Azure Migrate 服务中使用计算机依赖项创建评估。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 769c05916de4e7ad5b14812c2c8dbcf69e91320c
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2017
---
# <a name="group-machines-using-machine-dependency-mapping"></a>使用计算机依赖项映射分组计算机

本文介绍如何创建计算机组以便使用计算机依赖项映射进行 [Azure Migrate](migrate-overview.md) 评估。 当你想要在运行评估之前通过交叉检查计算机依赖项评估可信度较高的 VM 组时，通常都会使用此方法。



## <a name="prepare-machines-for-dependency-mapping"></a>准备计算机以进行依赖项映射
若要在依赖项映射中包括计算机，你需要在要评估的每台本地计算机上下载并安装代理。 此外，如果你的计算机未连接到 Internet，你需要在计算机上下载并安装 [OMS 网关](../log-analytics/log-analytics-oms-gateway.md)。

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
5. 单击“添加”以添加新 OMS 工作区。 粘贴从门户复制的工作区 ID 和密钥。 单击“下一步”。


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
2. “依赖项”列现在应显示为“查看依赖项”。 单击该列以查看依赖项。
3. 对于每个计算机，你可以验证以下信息：
    - 是否已安装 MMA 和依赖项代理，并且是否已发现计算机。
    - 在计算机上运行的来宾操作系统。
    - 入站和出站 IP 连接和端口。
    - 在计算机上运行的进程。
    - 计算机之间的依赖项。

4. 若要查看更精细的依赖项，请单击时间范围并进行修改。 时间范围默认为 1 小时。 你可以修改时间范围，或指定开始和结束日期以及持续时间。
5. 确定想要分成一组的依赖计算机之后，在映射上选择计算机，然后单击“分组计算机”。
6. 指定组名。 确认 Azure Migrate 已发现计算机。 如果尚未发现，请再次运行本地发现进程。 你可以根据需要立即运行评估。
7. 单击“确定”以保存组。

    ![使用计算机依赖项创建组](./media/how-to-create-group-machine-dependencies/create-group.png)

## <a name="next-steps"></a>后续步骤

- [了解如何](how-to-create-group-dependencies.md)通过检查组依赖项优化组
- [详细了解](concepts-assessment-calculation.md)如何计算评估。
