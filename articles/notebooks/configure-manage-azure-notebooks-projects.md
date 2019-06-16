---
title: 配置和管理 Azure Notebooks 项目
description: 如何通过 Azure Notebooks UI 和直接终端访问管理项目元数据、项目文件、项目的环境和设置步骤。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 35dd6ff1-a14a-4a2e-b173-6d8467de3e89
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/13/2019
ms.author: kraigb
ms.openlocfilehash: 0440e498451ee141fa03851b78418caf911d0e32
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596736"
---
# <a name="manage-and-configure-projects"></a>管理和配置项目

Azure Notebooks 中的项目实质上是运行 Jupyter 笔记本的基础 Linux 虚拟机的一项配置，以及一个文件夹和一些描述性元数据。 Azure Notebooks 中的项目仪表板，可通过它管理文件和配置项目的特性：

- 项目运行的计算层，可以是免费层或 Azure 虚拟机。
- 项目元数据，其中包括名称、说明、共享项目时使用的标识符以及说明项目是公共项目还是专用项目的信息。
- 管理项目笔记本、数据和其他文件的方式与其他文件系统中的管理方式相同。
- 配置项目的环境有两种方式：通过启动脚本配置，或直接通过终端配置。
- 通过终端访问的日志。

> [!Note]
> 此处描述的管理和配置功能仅适用于最初创建项目的项目所有者。 但是，可以将项目克隆到你自己的帐户中，在这种情况下，你将成为所有者并可以根据需要配置项目。

每当你运行笔记本或其他文件时，Azure Notebooks 都会启动基础虚拟机。 服务器自动保存文件并在处于非活动状态 60 分钟后关闭。 你还可以随时使用“关闭”命令（键盘快捷方式：h）停止服务器  。

## <a name="compute-tier"></a>计算层

默认情况下，项目运行**免费计算**层，限制为 4 GB 的内存和 1 GB 的数据，以防止滥用。 可以绕过这些限制，并通过使用不同的虚拟机的 Azure 订阅中已预配提高计算能力。 有关详细信息，请参阅[如何使用数据科学虚拟机](use-data-science-virtual-machine.md)。

## <a name="edit-project-metadata"></a>编辑项目元数据

在项目面板中，选择“项目设置”，再选择“信息”选项卡，该选项卡包含下表所含的项目元数据   。 可随时更改项目元数据。

| 设置 | 描述 |
| --- | --- |
| 项目名称 | 项目的友好名称，Azure Notebooks 显示该名称。 例如“Python 中的 Hello World”。 |
| 项目 ID | 自定义标识符，是用于共享项目的 URL 的一部分。 此 ID 可以使用字母、 数字和连字符，限制为 30 个字符，且不能为[保留项目 ID](create-clone-jupyter-notebooks.md#reserved-project-ids)。 如果不确定使用什么内容，可按惯例使用小写的项目名且将空格转换为连字符，例如“my-notebook-project”（根据需要截断，以满足长度限制）。 |
| 公共项目 | 如果设置为公共项目，有链接的任何人均可访客项目。 创建专用项目时，请清除此选项。 |
| 隐藏克隆 | 如果设置了此选项，其他用户无法看到该项目的克隆的列表。 对于与不属于同一个组织的许多人共享（例如，在教学过程中使用笔记本）的项目，隐藏克隆十分有用。 |

> [!Important]
>
> 更改项目 ID 会造成之前可能已共享的项目的所有链接无效。

## <a name="manage-project-files"></a>管理项目文件

项目面板显示项目文件夹系统的内容。 可以使用各种命令来管理这些文件。

### <a name="create-new-files-and-folders"></a>新建文件和文件夹

“+ 新建”命令（键盘快捷方式：n）用于创建新的文件或文件夹  。 使用该命令时，首先选择要创建的项的类型：

| 项类型 | 描述 | 命令行为 |
| --- | --- | --- |
| **笔记本** | Jupyter 笔记本 | 显示一个弹出窗口，在其中指定笔记本的文件名和语言。 |
| **文件夹** | 子文件夹 | 在项目的文件列表中创建一个编辑字段，用于输入文件夹名称。 |
| **空白文件** | 可供存储文本、数据等任何内容的文件。 | 在项目的文件列表中创建一个编辑字段，用于输入文件名。 |
| **Markdown** | Markdown 文件。 | 在项目的文件列表中创建一个编辑字段，用于输入文件名。 |

### <a name="upload-files"></a>上传文件

“上传”命令为从其他位置导入数据提供两个选择  ：“从 URL 导入”以及“从计算机导入”   。 有关详细信息，请参阅[使用 Azure Notebook 项目中的数据文件](work-with-project-data-files.md)。

### <a name="select-file-specific-commands"></a>选择特定于文件的命令

项目文件列表中个各项均在右键单击时显示的上下文菜单中提供命令：

![文件上下文菜单上的命令](media/project-file-commands.png)

| 命令 | 键盘快捷键 | 操作 |
| --- | --- | --- |
| 运行 | r（或单击） | 运行笔记本文件。 其他文件类型打开供查看。  |
| 复制链接 | y | 将文件链接复制到剪贴板。 |
| 在 Jupyter 实验室中运行 | j | 在 Jupyter 实验室中运行笔记本，比起 Jupyter 通常提供的界面而言，该界面更像是面向开发者的界面。 |
| 预览 | p | 打开文件 HTML 格式的预览；对于笔记本，则以只读形式呈现笔记本。 有关详细信息，请参阅[预览](#preview)部分。 |
| 编辑文件 | i | 打开文件进行编辑。 |
| 下载 | d | 下载 zip 文件，它包含文件夹的文件或内容。 |
| 重命名 | a | 提示输入文件或文件夹的新名称。 |
| DELETE | x | 提示确认，然后从项目中永久地删除文件。 删除不可撤消。 |
| 移动 | m | 将文件移到同一项目的其他文件夹中。 |

#### <a name="preview"></a>预览

文件或笔记本的预览是内容的只读视图；禁用运行笔记本单元这一项功能。 拥有文件或笔记本的链接，但尚未登录 Azure Notebooks 的任何人均能查看预览。 登录后，用户可以将笔记本克隆到自己的帐户，或者将其下载到自己的本地计算机上。

预览页支持以下几个具有键盘快捷方式的工具栏命令：

| 命令 | 键盘快捷键 | 操作 |
| --- | --- | --- |
| 共享 | s | 显示共享弹出式菜单，可从中获取链接、共享至社交媒体、获取用于嵌入的 HTML 以及发送电子邮件。 |
| 克隆 | c  | 将笔记本克隆到你的帐户。 |
| 运行 | r | 运行笔记本（假如你有权执行此操作）。 |
| 下载 | d | 下载笔记本的副本。 |

## <a name="configure-the-project-environment"></a>配置项目环境

有三种方法可配置供运行笔记本的基础虚拟机的环境：

- 纳入一次性的初始化脚本
- 使用项目的环境设置来指定设置步骤
- 通过终端访问虚拟机。

无论何时启动虚拟机，均应用项目配置的所有窗体，这会影响到项目中的所有笔记本。

### <a name="one-time-initialization-script"></a>一次性的初始化脚本

Azure Notebooks 首次为项目创建服务器时，它会在项目中查找一个名为“aznbsetup.sh”的文件  。如果该文件存在，Azure Notebooks 会运行它。 该脚本的输出作为“.aznbsetup.log”存储在你的项目文件夹中  。

### <a name="environment-setup-steps"></a>环境设置步骤

你可以使用项目的环境设置来创建配置环境的各个步骤。

在项目仪表板上，选择“项目设置”，再选择“环境”选项卡，在该选项卡中添加、删除和修改项目的设置步骤   ：

![选中了“环境”选项卡的项目设置弹出式对话框](media/project-settings-environment-steps.png)

若要添加步骤，先选择“+ 添加”，再在“操作”下拉列表中选择一个步骤类型   ：

![新环境设置步骤的操作选择器](media/project-settings-environment-details.png)

稍后呈现的信息取决于你所选的操作类型：

- **Requirements.txt**：在第二个下拉列表中，选择项目中已有的一个 *requirements.txt* 文件。 然后从显示的第三个下拉列表中选择 Python 版本。 若使用 requirements.txt 文件，Azure Notebooks 会在启动笔记本服务器时通过 requirements.txt 运行 `pip install -r`   。 不需要在笔记本内显式安装程序包。

- **Shell 脚本**：在第二个下拉列表中，选择项目中的一个 bash shell 脚本（通常为扩展名为 .sh 的文件），该脚本包含你希望为初始化环境而运行的所有命令  。

- **Environment.yml**：在第二个下拉列表中，为使用 conda 环境的 Python 项目选择 environments.yml 文件  。

完成添加步骤后，选择“保存”  。

### <a name="use-the-terminal"></a>使用终端

终端命令位于项目仪表板上，用于打开 Linux 终端，使你直接访问服务器  。 可以在终端内下载数据、编辑或管理文件、检查进程，甚至使用 vi 和 nano 等工具。

> [!Note]
> 如果项目环境中有启动脚本，打开终端可能会显示一则消息，指示仍在进行设置。

可以在终端中发出任何标准 Linux 命令。 还可以在主文件夹中使用 `ls` 查看虚拟机上存在的不同环境（例如 anaconda2_501、anaconda3_420、anaconda3_501、IfSharp 和 R）以及含有以下项目的项目文件夹       ：

![Azure Notebooks 中的项目终端](media/project-terminal.png)

若要影响特定的环境，首先将目录切换到该环境文件夹。

就 Python 环境而言，可以在各个环境的 bin 文件夹中找到 `pip` 和 `conda`  。 还可以为环境使用内置的别名：

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

对服务器所做的更改仅应用于当前会话，你在项目文件夹内创建的文件和文件夹除外  。 例如，会在在会话之间保存对项目文件夹中的文件的编辑，但不保存使用 `pip install` 的程序包。

> [!Note]
> 如果使用的是 `python` 或 `python3`则调用系统安装的 Python 版本，这些版本不用于笔记本。 你没有 `pip install` 等操作的权限，因此务必使用特定于版本的别名。

## <a name="access-notebook-logs"></a>访问笔记本日志

如果在运行笔记本时遇到问题，来自 Jupyter 的输出会存储在名为“.nb.log”的文件夹中  。 可以通过终端命令或项目仪表板访问这些日志  。

通常在本地运行 Jupyter 时，你可能已从终端窗口启动了它。 终端窗口显示内核状态等输出。

在终端中使用以下命令查看日志：

```bash
cat .nb.log
```

还可以从 Python 笔记本中的代码单元使用该命令：

```bash
!cat .nb.log
```

## <a name="next-steps"></a>后续步骤

- [如何：使用项目数据文件](work-with-project-data-files.md)
- [在笔记本中访问云数据](access-data-resources-jupyter-notebooks.md)
