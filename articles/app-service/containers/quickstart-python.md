---
title: 快速入门：创建 Linux Python 应用
description: 将第一个 Python 应用部署到 Azure 应用服务中的 Linux 容器即可开始使用应用服务上的 Linux 应用。
ms.topic: quickstart
ms.date: 06/30/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
ms.openlocfilehash: 804ed6084454a70fa6fc4edff9a0579e43a3a872
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853477"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>快速入门：在 Linux 上的 Azure 应用服务中创建 Python 应用

在本快速入门中，需将 Python Web 应用部署到 [Linux 上的应用服务](app-service-linux-intro.md)，该版本提供了一项高度可缩放、自我修补的 Azure Web 托管服务。 在 Mac、Linux 或 Windows 计算机上使用本地 [Azure 命令行接口 (CLI)](/cli/azure/install-azure-cli)。 配置的 Web 应用使用免费的应用服务层，因此本文中的操作不会产生任何费用。

如果希望通过 IDE 部署应用，请参阅[从 Visual Studio Code 将 Python 应用部署到应用服务](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)。

## <a name="set-up-your-initial-environment"></a>设置初始环境

在开始之前，必须满足以下条件：

1. 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
1. 安装 <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 或更高版本</a>。
1. 安装 <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 或更高版本，使用它可以在任何 shell 中运行命令来预配和配置 Azure 资源。

打开终端窗口并检查 Python 版本是否为 3.6 或更高版本：

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

检查 Azure CLI 版本是否为 2.0.80 或更高版本：

```azurecli
az --version
```

然后通过 CLI 登录到 Azure：

```azurecli
az login
```

此命令将打开浏览器以获取凭据。 命令完成后，会显示包含订阅相关信息的 JSON 输出。

登录后，可以使用 Azure CLI 运行 Azure 命令，处理订阅中的资源。

## <a name="clone-the-sample"></a>克隆示例

使用以下命令克隆示例存储库。 （如果尚未安装 git，请[安装 git](https://git-scm.com/downloads)。）

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

然后转到该文件夹：

```terminal
cd python-docs-hello-world
```

示例代码包含一个 application.py 文件，该文件会告知应用服务：代码包含 Flask 应用。 有关详细信息，请参阅[容器启动过程和自定义项](how-to-configure-python.md)。

## <a name="run-the-sample"></a>运行示例

# <a name="bash"></a>[Bash](#tab/bash)

首先，创建虚拟环境并安装依赖项：

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

然后，将 `FLASK_APP` 环境变量设置为应用的条目模块，并运行 Flask 开发服务器：

```
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

首先，创建虚拟环境并安装依赖项：

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

然后，将 `FLASK_APP` 环境变量设置为应用的条目模块，并运行 Flask 开发服务器：

```powershell
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

首先，创建虚拟环境并安装依赖项：

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

然后，将 `FLASK_APP` 环境变量设置为应用的条目模块，并运行 Flask 开发服务器：

```cmd
SET FLASK_APP=application.py
flask run
```

---

打开 Web 浏览器并转到 `http://localhost:5000/` 处的示例应用。 应用显示“Hello World!”消息  。

![在本地运行示例 Python 应用](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

在终端窗口中，按“Ctrl+C”退出 Flask 开发服务器 。

## <a name="deploy-the-sample"></a>部署示例

使用 `az webapp up` 命令在本地文件夹 (python-docs-hello-world) 中部署代码：

```azurecli
az webapp up --sku F1 -n <app-name>
```

- 如果无法识别 `az` 命令，请确保按照[设置初始环境](#set-up-your-initial-environment)中所述安装 Azure CLI。
- 将 `<app_name>` 替换为在整个 Azure 中均唯一的名称（有效字符为 `a-z`、`0-9` 和 `-`）。 良好的模式是结合使用公司名称和应用标识符。
- `--sku F1` 参数在“免费”定价层上创建 Web 应用。 省略此参数可使用更快的高级层，这会按小时计费。
- 可以选择性地包含参数 `-l <location-name>`，其中，`<location_name>` 是 Azure 区域，例如 centralus、eastasia、westeurope、koreasouth、brazilsouth、centralindia等。 可以运行 [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) 命令来检索 Azure 帐户的允许区域列表。

此命令可能需要花费几分钟时间完成。 运行时，它提供以下相关信息：创建资源组、应用服务计划和托管应用，配置日志记录，然后执行 ZIP 部署。 然后，它将显示消息“可以在 http://&lt;app-name&gt;.azurewebsites.net（这是 Azure 上应用的 URL）启动应用”。

![az webapp up 命令的示例输出](./media/quickstart-python/az-webapp-up-output.png)

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>浏览到应用

在 Web 浏览器中使用以下 URL 浏览到已部署的应用程序：`http://<app-name>.azurewebsites.net`。

Python 示例代码在使用内置映像的应用服务中运行 Linux 容器。

![在 Azure 中运行示例 Python 应用](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

祝贺你！ 现已将 Python 应用部署到 Linux 上的应用服务。

## <a name="redeploy-updates"></a>重新部署更新

在偏好的代码编辑器中打开 application.py，并按如下所示更新 `hello` 函数。 此项更改会添加一个 `print` 语句，用于生成要在下一部分使用的日志记录输出。 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

保存更改并退出编辑器。 

再次使用 `az webapp up` 命令，重新部署应用：

```azurecli
az webapp up
```

此命令使用本地缓存在 .azure/config 文件中的值，包括应用名称、资源组和应用服务计划。

部署完成后，切换回打开到 `http://<app-name>.azurewebsites.net` 的浏览器窗口。 刷新页面，刷新后的页面应显示修改后的消息：

![在 Azure 中运行更新的示例 Python 应用](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code 为 Python 和 Azure 应用服务提供了功能强大的扩展，简化了将 Python Web 应用部署到应用服务的过程。 有关详细信息，请参阅[将 Python 应用从 Visual Studio Code 部署到应用服务](/azure/python/tutorial-deploy-app-service-on-linux-01)。

## <a name="stream-logs"></a>流式传输日志

可以访问应用内和运行应用的容器所生成的控制台日志。 这些日志包括使用 `print` 语句生成的任何输出。

若要流式传输日志，请运行以下命令：

```azurecli
az webapp log tail
```

在浏览器中刷新应用以生成控制台日志，其中包括描述对应用的 HTTP 请求的消息。 如果未立即显示输出，请在 30 秒后重试。

也可通过浏览器在 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 中检查日志文件。

若要随时停止日志流式处理，可键入 Ctrl+C 。

## <a name="manage-the-azure-app"></a>管理 Azure 应用

转到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>管理已创建的应用。 搜索并选择“应用服务”。

![在 Azure 门户中导航到应用服务](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

选择 Azure 应用名称。

![在 Azure 门户的应用程序服务中导航到 Python 应用](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

选择该应用将打开其“概述”页，你可以在其中执行基本的管理任务，例如浏览、停止、启动、重启和删除。

![在 Azure 门户的“概述”页中管理 Python 应用](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

应用服务菜单提供了用于配置应用的不同页面。

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，你在资源组中创建了 Azure 资源。 资源组根据位置得名，例如“appsvc_rg_Linux_CentralUS”。 如果使用 F1 免费层以外的应用服务 SKU，这些资源会持续产生费用（请参阅[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/linux/)）。

如果你认为将来不再需要这些资源，请运行以下命令删除资源组：

```azurecli
az group delete
```

此命令使用 .azure/config 文件中缓存的资源组名称。

此命令可能需要一分钟才能完成。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 PostgreSQL 的 Python (Django) Web 应用](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [将用户登录添加到 Python Web 应用](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [配置 Python 应用](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [教程：在自定义容器中运行 Python 应用](tutorial-custom-docker-image.md)
