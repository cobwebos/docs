---
title: 教程：使用 Python 启动沉浸式阅读器
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将从头开始构建 Python 应用程序并添加沉浸式阅读器功能。
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.openlocfilehash: a252afae0a007ee0b791b56d19ffb0685848d30a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844354"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>教程：使用 Python 示例项目启动沉浸式阅读器

在[概述](./overview.md)中，你了解了沉浸式阅读器是什么以及它如何实施经过验证的技术以提高语言学习者、新兴读者和有学习差异的学生的阅读理解能力。 本教程介绍如何创建启动沉浸式阅读器的 Python Web 应用程序。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用示例项目创建包含 Pip、Flask、Jinja 和 virtualenv 的 Python Web 应用
> * 获取访问令牌
> * 启动沉浸式阅读器以显示示例内容

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

* 为 Azure Active Directory 身份验证配置的沉浸式阅读器资源。 按照[这些说明](./how-to-create-immersive-reader.md)进行设置。 在配置环境属性时，将需要在此处创建的一些值。 将会话的输出保存到文本文件中，以供将来参考。
* [Git](https://git-scm.com/)
* [沉浸式阅读器 SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) 和 [pip](https://docs.python.org/3/installing/index.html)。 从 Python 3.4 开始，Python 二进制安装程序默认随附 pip。
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) 和 [virtualenvwrapper-win for Windows](https://pypi.org/project/virtualenvwrapper-win/)，或 [virtualenvwrapper for OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [requests 模块](https://pypi.org/project/requests/2.7.0/)
* IDE 如 [Visual Studio Code](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>配置身份验证凭据

创建一个名为 _.env_ 的新文件，并将以下代码粘贴到其中，提供在创建沉浸式阅读器资源时给出的值。

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

请确保不要将此文件提交到源代码管理中，因为它包含不应公开的机密。

**getimmersivereadertoken** API 终结点应该以某种形式的身份验证（例如，[OAuth](https://oauth.net/2/)）进行保护，以防止未经授权的用户获取令牌以用于你的沉浸式阅读器服务和计费；该内容超出了本教程的范围。

## <a name="create-a-python-web-app-on-windows"></a>在 Windows 中创建 Python Web 应用

在 Windows 中使用 `flask` 创建 Python Web 应用。

安装 [Git](https://git-scm.com/)。

安装 Git 后，打开命令提示符并将沉浸式阅读器 SDK Git 存储库“克隆”到计算机上的某个文件夹

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

安装 [Python](https://www.python.org/downloads/)。

选中“将 Python 添加到 PATH”框。

![Python Windows 安装对话框 - 步骤 1](./media/pythoninstallone.jpg)

通过选中相应的框添加可选功能，然后单击“下一步”按钮。

![Python Windows 安装对话框 - 步骤 2](./media/pythoninstalltwo.jpg)

选择“自定义安装”，将安装路径设置为根文件夹（例如 `C:\Python37-32\`），然后单击“安装”按钮。

![Python Windows 安装对话框 - 步骤 3](./media/pythoninstallthree.jpg)

Python 安装完成后，打开命令提示符并运行 `cd` 切换到 Python Scripts 文件夹。

```cmd
cd C:\Python37-32\Scripts
```

安装 Flask。

```cmd
pip install flask
```

安装 Jinja2： 适用于 Python 的全功能模板引擎。

```cmd
pip install jinja2
```

安装 virtualenv： 用于创建独立 Python 环境的工具。

```cmd
pip install virtualenv
```

安装 virtualenvwrapper-win。 virtualenvwrapper 的理念是简化 virtualenv 的使用。

```cmd
pip install virtualenvwrapper-win
```

安装 requests 模块。 Requests 是使用 Python 编写的 Apache2 许可 HTTP 库。

```cmd
pip install requests
```

安装 python-dotenv 模块。 此模块从 .env 文件中读取键值对并将其添加到环境变量中。

```cmd
pip install python-dotenv
```

创建虚拟环境

```cmd
mkvirtualenv advanced-python
```

运行 `cd` 切换到示例项目的根文件夹。

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

将示例项目与环境相连接。 这会将新建的虚拟环境映射到示例项目的根文件夹。

```cmd
setprojectdir .
```

激活虚拟环境。

```cmd
activate
```

项目现在应处于活动状态，命令提示符中会显示类似于 `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` 的内容。

停用环境。

```cmd
deactivate
```

`(advanced-python)` 前缀现在应已消失，因为环境现已停用。

若要重新激活环境，请从示例项目的根文件夹运行 `workon advanced-python`。

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>启动沉浸式阅读器以显示示例内容

环境处于活动状态时，从示例项目的根文件夹输入 `flask run` 来运行示例项目。

```cmd
flask run
```

打开浏览器并导航到 _http://localhost:5000_ 。

## <a name="create-a-python-web-app-on-osx"></a>在 OSX 中创建 Python Web 应用

在 OSX 中使用 `flask` 创建 Python Web 应用。

安装 [Git](https://git-scm.com/)。

安装 Git 后，打开终端并将沉浸式阅读器 SDK Git 存储库“克隆”到计算机上的某个文件夹

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

安装 [Python](https://www.python.org/downloads/)。

现在，Python 根文件夹（例如 `Python37-32`）应会出现在 Applications 文件夹中。

Python 安装完成后，打开终端并运行 `cd` 切换到 Python Scripts 文件夹。

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

安装 pip。

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

然后运行以下命令为当前已登录的用户安装 pip，以避免权限问题。

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- 根据提示输入密码。
- 将 pip 安装路径添加到 PATH 变量中。
- 转到文件的底部，输入要添加的路径（作为列表中最后一项），例如`PATH=$PATH:/usr/local/bin`。
- 按 Ctrl-X 退出。
- 输入 `Y` 以保存修改的缓冲区。
- 就这么简单！ 若要进行测试，请在新的终端窗口中键入 `echo $PATH`。

安装 Flask。

```bash
pip install flask --user
```

安装 Jinja2： 适用于 Python 的全功能模板引擎。

```bash
pip install Jinja2 --user
```

安装 virtualenv： 用于创建独立 Python 环境的工具。

```bash
pip install virtualenv --user
```

安装 virtualenvwrapper。 virtualenvwrapper 的理念是简化 virtualenv 的使用。

```bash
pip install virtualenvwrapper --user
```

安装 requests 模块。 Requests 是使用 Python 编写的 Apache2 许可 HTTP 库。

```bash
pip install requests --user
```

安装 python-dotenv 模块。 此模块从 .env 文件中读取键值对并将其添加到环境变量中。

```bash
pip install python-dotenv --user
```

选择要在其中保留虚拟环境的文件夹并运行此命令

```bash
mkdir ~/.virtualenvs
```

运行 `cd` 切换到沉浸式阅读器 SDK Python 示例应用程序文件夹。

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

创建虚拟环境

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

将示例项目与环境相连接。 这会将新建的虚拟环境映射到示例项目的根文件夹。

```bash
setprojectdir .
```

激活虚拟环境。

```bash
activate
```

项目现在应处于活动状态，命令提示符中会显示类似于 `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` 的内容。

停用环境。

```bash
deactivate
```

`(advanced-python)` 前缀现在应已消失，因为环境现已停用。

若要重新激活环境，请从示例项目的根文件夹运行 `workon advanced-python`。

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>启动沉浸式阅读器以显示示例内容

环境处于活动状态时，从示例项目的根文件夹输入 `flask run` 来运行示例项目。

```bash
flask run
```

打开浏览器并导航到 _http://localhost:5000_ 。

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](./reference.md)
* 在[ GitHub ](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)上查看代码示例
