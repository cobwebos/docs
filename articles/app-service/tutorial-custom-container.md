---
title: 教程：在 Azure 应用服务中生成并运行自定义映像
description: 一个分步指南，用于构建自定义 Linux 或 Windows 映像，将映像推送到 Azure 容器注册表，然后将该映像部署到 Azure 应用服务。 了解如何在自定义容器中将部署自定义软件迁移到应用服务。
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: azure 应用服务, web 应用, linux, windows, docker, 容器
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: f4b2aea0a6782b5484b2f6d15066d71990348596
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91312050"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>使用自定义容器将自定义软件迁移到 Azure 应用服务

::: zone pivot="container-windows"  

[Azure 应用服务](overview.md)在 Windows 上提供预定义的应用程序堆栈，例如在 IIS 上运行的 ASP.NET 或 Node.js。 预配置的 Windows 环境锁定了操作系统，不允许对其进行管理访问、软件安装、全局程序集缓存更改等操作（请参阅 [Azure 应用服务上的操作系统功能](operating-system-functionality.md)）。 但是，通过在应用服务（预览版）中使用自定义 Windows 容器，可以作出应用所需的 OS 更改，因此可轻松迁移需要自定义 OS 和软件配置的本地应用。 本教程演示如何将使用 Windows 字体库中安装的自定义字体的 ASP.NET 应用迁移到应用服务。 你将自定义配置的 Windows 映像从 Visual Studio 部署到 [Azure 容器注册表](../container-registry/index.yml)，然后在应用服务中运行它。

![显示在 Windows 容器中运行的 Web 应用。](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>先决条件

为完成此教程：

- <a href="https://hub.docker.com/" target="_blank">注册 Docker 中心帐户</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">安装用于 Windows 的 Docker</a>。
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">将 Docker 切换为运行 Windows 容器</a>。
- <a href="https://www.visualstudio.com/downloads/" target="_blank">安装 Visual Studio 2019</a>，其中包含 **ASP.NET 和 web 开发**以及 **Azure 开发**工作负载。 如果已安装 Visual Studio 2019：
    - 通过单击“帮助” > “检查更新”，在 Visual Studio 中安装最新的更新。
    - 在 Visual Studio 中，通过单击“工具” > “获取工具和功能”，添加工作负载。

## <a name="set-up-the-app-locally"></a>在本地设置应用

### <a name="download-the-sample"></a>下载示例

在此步骤中，将设置本地 .NET 项目。

- [下载示例项目 ](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip)。
- 提取（解压缩）custom-font-win-container.zip 文件。

示例项目包括一个简单的 ASP.NET 应用程序，该应用程序使用安装在 Windows 字体库中的自定义字体。 不需要安装字体，但这是一个与基础 OS 集成的应用示例。 要将此类应用迁移到应用服务，需要重新构建代码以移除集成，或通过自定义 Windows 容器将其按原样迁移。

### <a name="install-the-font"></a>安装字体

在 Windows 资源管理器中，导航到“custom-font-win-container-master/CustomFontSample”，右键单击“FrederickatheGreat-Regular.ttf”，然后选择“安装” 。

该字体在 [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great) 中公开发布。

### <a name="run-the-app"></a>运行应用

在 Visual Studio 中打开 custom-font-win-container/CustomFontSample.sln 文件。 

键入 `Ctrl+F5` 在不调试的情况下运行应用。 该应用将显示在默认浏览器中。 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="显示在默认浏览器中显示的应用的屏幕截图。":::

由于它使用已安装的字体，因此应用无法在应用服务沙盒中运行。 但是，可以改为使用 Windows 容器对其进行部署，因为可以在 Windows 容器中安装该字体。

### <a name="configure-windows-container"></a>配置 Windows 容器

在解决方案资源管理器中，右键单击“CustomFontSample”项目，选择“添加” > “容器业务流程支持”  。

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="显示在默认浏览器中显示的应用的屏幕截图。":::

选择“Docker Compose” > “确定” 。

现在，项目设置为在 Windows 容器中运行。 Dockerfile 添加到 CustomFontSample 项目，docker-compose 项目添加到解决方案 。 

在解决方案资源管理器中，打开 Dockerfile。

需要使用[受支持的父映像](configure-custom-container.md#supported-parent-images)。 通过将 `FROM` 行替换为以下代码，更改父映像：

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

在该文件的末尾，添加以下行并保存文件：

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

可以在 CustomFontSample 项目中找到 InstallFont.ps1。 它是一个安装该字体的简单脚本。 可以在[脚本中心](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133)找到更复杂的脚本版本。

> [!NOTE]
> 若要在本地测试 Windows 容器，请确保在本地计算机上启动 Docker。
>

## <a name="publish-to-azure-container-registry"></a>发布到 Azure 容器注册表

[Azure 容器注册表](../container-registry/index.yml)可以存储用于容器部署的映像。 可以将应用服务配置为使用 Azure 容器注册表中托管的映像。

### <a name="open-publish-wizard"></a>打开发布向导

在解决方案资源管理器中，右键单击“CustomFontSample”项目，选择“发布” 。

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="显示在默认浏览器中显示的应用的屏幕截图。":::

### <a name="create-registry-and-publish"></a>创建注册表并发布

在发布向导中，选择“容器注册表” > “新建 Azure 容器注册表” > “发布”  。

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="显示在默认浏览器中显示的应用的屏幕截图。":::

### <a name="sign-in-with-azure-account"></a>使用 Azure 帐户登录

在“新建 Azure 容器注册表”对话框中，选择“添加帐户”，然后登录到你的 Azure 订阅。 如果已登录，请从下拉列表中选择包含所需订阅的帐户。

![登录 Azure](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>配置注册表

根据下表中建议的值配置新的容器注册表。 完成后，单击“创建”。

| 设置  | 建议的值 | 更多信息 |
| ----------------- | ------------ | ----|
|**DNS 前缀**| 保留生成的注册表名称，或将其更改为另一个唯一的名称。 |  |
|**资源组**| 单击“新建”，键入 myResourceGroup，然后单击“确定”  。 |  |
|**SKU**| 基本 | [定价层](https://azure.microsoft.com/pricing/details/container-registry/)|
|**注册表位置**| 西欧 | |

![配置 Azure 容器注册表](./media/tutorial-custom-container/configure-registry.png)

终端窗口随即打开并显示映像部署进度。 等待部署完成。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-web-app"></a>创建 Web 应用

在左侧菜单中，选择“创建资源” > “Web” > “用于容器的 Web 应用”  。

### <a name="configure-app-basics"></a>配置应用基本信息

在“基本信息”选项卡中，根据下表配置设置，然后单击“下一步:Docker”。

| 设置  | 建议的值 | 更多信息 |
| ----------------- | ------------ | ----|
|**订阅**| 确保选择正确的订阅。 |  |
|**资源组**| 选择“新建”，键入 myResourceGroup，然后选择“确定”  。 |  |
|**名称**| 键入唯一名称。 | Web 应用的 URL 为 `http://<app-name>.azurewebsites.net`，其中 `<app-name>` 是应用名称。 |
|**发布**| Docker 容器 | |
|**操作系统**| Windows | |
|**区域**| 西欧 | |
|**Windows 计划**| 选择“新建”，键入 **myAppServicePlan**，然后单击“确定”。 | |

“基本信息”选项卡应如下所示：

![显示用于配置 Web 应用的“基本信息”选项卡。](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>配置 Windows 容器

在“Docker”选项卡中，按下表所示配置自定义 Windows 容器，并选择“查看 + 创建”。

| 设置  | 建议的值 |
| ----------------- | ------------ |
|**映像源**| Azure 容器注册表 |
|**注册表**| 选择[前面创建的注册表](#publish-to-azure-container-registry)。 |
|**图像**| customfontsample |
|**标记**| 最新 |

### <a name="complete-app-creation"></a>完成应用创建

单击“创建”，等待 Azure 创建所需的资源。

## <a name="browse-to-the-web-app"></a>浏览到 Web 应用

Azure 操作完成后，会显示通知框。

![显示 Azure 操作已完成。](media/tutorial-custom-container/portal-create-finished.png)

1. 单击“转到资源”。

2. 在应用页中，单击“URL”下的链接。

如果会打开一个如以下页面所示的新浏览器页面：

![显示 Web 应用的新浏览器页面。](media/tutorial-custom-container/app-starting.png)

等待几分钟，然后重试，直到主页显示你所需的美观字体：

![使用配置的字体显示主页。](media/tutorial-custom-container/app-running.png)

祝贺你！ 你已通过 Windows 容器将 ASP.NET 应用程序迁移到 Azure 应用服务。

## <a name="see-container-start-up-logs"></a>查看容器启动日志

加载 Windows 容器可能需要一些时间。 若要查看进度，请通过将 \<app-name> 替换为应用的名称，导航到以下 URL。
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

流式传输的日志如下所示：

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Azure 应用服务使用 Docker 容器技术同时托管内置映像和自定义映像。 若要查看内置映像列表，请运行 Azure CLI 命令 ['az webapp list-runtimes --linux'](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az-webapp-list-runtimes)。 如果这些映像无法满足需要，可以生成并部署自定义映像。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 如果没有可满足需求的内置映像，请生成自定义映像
> * 将自定义映像推送到 Azure 上的专用容器注册表
> * 在应用服务中运行自定义映像
> * 配置环境变量
> * 更新并重新部署映像
> * 访问诊断日志
> * 使用 SSH 连接到容器

完成本教程会导致 Azure 帐户中产生一点容器注册表费用，并且可能会因为容器托管超过一个月而产生额外的费用。

## <a name="set-up-your-initial-environment"></a>设置初始环境

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* 安装 [Docker](https://docs.docker.com/get-started/#setup)，用于构建 Docker 映像。 安装 Docker 可能需要重新启动计算机。
* 安装 <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 或更高版本，使用它可以在任何 shell 中运行命令来预配和配置 Azure 资源。

安装 Docker 和 Azure CLI 后，打开终端窗口，验证是否已安装 docker：

```bash
docker --version
```

同时，验证 Azure CLI 版本是否为 2.0.80 或更高版本：

```azurecli
az --version
```

然后通过 CLI 登录到 Azure：

```azurecli
az login
```

`az login` 命令打开浏览器以收集凭据。 当命令完成时，会显示包含订阅相关信息的 JSON 输出。

登录后，可以使用 Azure CLI 运行 Azure 命令，处理订阅中的资源。

## <a name="clone-or-download-the-sample-app"></a>克隆或下载示例应用

可以通过 git 克隆或下载操作获取此教程的示例。

### <a name="clone-with-git"></a>使用 git 进行克隆

克隆示例存储库：

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

请确保包含 `--config core.autocrlf=input` 参数以确保 Linux 容器中使用的文件中的行尾正确：

然后转到该文件夹：

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>从 GitHub 下载

可以访问 [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux)，依次选择“克隆”和“下载 ZIP”，而不使用 git 克隆 。 

将 ZIP 文件解压缩到名为 docker-django-webapp-linux 的文件夹中。 

然后打开 docker-django-webapp-linux 文件夹中的终端窗口。

## <a name="optional-examine-the-docker-file"></a>（可选）检查 Docker 文件

示例中名为 Dockerfile 的文件，描述了 Docker 映像并包含配置说明：

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* 第一组命令在环境中安装应用的要求。
* 第二组命令创建 [SSH](https://www.ssh.com/ssh/protocol/) 服务器来实现容器和主机之间的安全通信。
* 最后一行 `ENTRYPOINT ["init.sh"]` 调用 `init.sh` 来启动 SSH 服务和 Python 服务器。

## <a name="build-and-test-the-image-locally"></a>在本地生成和测试映像

1. 运行以下命令生成映像：

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. 本地运行 Docker 容器来测试生成是否有效：

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    此 [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) 命令通过 `-p` 参数后跟映像名称来指定端口。 
    
    > [!TIP]
    > 如果在 Windows 上运行时显示错误“standard_init_linux.go:211: exec 用户进程导致‘无此类文件或目录’”，则表示 init.sh 文件包含 CR-LF 行尾而不是预期的 LF 行尾 。 如果使用 git 克隆示例存储库，但省略了 `--config core.autocrlf=input` 参数，就会发生此错误。 在这种情况下，请使用 `--config`` 参数再次克隆存储库。 如果已编辑 init.sh 并以 CRLF 结尾进行保存，也可能会发生此错误。 在这种情况下，请仅以 LF 结尾再次保存文件。

1. 浏览到 `http://localhost:8000`，验证 Web 应用和容器是否正常运行。

    ![在本地测试 Web 应用](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>创建资源组

在本部分以及后续部分中，将在 Azure 中预配要向其推送映像的资源，然后将容器部署到 Azure 应用服务。 首先，创建一个资源组，用于收集所有这些资源。

运行 [az group create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-create) 命令创建资源组：

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

可以更改 `--location` 值以指定附近的区域。

## <a name="push-the-image-to-azure-container-registry"></a>向 Azure 容器注册表推送映像

在本部分中，将映像推送到 Azure 容器注册表，应用服务可以从此注册表中对映像进行部署。

1. 运行 [`az acr create`](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az-acr-create) 命令创建 Azure 容器注册表：

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    将 `<registry-name>` 替换为适合注册表的名称。 名称只能包含字母和数字，并且必须在整个 Azure 中都是唯一的。

1. 运行 [`az acr show`](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az-acr-show) 命令以检索注册表的凭据：

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    此命令的 JSON 输出提供两个密码以及注册表的用户名。
    
1. 使用 `docker login` 命令登录到容器注册表：

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    将 `<registry-name>` 和 `<registry-username>` 替换为前面步骤中的值。 出现提示时，键入在上一步骤中获取的某个密码。

    在本部分余下的所有步骤中都使用相同的注册表名称。

1. 登录成功后，为注册表标记本地 Docker 映像：

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. 使用 `docker push` 命令将映像推送到注册表：

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    第一次上传映像可能需要几分钟时间，因为它包含基本映像。 后续上传速度通常较快。

    在等待期间，可以完成下一部分中的步骤，将应用服务配置为从注册表部署。

1. 使用 `az acr repository list` 命令验证推送是否成功：

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    输出应显示映像的名称。


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>将应用服务配置为从注册表部署映像

若要将容器部署到 Azure 应用服务，请先在应用服务上创建 Web 应用，然后将 Web 应用连接到容器注册表。 Web 应用启动时，应用服务会自动从注册表中拉取映像。

1. 使用 [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest&preserve-view=true#az-appservice-plan-create) 命令创建应用服务计划：

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    应用服务计划对应托管 Web 应用的虚拟机。 默认情况下，前面的命令使用平价的 [B1 定价层](https://azure.microsoft.com/pricing/details/app-service/linux/)，该定价层第一个月免费提供。 可以使用 `--sku` 参数控制层。

1. 使用 [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az-webapp-create) 命令创建 Web 应用：

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    将 `<app-name>` 替换为 Web 应用的名称，该名称在整个 Azure 中必须是唯一的。 同时将 `<registry-name>` 替换为上一部分中注册表的名称。

1. 使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) 按应用代码的需要设置 `WEBSITES_PORT` 环境变量： 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    将 `<app-name>` 替换为上一步使用的名称。
    
    有关此环境变量的详细信息，请参阅[示例 GitHub 存储库中的自述文件](https://github.com/Azure-Samples/docker-django-webapp-linux)。

1. 使用 [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest&preserve-view=true#az-webapp-identity-assign) 命令为 web 应用启用[托管标识](./overview-managed-identity.md)：

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    将 `<app-name>` 替换为上一步使用的名称。 命令的输出（由 `--query` 和 `--output` 参数筛选）是分配的标识的服务主体，很快就会用到它。

    通过托管标识，可以向 Web 应用授予访问其他 Azure 资源的权限，且无需任何特定凭据。

1. 使用 [`az account show`](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az-account-show) 命令检索订阅 ID，在下一步中需要用到它：

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. 授予 Web 应用访问容器注册表的权限：

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    请替换以下值：
    - 将 `<principal-id>` 替换为 `az webapp identity assign` 命令中的服务主体 ID
    - 将 `<registry-name>` 替换为容器注册表的名称
    - 将 `<subscription-id>` 替换为从 `az account show` 命令检索到的订阅 ID

有关这些权限的详细信息，请参阅[什么是 Azure 基于角色的访问控制](../role-based-access-control/overview.md)和 

## <a name="deploy-the-image-and-test-the-app"></a>部署映像并测试应用

将映像推送到容器注册表并完全预配应用服务后，即可完成这些步骤。

1. 使用 [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest&preserve-view=true#az-webapp-config-container-set) 命令指定要为 Web 应用部署的容器注册表和映像：

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    将 `<app_name>` 替换为 Web 应用的名称，并在两个位置将 `<registry-name>` 替换为注册表的名称。 

    - 在使用注册表而不是 Docker Hub 时（如本示例所示），`--docker-registry-server-url` 必须采用 `https://` 格式，后接注册表的完全限定域名。
    - 消息“未提供用于访问 Azure 容器注册表的凭据。 正在尝试查找…”指示 Azure 使用应用的托管标识进行容器注册表身份验证，而不是要求提供用户名和密码来进行验证。
    - 如果遇到错误“AttributeError：‘NoneType’对象没有‘保留’属性”，请确保 `<app-name>` 正确。

    > [!TIP]
    > 可以随时使用命令 `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` 检索 Web 应用的容器设置。 该映像在 `DOCKER_CUSTOM_IMAGE_NAME` 属性中指定。 当通过 Azure DevOps 或 Azure 资源管理器模板部署 Web 应用时，映像还可能出现在名为 `LinuxFxVersion` 的属性中。 这两个属性的作用相同。 如果 Web 应用的配置中同时存在这两个属性，则优先使用 `LinuxFxVersion`。

1. `az webapp config container set` 命令完成后，Web 应用应在应用服务上的容器中运行。

    若要测试应用，请浏览到 `http://<app-name>.azurewebsites.net`，将 `<app-name>` 替换为 Web 应用的名称。 首次访问时，应用可能需要一些时间来响应，因为应用服务必须从注册表中拉取整个映像。 如果浏览器超时，刷新页面即可。 拉取初始映像后，后续测试的运行速度将快得多。

    ![在 Azure 上成功测试 Web 应用](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>修改应用程序代码并重新部署

在本部分中，将对 Web 应用代码进行更改，重新生成容器，然后将容器推送到注册表。 然后，应用服务会自动从注册表中拉取更新后的映像，以更新运行的 Web 应用。

1. 在本地 docker-django-webapp-linux 文件夹中，打开文件 app/templates/app/index.html 。

1. 更改第一个 HTML 元素，使其与以下代码相匹配。

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. 保存所做更改。

1. 更改为 docker-django-webapp-linux 文件夹，然后重新生成映像：

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. 将映像标记中的版本号更新为 v1.0.1：

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    将 `<registry-name>` 替换为注册表的名称。

1. 将映像推送到注册表：

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. 重新启动 Web 应用：

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    将 `<app_name>` 替换为 Web 应用的名称。 重新启动后，应用服务会从容器注册表中拉取更新后的映像。

1. 通过浏览到 `http://<app-name>.azurewebsites.net` 验证更新是否已部署。

## <a name="access-diagnostic-logs"></a>访问诊断日志

1. 启用容器日志记录：

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. 启用日志流：

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    如果没有立即看到控制台日志，请在 30 秒后重新查看。

    也可通过浏览器在 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 中检查日志文件。

1. 若要随时停止日志流式处理，可键入 Ctrl+C 。

## <a name="connect-to-the-container-using-ssh"></a>使用 SSH 连接到容器

SSH 实现容器和客户端之间的安全通信。 若要启用到容器的 SSH 连接，必须针对该连接配置自定义映像。 容器运行后，可以打开 SSH 连接。

### <a name="configure-the-container-for-ssh"></a>为 SSH 配置容器

本教程中使用的示例应用在 Dockerfile 中已具有必要的配置，Dockerfile 将安装 SSH 服务器并设置登录凭据。 本部分仅供参考。 若要连接到容器，请跳到下一部分

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> 此配置不允许从外部建立到容器的连接。 只能通过 Kudu/SCM 站点使用 SSH。 Kudu/SCM 站点使用 Azure 帐户进行身份验证。

Dockerfile 还将 sshd_config 文件复制到 /etc/ssh/ 文件夹，并在容器上公开端口 2222  ：

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

端口 2222 是一个仅供内部使用的端口，仅可供专用虚拟网络的桥网络中的容器访问。 

最后，入口脚本 init.sh 启动 SSH 服务器。

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>打开到容器的 SSH 连接

1. 浏览到 `https://<app-name>.scm.azurewebsites.net/webssh/host` 并使用 Azure 帐户登录。 将 `<app-name>` 替换为 Web 应用的名称。

1. 登录后，将重定向到 Web 应用的信息页。 选择页面顶部的“SSH”，打开 shell 并使用命令。

    例如，可以使用 `top` 命令检查在其中运行的进程。
    
## <a name="clean-up-resources"></a>清理资源

本文中创建的资源可能会持续产生费用。 若要清理资源，只需删除包含这些资源的资源组：

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>后续步骤

你已了解：

> [!div class="checklist"]
> * 将自定义映像部署到专用容器注册表
> * 在应用服务中部署和自定义映像
::: zone pivot="container-linux"
> * 更新并重新部署映像
::: zone-end
> * 访问诊断日志
::: zone pivot="container-linux"
> * 使用 SSH 连接到容器
::: zone-end

在下一教程中，你将了解如何将自定义 DNS 名称映射到应用。

> [!div class="nextstepaction"]
> [教程：将自定义 DNS 名称映射到应用](app-service-web-tutorial-custom-domain.md)

或者，查看其他资源：

> [!div class="nextstepaction"]
> [配置自定义容器](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [教程：多容器 WordPress 应用](tutorial-multi-container-app.md)
::: zone-end
