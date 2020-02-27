---
title: 什么是 Azure Data Science Virtual Machine？
titleSuffix: Azure Data Science Virtual Machine
description: 适用于 Windows 和 Linux 数据科学虚拟机的关键分析方案和组件。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 12/31/2019
ms.openlocfilehash: ea53ee3f3c1d39652982b6343b1e634dac1fbc75
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525917"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>什么是适用于 Linux 和 Windows 的 Azure Data Science Virtual Machine？

Data Science Virtual Machine (DSVM) 是专为开展数据科学构建的 Azure 云平台上的自定义 VM 映像。 它预装并预配了许多热门数据科学工具，可为高级分析快速生成智能应用程序。 

DSVM 在以下环境中可用：
+ **Windows Server 2019（预览版）**
+ **Ubuntu 18.04 LTS（预览版）**
+ Windows Server 2016
+ Ubuntu 16.04 LTS 和 CentOS 7.4


> [!NOTE]
> 用于深度学习的所有 VM 工具都已装入到 Data Science Virtual Machine 中。 


## <a name="why-choose-the-dsvm"></a>为何选择 DSVM？
Data Science Virtual Machine 的目标在于向所有技能级别和各个行业的数据专业人员提供无摩擦、预配置的数据科学环境。 可以预配一个 DSVM，而无需自行部署一个与之相当的工作区。 这种做法可以在安装、配置和包管理方面节省数天甚至数周的时间。  分配 DSVM 后，可以立即开始处理数据科学项目。

## <a name="sample-use-cases"></a>示例用例

下面演示了 DSVM 客户的一些常见用例。

### <a name="moving-data-science-workloads-to-the-cloud"></a>将数据科学工作负荷迁移到云

DSVM 为数据科学团队提供基线配置，让他们将本地桌面替换为托管云桌面，并确保团队中的所有数据科学家可以使用一致的设置来验证试验和促进协作。 它还通过减轻系统管理员负担来降低成本。 这种减轻负担可以节省评估、安装和维护高级分析软件包所需的时间。

### <a name="data-science-training-and-education"></a>数据科学训练和培训
教授数据科学课程的企业培训师和教师通常提供虚拟机映像。 该映像确保学员具有一致的设置且示例以可预测方式工作。 

DSVM 创建可缓解支持和不兼容性挑战的一致设置的按需环境。 这些环境需要频繁生成，特别是短期培训课程的情况从中获益极大。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>对于大型项目的按需弹性容量
数据科学编程马拉松/竞赛或大型数据建模和浏览需要扩展的硬件容量，通常持续时间比较短。 DSVM 有助于根据需要在允许运行高性能计算资源试验的扩展服务器上快速复制数据科学环境。

### <a name="custom-compute-power-for-azure-notebooks"></a>自定义 Azure Notebooks 的计算能力
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) 是一项免费的托管服务，用于在云中开发、运行和共享 Jupyter 笔记本而无需进行安装。 免费服务层限制为 4 GB 内存和 1 GB 数据。 

若要放开所有限制，可以将 Notebooks 项目附加到 DSVM 或任何其他运行 Jupyter 服务器的 VM。 如果你经由使用 Azure Active Directory 的帐户（例如公司帐户）登录到 Azure Notebooks，则 Notebooks 会自动在与该帐户关联的任意订阅中显示 DSVM。 可以[将 DSVM 附加到 Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) 以扩展可用计算能力。

### <a name="short-term-experimentation-and-evaluation"></a>短期实验和评估
可以使用 DSVM，专门参考我们发布的一些[示例和演练](./dsvm-samples-and-walkthroughs.md)来评估或学习新的数据科学[工具](./tools-included.md)。


### <a name="deep-learning-with-gpus"></a>使用 GPU 进行深度学习
在 DSVM 中，训练模型可以使用基于图形处理单元 (GPU) 的硬件上的深度学习算法。 利用 Azure 平台的 VM 缩放功能，DSVM 可帮助根据需要在云中使用基于 GPU 的硬件。 若要训练大型模型或者在保留相同 OS 磁盘的同时进行高速计算，可以切换到基于 GPU 的 VM。 可以在 DSVM 中选择启用了 N 系列 GPU 的任意虚拟机 SKU。 请注意，Azure 免费帐户不支持启用 GPU 的虚拟机 SKU。

Windows Server 2016 版本的 DSVM 预安装了 GPU 驱动程序、框架和 GPU 版本的深度学习框架。 在 Linux 版上，在 CentOS 和 Ubuntu DSVM 中都启用了 GPU 上的深度学习。 

还可以将 Ubuntu、CentOS 或 Windows 2016 版的 DSVM 部署到非基于 GPU 的 Azure 虚拟机。 在这种情况下，所有深度学习框架都将回退到 CPU 模式。
 
[详细了解可用的深度学习和 AI 框架](dsvm-tools-deep-learning-frameworks.md)。

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>DSVM 中包含哪些组件？

在[此处](tools-included.md)查看 Windows 和 Linux DSVM 上的完整工具列表。

## <a name="next-steps"></a>后续步骤

通过以下文章，了解详细信息：

+ Windows：
  + [设置 Windows DSVM](provision-vm.md)
  + [可以在 Windows DSVM 上执行的十项操作](vm-do-ten-things.md)

+ Linux：
  + [设置 Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [设置 Linux DSVM (CentOS)](linux-dsvm-intro.md)
  + [Linux DSVM 上的数据科学](linux-dsvm-walkthrough.md)
