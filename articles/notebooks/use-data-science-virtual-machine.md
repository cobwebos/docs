---
title: 使用 Azure 数据科学虚拟机
description: 连接到 Azure 数据科学虚拟机 (DSVM) 扩展到 Azure 笔记本的可用的计算能力。
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: getroyer
ms.openlocfilehash: d4321fe60d47bf942e2c413ba81c2c9f54317fd1
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597861"
---
# <a name="use-azure-data-science-virtual-machines"></a>使用 Azure 数据科学虚拟机

默认情况下，项目运行**免费计算**层，限制为 4 GB 的内存和 1 GB 的数据，以防止滥用。 可以使用在 Azure 订阅中预配的其他虚拟机来绕过这些限制。 为此，最好的选择是将 Azure 数据科学虚拟机 (DSVM) 使用**适用于 Linux (Ubuntu) 的数据科学虚拟机**映像。 此类 dsvm 预先配置的所有内容需要适用于 Azure Notebook，显示上自动**运行**Azure 笔记本中的下拉列表。

> [!Note]
> Azure Notebook 是仅支持使用 on Linux Ubuntu 映像创建 Dsvm。 Windows 2012 和 Windows 2016 或 Linux CentOS 映像不支持笔记本。

## <a name="create-a-dsvm-instance"></a>创建 DSVM 实例

要创建新的 DSVM 实例，请按照[创建 Ubuntu Data Science VM](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)上的说明进行操作。 有关详细信息，包括定价详细信息，请参阅[数据科学虚拟机](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a name="connect-to-the-dsvm"></a>连接到 DSVM

正在创建 DSVM，选择**运行**Azure 笔记本上的下拉列表项目仪表板中，并选择相应的 DSVM 实例。 下拉列表显示了是否以下条件成立的多个 DSVM 实例：

- 使用 Azure Active Directory (AAD) 帐户（例如公司帐户）登录 Azure Notebooks。
- 你的帐户已连接到 Azure 订阅。
- 您在该订阅中的一个或多个虚拟机与至少读取者访问权限，数据科学虚拟机用于 Linux (Ubuntu) 映像。)

![Data Science Virtual Machine 实例位于项目仪表板的下拉列表中](media/project-compute-tier-dsvm.png)

选择 DSVM 实例时，Azure Notebooks 可能会提示输入创建 VM 时使用的特定计算机凭据。

如果不满足任何条件，你仍可连接到 DSVM。 在下拉列表中，选择**直接计算**选项，将提示您输入的名称 （若要显示在列表中）、 VM 的 IP 地址和端口 (通常 8000，到哪些 JupyterHub 侦听默认端口) 和 VM 凭据：

![提示收集“直接计算”选项的服务器信息](media/project-compute-tier-direct.png)

从 Azure 门户中的 DSVM 页面获取这些值。

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>从 DSVM 访问 Azure Notebook 文件

若要保留的文件路径的奇偶校验**免费计算**层中，您将能够在 DSVM 上一次只能打开一个项目。 若要打开一个新的项目，则必须关闭打开的项目第一次。

![Azure Notebook 中的关闭按钮](media/shutdown.png)

当在 VM 上运行项目时，将文件装载到 Jupyter 服务器 （在 JupyterHub 中所示的目录） 的根目录上替换默认 Azure Notebook 文件。 当您关闭的情况下使用 VM**关闭**notebook 用户界面，Azure Notebooks 按钮还原默认的文件。

## <a name="next-steps"></a>后续步骤

了解有关 Dsvm 的详细信息[引入到 Azure 数据科学虚拟机](/machine-learning/data-science-virtual-machine/overview)。
