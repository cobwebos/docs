---
title: "在 Azure 中使用 Flask 创建 Web 应用"
description: "本教程介绍在 Azure 上运行 Python Web 应用。"
services: app-service\web
documentationcenter: python
tags: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: b7f4ca3a-0b52-4108-90a7-f7e07ac73da0
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/20/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6a0aa48be45b5d0cbac7953e0106c465161e7228


---
# <a name="creating-web-apps-with-flask-in-azure"></a>在 Azure 中使用 Flask 创建 Web 应用
本教程介绍了如何开始在 [Azure App Service Web 应用](http://go.microsoft.com/fwlink/?LinkId=529714)中运行 Python。  Web Apps 提供有限的免费托管和快速部署功能，现在，您可以使用 Python！  随着您的应用增长，可以切换到付费托管，并且还可以与所有其他 Azure 服务集成。

你将使用 Flask web 框架创建应用程序（请参阅本教程针对 [Django](web-sites-python-create-deploy-django-app.md) 和 [Bottle](web-sites-python-create-deploy-bottle-app.md) 的备选版本）。  将从 Azure 库中创建网站，设置 Git 部署和本地克隆存储库。  然后将本地运行应用程序，将其更改、 提交和推送到 Azure。  本教程演示如何从 Windows 或 Mac/Linux 执行此操作。

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> 如果您想要在注册 Azure 帐户之前开始使用 Azure App Service，请转到 [试用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，您可以在 App Service 中立即创建一个生存期较短的入门 Web 应用。 你不需要使用信用卡，也不需要做出承诺。
> 
> 

## <a name="prerequisites"></a>先决条件
* Windows、 Mac 或 Linux
* Python 2.7 或 3.4
* setuptools, pip, virtualenv（仅限 Python 2.7）
* Git
* [用于 Visual Studio 的 Python 工具][用于 Visual Studio 的 Python 工具] (PTVS) - 注意：这是可选的

**注意**：Python 项目目前不支持 TFS 发布。

### <a name="windows"></a>Windows
如果尚未安装 Python 2.7 或 3.4（32 位） ，建议使用 Web 平台安装程序来安装 [Azure SDK for Python 2.7] 或 [Azure SDK for Python 3.4]。  这将安装 32 位版本的 Python、setuptools、pip、virtualenv 等（32 位 Python 是在 Azure 主机计算机上安装的）。  或者，您可以从 [python.org]获取 Python。

对于 Git，我们建议使用 [Git for Windows] 或 [GitHub for Windows]。  如果您使用 Visual Studio，可以使用集成的 Git 支持。

我们还建议你安装 [Python Tools 2.2 for Visual Studio]。  这是可选的，但是如果您有 [Visual Studio]（包括免费 Visual Studio Community 2013 或 Visual Studio Express 2013 for Web），则这将为您提供很好的 Python IDE。

### <a name="maclinux"></a>Mac/Linux
你应安装了 Python 和 Git ，但请确保你具有 Python 2.7 或 3.4。

## <a name="web-app-create-on-the-azure-portal"></a>在 Azure 门户中创建 Web 应用
创建您的应用的第一步是通过 [Azure 门户](https://portal.azure.com)创建 Web 应用。 

1. 登录到 Azure 门户，然后单击左下角的“新建”  按钮。 
2. 单击“Web + 移动” 。
3. 在搜索框中，键入“python”。
4. 在搜索结果中，选择“Flask”，然后单击”创建”。
5. 配置新的 Flask 应用，如为其创建新的应用服务计划和新的资源组。 然后单击 **创建**。
6. 按照 [从本地 Git 部署到 Azure App Service](app-service-deploy-local-git.md)的说明为新创建的 Web 应用配置 Git 发布。

## <a name="application-overview"></a>应用程序概述
### <a name="git-repository-contents"></a>Git 存储库内容
下面是您会在初始 Git 存储库找到的文件的概览，我们将在下一节中予以克隆。

    \FlaskWebProject\__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

应用程序的的主要来源。  包含具有母版布局的 3 页（索引、关于、联系人）。  静态内容和脚本包括启动、 jquery、 modernizr 和响应。

    \runserver.py

本地开发服务器支持。 使用此选项以在本地运行应用程序。

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

[用于 Visual Studio 的 Python 工具]的项目文件。

    \ptvs_virtualenv_proxy.py

虚拟环境的 IIS 代理和 PTVS 远程调试支持。

    \requirements.txt

此应用程序所需的外部软件包。 部署脚本将对在此文件中列出的包进行 pip 安装。

    \web.2.7.config
    \web.3.4.config

IIS 配置文件。  部署脚本将使用适当的 web.x.y.config，并将其复制为 web.config。

### <a name="optional-files---customizing-deployment"></a>可选文件 - 自定义部署
[!INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### <a name="optional-files---python-runtime"></a>可选文件 - Python 运行时
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### <a name="additional-files-on-server"></a>服务器上的其他文件
某些文件存在于服务器上，但不会添加到 git 存储库。  这些是由部署脚本创建的。

    \web.config

IIS 配置文件。  每次部署中从 web.x.y.config 创建

    \env\

Python 虚拟环境。  如果在应用上尚不存在兼容的虚拟环境，请在部署期间创建。  在 requirements.txt 中列出的软件包是 pip 安装的，但如果已安装软件包，pip 将跳过安装。

后续 3 节描述如何在 3 个不同的环境下继续进行 Web 应用开发：

* Windows，具有 Python Tools for Visual Studio
* Windows，具有命令行
* Mac/Linux，具有命令行

## <a name="web-app-development---windows---python-tools-for-visual-studio"></a>Web 应用开发 - Windows - Python Tools for Visual Studio
### <a name="clone-the-repository"></a>克隆存储库
首先，使用 Azure 门户网站上提供的 URL 来克隆存储库。 有关详细信息，请参阅 [从本地 Git 部署到 Azure App Service](app-service-deploy-local-git.md)。

打开包含在存储库根中的解决方案文件 (.sln) 。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### <a name="create-virtual-environment"></a>创建虚拟环境
现在，我们将创建本地开发的虚拟环境。  右键单击 **Python 环境**，选择“添加虚拟环境...”。

* 请确保环境的名称是 `env`。
* 选择基解释器。  请确保使用的 Python 版本与 Web 应用所选的 Python 版本相同（在 runtime.txt 中或 Azure 门户中 Web 应用的 **应用程序设置** 边栏选项卡）。
* 请确保选中此选项以下载并安装软件包。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

单击“创建” 。  这会创建虚拟环境，并安装 requirements.txt 中列出的依赖项。

### <a name="run-using-development-server"></a>使用开发服务器运行
按 F5 开始调试，您的 web 浏览器会自动打开到本地运行的页面。

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

您可以在源中设置断点，使用监视窗口等。请参阅 [Python Tools for Visual Studio 文档]以了解有关各种功能的详细信息。

### <a name="make-changes"></a>进行更改
现在您可以通过更改应用程序源和/或模板进行试验。

您测试您的更改后，将其提交到 Git 存储库：

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### <a name="install-more-packages"></a>安装多个软件包
你的应用程序可能具有除 Python 和 Flask 以外的依赖关系。

您可以使用 pip 安装其他软件包。  要安装包，请右键单击虚拟环境，然后选择 **安装 Python 软件包**。

例如，要安装 Azure SDK for Python（使您可以访问 Azure 存储、服务总线和其他 Azure 服务），请输入 `azure`：

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

在虚拟环境上右键单击并选择 **生成 requirements.txt** 以更新 requirements.txt。

然后，将 requirements.txt 的更改落实到 Git 存储库。

### <a name="deploy-to-azure"></a>部署到 Azure
要触发部署操作，请单击“同步”或“推送”。  同步执行推送和拉取。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

首次部署将需要一些时间，因为它将创建虚拟环境、 安装包等。

Visual Studio 不会显示部署的进度。  如果您想要查看输出，请参阅 [故障排除 - 部署](#troubleshooting-deployment)上的相关部分。

浏览到 Azure URL 要查看您的更改。

## <a name="web-app-development---windows---command-line"></a>Web 应用开发 - Windows - 命令行
### <a name="clone-the-repository"></a>克隆存储库
首先，使用在 Azure 门户中提供的 URL 来克隆存储库，并将 Azure 存储库添加为远程。 有关详细信息，请参阅 [从本地 Git 部署到 Azure App Service](app-service-deploy-local-git.md)。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>创建虚拟环境
我们将为开发目的创建新的虚拟环境 （不要将其添加到存储库）。  Python 中的虚拟环境是可重定位，因此使用该应用程序的每个开发人员将在本地创建自己的环境。

请确保使用的 Python 版本与 Web 应用所选的 Python 版本相同（在 runtime.txt 中或 Azure 门户中 Web 应用的 **应用程序设置** 边栏选项卡）。

对于 Python 2.7：

    c:\python27\python.exe -m virtualenv env

对于 Python 3.4：

    c:\python34\python.exe -m venv env

安装应用程序所需的任何外部软件包。 可以使用该存储库的根处 requirements.txt 文件在虚拟环境中安装软件包：

    env\scripts\pip install -r requirements.txt

### <a name="run-using-development-server"></a>使用开发服务器运行
您可以使用以下命令在开发服务器下启用应用程序：

    env\scripts\python runserver.py

控制台将显示服务器侦听的 URL 和端口：

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

然后，将 web 浏览器打开到该 URL。

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### <a name="make-changes"></a>进行更改
现在您可以通过更改应用程序源和/或模板进行试验。

您测试您的更改后，将其提交到 Git 存储库：

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>安装多个软件包
你的应用程序可能具有除 Python 和 Flask 以外的依赖关系。

你可以使用 pip 安装其他软件包。  例如，要安装 Azure SDK for Python（使您可以访问 Azure 存储、 服务总线和其他 Azure 服务），请输入：

    env\scripts\pip install azure

请确保更新 requirements.txt：

    env\scripts\pip freeze > requirements.txt

落实更改：

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>部署到 Azure
要触发部署，请将更改推送到 Azure：

    git push azure master

您将看到部署脚本的输出，包括虚拟环境创建，安装软件包，创建 web.config。

浏览到 Azure URL 要查看您的更改。

## <a name="web-app-development---maclinux---command-line"></a>Web 应用开发 - Mac/Linux - 命令行
### <a name="clone-the-repository"></a>克隆存储库
首先，使用在 Azure 门户中提供的 URL 来克隆存储库，并将 Azure 存储库添加为远程。 有关详细信息，请参阅 [从本地 Git 部署到 Azure App Service](app-service-deploy-local-git.md)。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>创建虚拟环境
我们将为开发目的创建新的虚拟环境 （不要将其添加到存储库）。  Python 中的虚拟环境是可重定位，因此使用该应用程序的每个开发人员将在本地创建自己的环境。

请确保使用的 Python 版本与 Web 应用所选的 Python 版本相同（在 runtime.txt 中或 Azure 门户中 Web 应用的 **应用程序设置** 边栏选项卡）。

对于 Python 2.7：

    python -m virtualenv env

对于 Python 3.4：

    python -m venv env
或 pyvenv env

安装应用程序所需的任何外部软件包。 可以使用该存储库的根处 requirements.txt 文件在虚拟环境中安装软件包：

    env/bin/pip install -r requirements.txt

### <a name="run-using-development-server"></a>使用开发服务器运行
您可以使用以下命令在开发服务器下启用应用程序：

    env/bin/python runserver.py

控制台将显示服务器侦听的 URL 和端口：

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

然后，将 web 浏览器打开到该 URL。

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### <a name="make-changes"></a>进行更改
现在您可以通过更改应用程序源和/或模板进行试验。

您测试您的更改后，将其提交到 Git 存储库：

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>安装多个软件包
你的应用程序可能具有除 Python 和 Flask 以外的依赖关系。

你可以使用 pip 安装其他软件包。  例如，要安装 Azure SDK for Python（使您可以访问 Azure 存储、 服务总线和其他 Azure 服务），请输入：

    env/bin/pip install azure

请确保更新 requirements.txt：

    env/bin/pip freeze > requirements.txt

落实更改：

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>部署到 Azure
要触发部署，请将更改推送到 Azure：

    git push azure master

您将看到部署脚本的输出，包括虚拟环境创建，安装软件包，创建 web.config。

浏览到 Azure URL 要查看您的更改。

## <a name="troubleshooting---package-installation"></a>故障排除 - 软件包安装
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>故障排除 - 虚拟环境
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>后续步骤
请遵循下面链接以了解有关 Flask 和 Python Tools for Visual Studio 的更多信息： 

* [Flask 文档]
* [Python Tools for Visual Studio 文档]

有关使用 Azure 表存储和 MongoDB 的信息：

* [具有 Python Tools for Visual Studio 的 Azure 上的 Flask 和 MongoDB]
* [具有 Python Tools for Visual Studio 的 Azure 上的 Flask 和 Azure 表存储]

有关详细信息，另请参阅 [Python 开发人员中心](/develop/python/)。

## <a name="whats-changed"></a>发生的更改
* 有关从网站更改为 App Service 的指南，请参阅 [Azure App Service 及其对现有 Azure 服务的影响](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[具有 Python Tools for Visual Studio 的 Azure 上的 Flask 和 MongoDB]: https://github.com/microsoft/ptvs/wiki/Flask-and-MongoDB-on-Azure
[具有 Python Tools for Visual Studio 的 Azure 上的 Flask 和 Azure 表存储]: web-sites-python-ptvs-flask-table-storage.md

<!--External Link references-->
[Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git for Windows]: http://msysgit.github.io/
[GitHub for Windows]: https://windows.github.com/
[用于 Visual Studio 的 Python 工具]: http://aka.ms/ptvs
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Python Tools for Visual Studio 文档]: http://aka.ms/ptvsdocs
[Flask 文档]: http://flask.pocoo.org/ 




<!--HONumber=Nov16_HO3-->


