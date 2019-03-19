---
title: 在 Azure 上创建和克隆 Jupyter 笔记本
description: Azure Notebooks 项目管理一系列笔记本和相关文件，你可以新建或从另一个源克隆笔记本和相关文件。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: b99197f44961bdfa23050e9481e290e6c3d24845
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759079"
---
# <a name="create-and-clone-projects"></a>创建和克隆项目

Azure Notebooks 将 Jupyter 笔记本和相关文件组织为称作“项目”的逻辑组。 首先将项目作为容器创建，然后在文件夹内创建或克隆一个或多个笔记本（以及其他项目文件）。 （此过程如[教程](tutorial-create-run-jupyter-notebook.md)中所述。）

项目也保存影响运行笔记本的服务器的元数据和其他配置设置，包括自定义设置步骤和包安装。 有关详细信息，请参阅[管理和配置项目](configure-manage-azure-notebooks-projects.md)。

## <a name="use-the-my-projects-dashboard"></a>使用“我的项目”仪表板

“我的项目”仪表板位于 `https://notebooks.azure.com/<userID>/projects`，用于查看、管理和创建项目：

[![Azure Notebooks 中的“我的项目”仪表板](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

可在仪表板上执行的操作取决于你是否使用拥有用户 ID 的帐户登录：

| 命令 | 适用对象 | 描述 |
| --- | --- | --- |
| **Run** | 所有者 | 启动项目服务器并在 Jupyter 中打开项目文件夹。 （更常见的是：先导航到项目文件夹中，然后从中启动一个笔记本。） |
| **下载** | 所有人 | 将选定项目的副本下载到一个 ZIP 文件中。 |
| **共享** | 所有人 | 显示共享弹出窗口，你可以从中获取选定项目的 URL、共享到社交媒体、发送含该 URL 的电子邮件以及获取“启动笔记本”徽章的 HTML 或 Markdown 代码（参阅[获取启动徽章](#obtain-a-launch-badge)）。 |
| **删除** | 所有者 | 删除选定的项目。 此操作不可撤消。 |
| **终端** | 所有者 | 启动项目服务器，然后打开一个新的浏览器窗口，其中有该服务器的 Bash 终端。 |
| **+ 新建项目** | 所有者 | 新建一个项目。 请参阅[新建项目](#create-a-new-project)。 |
| **上传 GitHub 存储库** | 所有者 | 从 GitHub 导入项目。 [从 GitHub 导入项目](#import-a-project-from-github)。 |
| **克隆** | 所有人 | 将选定项目复制到自己的帐户中。 如果未登录，则提示你登录。 请参阅[克隆项目](#clone-a-project)。 |

### <a name="obtain-a-launch-badge"></a>获取启动徽章

使用“共享”命令再选择“嵌入”选项卡，即可复制创建“启动笔记本”徽章的 HTML 或 Markdown 代码：

![启动笔记本徽章](https://notebooks.azure.com/launch.png)

如果没有 Azure Notebooks 项目，可以使用以下模板，替换相应的用户名和存储库名称，创建一个直接从 GitHub 进行克隆的链接：

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>创建新项目

使用“+ 新建项目”命令时，Azure Notebooks 显示一个“新建项目”弹出窗口。 在其中输入以下信息，然后选择“创建”：

| 字段 | 描述 |
| --- | --- |
| 项目名称 | 项目的友好名称，Azure Notebooks 显示该名称。 例如，"我的笔记本的项目"。 |
| 项目 ID | 自定义标识符，是用于共享项目的 URL 的一部分（形式为 `https://notebooks.azure.com/<user_id>/projects/<project_id>`）。 此 ID 可以使用字母、 数字和连字符，限制为 30 个字符，且不能为[保留项目 ID](#reserved-project-ids)。 如果不确定使用什么内容，可按惯例使用小写的项目名且将空格转换为连字符，例如“my-notebook-project”（根据需要截断，以满足长度限制）。 |
| 公共 | 如果设置为公共项目，有链接的任何人均可访客项目。 创建专用项目时，请清除此选项。 |
| 使用自述文件初始化此项目 | 如果这样设置，会在项目中创建一个默认的“README.md”文件。 README.md 文件用于根据需要提供项目相关文档。 |

### <a name="reserved-project-ids"></a>保留的项目 Id

在以下保留的字不能用作单独项目 Id。 但是，可以这些保留的字用作更长的项目 Id 的一部分。

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| about | 帐户 | 管理 | api | 博客 | 教室 |
| 内容 | 仪表板 | 浏览 | faq | help | html |
| 主页 | 进口 | 图书馆 | 管理 | 新的 | Notebook |
| 笔记本 | pdf | 预览 | 定价 | 个人资料 | 搜索 |
| status | support | 测试 | | | |

如果你尝试使用这些单词之一的项目 ID，作为**创建新项目**并**项目设置**指示弹出窗口，"库 id 是保留的标识符。"

由于项目 ID 也是项目的 URL 的一部分，ad 阻止程序软件可能会阻止使用某些关键字，如"广告"。 在这种情况下，使用的另一个词中项目 id。

## <a name="import-a-project-from-github"></a>从 GitHub 导入项目

可轻松将整个公共 GitHub 存储库作为项目（包括任何数据和 README.md 文件）导入。 使用“上传 GitHub 存储库”命令，在弹出窗口中提供以下详细信息，然后选择“导入”：

| 字段 | 描述 |
| --- | --- |
| GitHub 存储库 | github.com 上的源存储库的名称。 例如，若要克隆 Azure 认知服务的 Jupyter 笔记本（位于 [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)），请输入“Microsoft/cognitive-services-notebooks”。  |
| 以递归方式克隆 | GitHub 存储库可以包含多个子存储库。 如果希望克隆父存储库及其所有子存储库，请设置此选项。 一个存储库可能拥有许多子存储库，除非需要这样设置，否则请勿勾选此选项。 |
| 项目名称 | 项目的友好名称，Azure Notebooks 显示该名称。 |
| 项目 ID | 自定义标识符，是用于共享项目的 URL 的一部分（形式为 `https://notebooks.azure.com/<user_id>/projects/<project_id>`）。 此 ID 可以使用字母、 数字和连字符，限制为 30 个字符，且不能为[保留项目 ID](#reserved-project-ids)。 如果不确定使用什么内容，可按惯例使用小写的项目名且将空格转换为连字符，例如“my-notebook-project”（根据需要截断，以满足长度限制）。 |
| 公共 | 如果设置为公共项目，有链接的任何人均可访客项目。 创建专用项目时，请清除此选项。 |

从 GitHub 导入存储库还会导入其历史记录。 可以使用标准的 Git 命令执行从终端提交新更改、从 GitHub 拉取更改等操作。

## <a name="clone-a-project"></a>克隆项目

克隆会在你自己的帐户中创建某个现有项目的副本，然后你可以在该副本中运行并修改项目中的任何笔记本或其他文件。 克隆还可用于创建项目副本，你使用这些副本来执行试验或其他工作，不会对原始项目造成干扰。

克隆项目：

1. 在“我的项目”仪表板中右键单击所需的项目，然后选择“克隆”（键盘快捷方式：c）。

    ![项目上下文菜单上的克隆命令](media/clone-command.png)

1. 在“克隆项目”弹出窗口中，输入克隆的名称和 ID，并指定是否为公用克隆。 这些设置与[新项目](#create-a-new-project)相同。

    ![克隆项目弹出窗口](media/clone-project.png)

1. 选择“克隆”按钮后，Azure Notebooks 直接导航到副本。

## <a name="next-steps"></a>后续步骤

- [浏览示例笔记本](azure-notebooks-samples.md)
- [如何：配置和管理项目](configure-manage-azure-notebooks-projects.md)
- [如何：从笔记本内安装包](install-packages-jupyter-notebook.md)
- [如何：演示幻灯片放映](present-jupyter-notebooks-slideshow.md)
- [如何：处理数据文件](work-with-project-data-files.md)
- [如何：访问数据资源](access-data-resources-jupyter-notebooks.md)
- [如何：使用 Azure 机器学习服务](use-machine-learning-services-jupyter-notebooks.md)
