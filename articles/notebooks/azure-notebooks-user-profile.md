---
title: 用于 Azure Notebooks 的用户个人资料和 ID
description: 如何使用 Azure Notebooks 创建和管理用户个人资料和用户 ID。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: b8c21b908ca9162a7e44c7af1e222babc6ee1eb7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265604"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>用于 Azure Notebooks 的个人资料和用户 ID

在 Azure Notebooks 强大的协作空间中，用户个人资料将向其他人提供公共图像：

[![Azure Notebooks 配置文件页](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

用于共享项目和笔记本的 URL 中包含用户 ID。 下表介绍了不同的 URL 模式：

- `https://notebooks.azure.com/<user_id>`：个人资料页。
- `https://notebooks.azure.com/<user_id>/projects`：项目。 你可查看所有项目；其他用户只能查看你的公共项目。
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`：项目文件。
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`：特定项目的克隆。
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`：特定笔记本或文件的 HTML 预览。

## <a name="your-user-id"></a>用户 ID

首次登录 Azure Notebooks 时，将自动为帐户分配临时用户 ID，例如“anon-idr3ca”。 只要用户 ID 以“anon-”开头，登录时 Azure Notebooks 都会提示你对其进行更改：

![登录 Azure Notebooks 时提示创建用户 ID](media/accounts/create-user-id.png)

“配置用户 ID”命令也显示在临时用户姓名旁：

![使用临时 ID 时出现的“配置用户 ID”命令](media/accounts/configure-user-id-command.png)

还可以随时更改个人资料页上的用户 ID。

用户 ID 必须之间四个和十六个字母、 数字和连字符组成。 不能使用其他字符，且用户 ID 不能以连字符开头或结尾，也不能在一行中使用多个连字符。 Azure Notebook 的所有帐户间是唯一的用户 Id，因为可能会看到以下消息:"用户 ID 已在使用。" （还将显示消息如果尝试使用 Microsoft 商标作为用户 id。）在这些情况下，选择一个不同的用户 id。

> [!Important]
> 更改 ID 会导致之前使用旧 ID 共享的任何 URL 无效。 可以将 ID 更改为以前的 ID，使链接重新生效。 但是，其他用户可以在此期间声明未使用的 ID。

## <a name="your-profile"></a>个人资料

个人资料由 URL `https://notebooks.azure.com/<user_id>` 中可公开查看的信息组成。 个人资料页还会显示最近使用的项目和任何带星号的项目。

要编辑个人资料，请在个人资料页上使用“编辑个人资料信息”命令。 个人资料各部分如下：

| 部分 | 内容 |
| --- | --- |
| 个人资料照片 | 个人资料页上显示的图像。 |
| 帐户信息 | 显示名称、用户 ID 和公共电子邮件帐户。 此处的电子邮件帐户为其他用户提供了与你联系的方式，并且可以与用于登录 Azure Notebooks 的[帐户](azure-notebooks-user-account.md)不同。 |
| 个人资料信息 | 所在位置、公司、职位、网站和简短的自我描述。 |
| 个人社交资料 | GItHub、Twitter 和 Facebook ID（如果想共享）。 |
| 隐私设置 | 提供两个命令：<ul><li>**导出我的个人资料**：创建和下载 .zip 文件，其中包含 Azure Notebooks 在你的个人资料中保存的所有信息（包括照片、个人资料信息和安全日志）。</li><li>**删除我的帐户**：永久删除存储在 Azure Notebooks 中的所有个人信息。</li></ul> |
| 启用网站功能 | 你可以控制 Azure Notebooks 行为的各个方面：<ul><li>**统一的 Notebook 前端**：确保更快地启动笔记本并获得更好的持久性。</li><li>**默认在 JupyterLab 中运行**：默认情况下，Azure Notebooks 将提供适合大多数用户的简单用户界面。 JupyterLab 为经验丰富的用户提供内容更丰富，但操作更复杂的界面。</li><li>**VNext 网站**：启用本文档中显示的现代化 Web 布局。</li></ul> |

## <a name="next-steps"></a>后续步骤  

> [!div class="nextstepaction"]
> [教程：创建并运行 Jupyter Notebook 以执行线性回归](tutorial-create-run-jupyter-notebook.md)
