---
title: 教程：使用 Python 启动沉浸式阅读器
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将从头开始构建 Python 应用程序并添加沉浸式阅读器功能。
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: dylankil
ms.custom: devx-track-python
ms.openlocfilehash: 81d4135671d8ab3e2a8854b855ca285107faaa86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90979350"
---
# <a name="tutorial-start-the-immersive-reader-using-the-python-sample-project"></a>教程：使用 Python 示例项目启动沉浸式阅读器

在[概述](./overview.md)中，你了解了沉浸式阅读器是什么以及它如何实施经过验证的技术以提高语言学习者、新兴读者和有学习差异的学生的阅读理解能力。 本教程介绍如何创建启动沉浸式阅读器的 Python Web 应用程序。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用示例项目创建包含 pip、Flask、Jinja 和 virtualenv 的 Python Web 应用。
> * 获取访问令牌。
> * 启动沉浸式阅读器以显示示例内容。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

* 为 Azure Active Directory 身份验证配置的沉浸式阅读器资源。 按照[这些说明](./how-to-create-immersive-reader.md)进行设置。 在配置环境属性时，将需要在此处创建的一些值。 将会话的输出保存到文本文件中，以供将来参考。
* [Git](https://git-scm.com/)。
* [沉浸式阅读器 SDK](https://github.com/microsoft/immersive-reader-sdk)。
* [Python](https://www.python.org/downloads/) 和 [pip](https://docs.python.org/3/installing/index.html)。 从 Python 3.4 开始，Python 二进制安装程序默认随附 pip。
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)。
* [Jinja](http://jinja.pocoo.org/docs/2.10/)。
* [virtualenv](https://virtualenv.pypa.io/en/latest/) 和 [virtualenvwrapper-win for Windows](https://pypi.org/project/virtualenvwrapper-win/)，或 [virtualenvwrapper for OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)。
* [requests 模块](https://pypi.org/project/requests/2.7.0/)。
* IDE，如 [Visual Studio Code](https://code.visualstudio.com/)。

## <a name="configure-authentication-credentials"></a>配置身份验证凭据

创建名为 .env 的新文件，然后将以下名称和值粘贴到其中。 提供在创建沉浸式阅读器资源时给出的值。

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

不要将此文件提交到源代码管理中，因为它包含不应公开的机密。

采用某种形式的身份验证（例如 [OAuth](https://oauth.net/2/)）保护 getimmersivereadertoken API 终结点。 身份验证可防止未经授权的用户获取用于沉浸式阅读器服务和计费的令牌。 这超出了本教程的范围。

## <a name="create-a-python-web-app-on-windows"></a>在 Windows 中创建 Python Web 应用

在 Windows 中使用 `flask` 创建 Python Web 应用。

安装 [Git](https://git-scm.com/)。

安装 Git 后，打开命令提示符并将沉浸式阅读器 SDK Git 存储库克隆到计算机上的某个文件夹。

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

安装 [Python](https://www.python.org/downloads/)。

选中“将 Python 添加到 PATH”复选框。

![Python Windows 安装对话框 - 步骤 1](./media/pythoninstallone.jpg)

通过选中复选框来添加“可选功能”，然后选择“下一步”。

![Python Windows 安装对话框 - 步骤 2](./media/pythoninstalltwo.jpg)

选择“自定义安装”，将安装路径设置为根文件夹（例如 `C:\Python37-32\`）。 然后选择“安装”。

![Python Windows 安装对话框 - 步骤 3](./media/pythoninstallthree.jpg)

Python 安装完成后，打开命令提示符并使用 `cd` 转到 Python Scripts 文件夹。

```cmd
cd C:\Python37-32\Scripts
```

安装 Flask。

```cmd
pip install flask
```

安装 Jinja2： 它是适用于 Python 的全功能模板引擎。

```cmd
pip install jinja2
```

安装 virtualenv： 此工具可创建独立 Python 环境。

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

创建虚拟环境。

```cmd
mkvirtualenv advanced-python
```

使用 `cd` 转到示例项目的根文件夹。

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

将示例项目与环境相连接。 此操作会将新建的虚拟环境映射到示例项目的根文件夹。

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

`(advanced-python)` 前缀应已消失，因为环境已停用。

若要重新激活环境，请从示例项目的根文件夹运行 `workon advanced-python`。

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>启动沉浸式阅读器以显示示例内容

环境处于活动状态时，从示例项目的根文件夹输入 `flask run` 来运行示例项目。

```cmd
flask run
```

打开浏览器并转到 http://localhost:5000 。

## <a name="create-a-python-web-app-on-osx"></a>在 OSX 中创建 Python Web 应用

在 OSX 中使用 `flask` 创建 Python Web 应用。

安装 [Git](https://git-scm.com/)。

安装 Git 后，打开终端并将沉浸式阅读器 SDK Git 存储库克隆到计算机上的某个文件夹。

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

安装 [Python](https://www.python.org/downloads/)。

现在，Python 根文件夹（例如 `Python37-32`）应会出现在 Applications 文件夹中。

Python 安装完成后，打开终端并使用 `cd` 转到 Python Scripts 文件夹。

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

安装 pip。

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

运行以下代码为当前已登录的用户安装 pip，以避免权限问题。

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- 根据提示输入密码。
- 将 pip 安装路径添加到 PATH 变量中。
- 转到文件的底部，输入要添加的路径（作为列表中最后一项），例如 `PATH=$PATH:/usr/local/bin`。
- 选择 Ctrl+X 退出。
- 输入“Y”以保存修改的缓冲区。
- 就这么简单！ 若要进行测试，请在新的终端窗口中输入 `echo $PATH`。

安装 Flask。

```bash
pip install flask --user
```

安装 Jinja2： 它是适用于 Python 的全功能模板引擎。

```bash
pip install Jinja2 --user
```

安装 virtualenv： 此工具可创建独立 Python 环境。

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

选择要在其中保留虚拟环境的文件夹并运行此命令：

```bash
mkdir ~/.virtualenvs
```

使用 `cd` 转到沉浸式阅读器 SDK Python 示例应用程序文件夹。

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

创建虚拟环境。

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

将示例项目与环境相连接。 此操作会将新建的虚拟环境映射到示例项目的根文件夹。

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

`(advanced-python)` 前缀应已消失，因为环境已停用。

若要重新激活环境，请从示例项目的根文件夹运行 `workon advanced-python`。

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>启动沉浸式阅读器以显示示例内容

环境处于活动状态时，从示例项目的根文件夹输入 `flask run` 来运行示例项目。

```bash
flask run
```

打开浏览器并转到 http://localhost:5000 。

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](./reference.md)。
* 在 [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) 上查看代码示例。
