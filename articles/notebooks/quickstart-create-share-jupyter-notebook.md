---
title: 在 Azure 上创建并共享 Jupyter Notebook
description: 在 Azure Notebooks 上快速创建并运行 Jupyter Notebook，然后与他人共享该笔记本。
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 5add60ad-0b4b-4fd5-adf5-eb50ce072d00
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 19e8217bf88427c353fdd4c90c9f421445b7c81a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970180"
---
# <a name="quickstart-create-and-share-a-notebook"></a>快速入门：创建和共享笔记本

1. 转到 [Azure Notebooks](https://notebooks.azure.com) 并登录。 （有关详细信息，请参阅[快速入门 - 登录 Azure Notebooks](quickstart-sign-in-azure-notebooks.md)）。

1. 在公用个人资料页面中，选择页面顶部的“我的项目”  ：

    ![浏览器窗口顶部的“我的项目”链接](media/quickstarts/my-projects-link.png)

1. 在“我的项目”页面上，选择“+ 新建项目”（键盘快捷方式：N）；如果浏览器窗口较窄，该按钮可能仅显示为 +    ：

    ![“我的项目”页面上的“新建项目”命令](media/quickstarts/new-project-command.png)

1. 在出现的“创建新项目”弹出窗口中，输入或设置以下详细信息，然后选择“创建”   ：

   - 项目名称  ：Python 中的 hello world
   - 项目 ID  ：hello-world-python
   - 公共项目  ：（已清除）
   - 创建 README.md  ：（已清除）

     ![已填充详细信息的“新建项目”弹出窗口](media/quickstarts/new-project-popup.png)

1. 几分钟后，Azure Notebooks 导航到新项目。 通过选择“+ 新建”下拉列表（可能仅显示为 +）并选择“笔记本”，向该项目添加笔记本    ：

    [![](media/quickstarts/empty-project-new-notebook-button.png "新的空项目和添加笔记本命令")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. 在出现的“创建新笔记本”弹出窗口中，输入笔记本的文件名，例如 HelloWorldInPython.ipynb（.ipynb 表示 IronPython (Jupyter) Notebook），并为语言（也称为内核）选择“Python 3.6”      ：

    ![“创建新笔记本”弹出窗口](media/quickstarts/new-notebook-popup.png)

1. 选择“新建”以完成创建笔记本，该笔记本随后显示在项目的文件列表中  ：

    ![出现在项目文件列表中的新笔记本](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>运行笔记本

1. 选择该新笔记本，使其在编辑器中运行；可为此笔记本自动激活所选内核（在本例中为 Python 3.6）：

    ![Azure Notebooks 中新笔记本的视图](media/quickstarts/create-notebook-first-open.png)

1. 默认情况下，笔记本有一个空代码单元。 要将单元类型更改为“Markdown”，请使用单元类型下拉列表选择“Markdown”   ：

    ![更改新笔记本中的单元类型](media/quickstarts/create-notebook-cell-type.png)

1. 在单元中输入或粘贴以下标题文本：

    ```markdown
    # Hello World in Python
    ```

1. 由于你正在编辑 Markdown，因此文本显示为带有“#”的标题。 要将 Markdown 呈现为 HTML，请选择“运行”按钮  。 Azure Notebooks 随后自动创建一个新的代码单元：

    ![单元的运行按钮和呈现的 Markdown](media/quickstarts/run-cell-markdown-render.png)

1. 在代码单元中，输入以下 Python 代码：

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. 选择“运行”  （键盘快捷方式：Shift+Enter）以运行代码。 在单元格下方，应看到类似于以下文本的成功输出：

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. 选择保存图标以保存你的工作：

    ![Jupyter Notebook 工具栏上的保存图标](media/quickstarts/hello-results-save-icon.png)

1. 选择“文件” > “关闭并停止”菜单命令来停止服务器并关闭浏览器窗口   。

## <a name="share-the-notebook"></a>共享笔记本

要共享笔记本，请根据需要切换回项目页面，右键单击笔记本文件，选择“复制链接”（键盘快捷方式：Y），然后将该链接粘贴到相应的消息中（电子邮件、IM 等。）  。

在项目页面上，还可以使用“共享”菜单获取链接，使用链接创建电子邮件，或获取 HTML 和 Markdown 嵌入代码  ：

![项目共享命令](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：创建并运行 Jupyter Notebook 以执行线性回归](tutorial-create-run-jupyter-notebook.md)
