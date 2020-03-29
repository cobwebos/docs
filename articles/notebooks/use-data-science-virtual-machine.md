---
title: 使用 Azure 数据科学虚拟机
description: 了解如何连接到 Azure 数据科学虚拟机 （DSVM） 以扩展 Azure 笔记本预览版可用的计算能力。
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: b4da63b7b2a6da4316215b85a09ca7420745251c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898407"
---
# <a name="use-azure-data-science-virtual-machines"></a>使用 Azure 数据科学虚拟机

默认情况下，项目在**免费计算**层上运行，该层限制为 4 GB 内存和 1 GB 的数据，以防止滥用。 可以使用在 Azure 订阅中预配的其他虚拟机来绕过这些限制。 为此，最佳选择是使用**数据科学虚拟机（Ubuntu）映像的**Azure 数据科学虚拟机 （DSVM）。 此类 DSVM 预先配置了 Azure 笔记本所需的一切，并自动显示在 Azure 笔记本中的 **"运行**"下拉列表中。

> [!Note]
> Azure 笔记本仅在使用 Linux Ubuntu 映像创建的 DSVM 上受支持。 Windows 2012、Windows 2016 或 Linux CentOS 映像不支持笔记本。

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-dsvm-instance"></a>创建 DSVM 实例

要创建新的 DSVM 实例，请按照[创建 Ubuntu Data Science VM](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)上的说明进行操作。 有关详细信息（包括定价详细信息），请参阅[数据科学虚拟机](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a name="connect-to-the-dsvm"></a>连接到 DSVM

创建 DSVM 后，在 Azure 笔记本项目仪表板上选择 **"运行**下拉列表"，然后选择相应的 DSVM 实例。 如果以下条件为 true，下拉列表将显示 DSVM 实例：

- 使用 Azure Active Directory (AAD) 帐户（例如公司帐户）登录 Azure Notebooks。
- 你的帐户已连接到 Azure 订阅。
- 该订阅中至少有一个或多个虚拟机，至少具有读取器访问权限，这些虚拟机使用 Linux 数据科学虚拟机 （Ubuntu） 映像。

![Data Science Virtual Machine 实例位于项目仪表板的下拉列表中](media/project-compute-tier-dsvm.png)

选择 DSVM 实例时，Azure Notebooks 可能会提示输入创建 VM 时使用的特定计算机凭据。

> [!Important]
> 用户名必须小写，才能将其与 JupyterHub 一起使用。

如果未满足任何条件，您仍然可以连接到 DSVM。 在下拉列表中，选择 **"直接计算"** 选项，该选项会提示您输入名称（显示在列表中）、VM 的 IP 地址和端口（通常为 8000，JupyterHub 侦听的默认端口）和 VM 凭据：

![提示收集“直接计算”选项的服务器信息](media/project-compute-tier-direct.png)

从 Azure 门户中的 DSVM 页获取这些值。

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>从 DSVM 访问 Azure 笔记本文件

DSVM 版本 19.06.15 或更高版本支持文件系统访问。 要检查版本，请首先通过 SSH 连接到 DSVM，然后运行以下命令：（`curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"`您必须使用此处显示的确切 IP 地址）。 版本号显示在"版本"的输出中。

要使用**自由计算**层保留文件路径的奇偶校验，您可以在 DSVM 上一次只打开一个项目。 要打开新项目，必须首先关闭打开的项目。

在 VM 上运行项目时，文件将安装在 Jupyter 服务器（JupyterHub 中显示的目录）的根目录中，替换默认的 Azure 笔记本文件。 使用笔记本 UI 上的 **"关闭"** 按钮关闭 VM 时，Azure 笔记本将恢复默认文件。

![Azure 笔记本中的关机按钮](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>创建新的 DSVM 用户

如果多个用户共享 DSVM，则可以通过为每个笔记本用户创建和使用 DSVM 用户来避免相互阻塞：

1. 在[Azure 门户](https://portal.azure.com)上，导航到虚拟机。
1. 在左边距中的 **"支持 + 故障排除"** 下，选择 **"重置密码**"。
1. 输入新的**用户名**。 用户名必须小写，才能将其与 JupyterHub 一起使用。 输入密码。 然后选择“更新”。**** （现有用户名不受影响。
1. 对任何其他用户重复上一步。

## <a name="next-steps"></a>后续步骤

了解有关[Azure 数据科学虚拟机简介的](/azure/machine-learning/data-science-virtual-machine/overview)DSVM 的更多详细信息。
