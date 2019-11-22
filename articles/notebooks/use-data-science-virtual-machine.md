---
title: 使用 Azure 数据科学虚拟机
description: 连接到 Azure Data Science Virtual Machine （DSVM）以将计算能力扩展到 Azure Notebooks。
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: article
ms.date: 06/13/2019
ms.openlocfilehash: 0559442cb3f6c3937e6930082940e9354c43bc30
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277403"
---
# <a name="use-azure-data-science-virtual-machines"></a>使用 Azure 数据科学虚拟机

默认情况下，项目在**免费计算**层上运行，其限制为 4 gb 的内存和 1 GB 的数据，以防止滥用。 可以使用在 Azure 订阅中预配的其他虚拟机来绕过这些限制。 为此，最佳选择是使用**Linux （Ubuntu）映像 Data Science Virtual Machine**的 Azure DATA SCIENCE VIRTUAL MACHINE （DSVM）。 此类 DSVM 预配置了 Azure Notebooks 所需的一切，并自动显示在 Azure Notebooks 的 "**运行**" 下拉列表中。

> [!Note]
> 仅在用 Linux Ubuntu 映像上创建的 Dsvm 支持 Azure Notebooks。 笔记本在 Windows 2012、Windows 2016 或 Linux CentOS 映像上不受支持。

## <a name="create-a-dsvm-instance"></a>创建 DSVM 实例

要创建新的 DSVM 实例，请按照[创建 Ubuntu Data Science VM](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)上的说明进行操作。 有关详细信息（包括定价详细信息），请参阅[数据科学虚拟机](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a name="connect-to-the-dsvm"></a>连接到 DSVM

创建 DSVM 后，选择 "Azure Notebooks 项目" 仪表板上的 "**运行**" 下拉列表，然后选择相应的 DSVM 实例。 如果满足以下条件，则下拉列表将显示 DSVM 实例：

- 使用 Azure Active Directory (AAD) 帐户（例如公司帐户）登录 Azure Notebooks。
- 你的帐户已连接到 Azure 订阅。
- 此订阅中有一个或多个虚拟机（至少具有读取者访问权限），该订阅使用适用于 Linux （Ubuntu）映像的 Data Science Virtual Machine。）

![Data Science Virtual Machine 实例位于项目仪表板的下拉列表中](media/project-compute-tier-dsvm.png)

选择 DSVM 实例时，Azure Notebooks 可能会提示输入创建 VM 时使用的特定计算机凭据。

如果不满足任何条件，你仍可以连接到 DSVM。 在下拉列表中，选择 "**直接计算**" 选项，该选项将提示你输入名称（在列表中显示）、VM 的 IP 地址和端口（通常为8000，JupyterHub 侦听的默认端口）和 VM 凭据：

![提示收集“直接计算”选项的服务器信息](media/project-compute-tier-direct.png)

可以从 Azure 门户的 "DSVM" 页中获取这些值。

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>从 DSVM 访问 Azure Notebooks 文件

DSVM 版本19.06.15 或更高版本支持文件系统访问。 若要检查版本，请首先通过 SSH 连接到 DSVM，并运行以下命令： `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` （必须使用此处所示的确切 IP 地址）。 版本号显示在 "版本" 的输出中。

若要保留具有**免费计算**层的文件路径的奇偶校验，只能在 DSVM 上一次打开一个项目。 若要打开新项目，必须先关闭打开的项目。

当在 VM 上运行项目时，这些文件将装载到 Jupyter 服务器的根目录下（在 JupyterHub 中显示的目录），并替换默认的 Azure Notebooks 文件。 使用笔记本 UI 上的 "**关闭**" 按钮关闭 VM 时，Azure Notebooks 会还原默认文件。

![Azure Notebooks 中的 "关闭" 按钮](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>创建新的 DSVM 用户

如果多个用户共享了 DSVM，则可以通过为每个笔记本用户创建和使用 DSVM 用户，避免彼此阻止：

1. 在[Azure 门户](https://portal.azure.com)中，导航到你的虚拟机。
1. 在左边距中的 "**支持 + 故障排除**" 下，选择 "**重置密码**"。
1. 输入新的用户名和密码，然后选择 "**更新**"。 （现有的用户名不受影响。）
1. 对任何其他用户重复前面的步骤。

## <a name="next-steps"></a>后续步骤

详细了解[Azure 数据科学虚拟机简介](/azure/machine-learning/data-science-virtual-machine/overview)中的 dsvm。
