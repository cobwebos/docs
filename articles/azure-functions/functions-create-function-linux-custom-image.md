---
title: 在 Linux 上使用自定义映像创建 Azure Functions
description: 了解如何创建在自定义 Linux 映像中运行的 Azure Functions。
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions01
ms.openlocfilehash: b714806c163a94bbae7069c357e603b82ba797ba
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482354"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>在 Linux 上使用自定义容器创建函数

在本教程中，你将创建 Python 代码，并使用 Linux 基础映像将其作为自定义 Docker 容器部署到 Azure Functions。 当函数需要特定的语言版本，或者需要内置映像不提供的特定依赖项或配置时，通常你会使用自定义映像。

也可以根据[创建托管在 Linux 上的第一个函数](functions-create-first-azure-function-azure-cli-linux.md)中所述，使用默认的 Azure 应用服务容器。 Azure Functions 支持的基础映像可以在 [Azure Functions 基础映像存储库](https://hub.docker.com/_/microsoft-azure-functions-base)中找到。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 Azure Functions Core Tools 创建函数应用和 Dockerfile。
> * 使用 Docker 生成自定义映像。
> * 将自定义映像发布到容器注册表。
> * 在 Azure 中创建函数应用的支持性资源
> * 从 Docker 中心部署函数应用。
> * 将应用程序设置添加到函数应用。
> * 启用持续部署。
> * 与容器建立 SSH 连接。
> * 添加队列存储输出绑定。 

可以在运行 Windows、Mac OS 或 Linux 的任何计算机上按照本教程所述进行操作。 完成本教程会在你的 Azure 帐户扣取几美元的费用。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846 或更高版本
- [Azure CLI](/cli/azure/install-azure-cli) 2.0.77 或更高版本
- [Azure Functions 2.x 运行时](functions-versions.md)
- 以下语言运行时组件：
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2.x 或更高版本](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3.6 - 64 位](https://www.python.org/downloads/release/python-3610/)或 [Python 3.7 - 64 位](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- [Docker ID](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>先决条件检查

1. 在终端或命令窗口中，运行 `func --version` 检查 Azure Functions Core Tools 的版本是否为 2.7.1846 或以上。
1. 运行 `az --version` 检查 Azure CLI 版本是否为 2.0.76 或以上。
1. 运行 `az login` 登录到 Azure 并验证活动订阅。
1. 运行 `docker login` 登录到 Docker。 如果 Docker 未运行，则此命令将会失败。在这种情况下，请启动 Docker，然后重试该命令。

## <a name="create-and-test-the-local-functions-project"></a>创建并测试本地 Functions 项目

1. 在终端中或命令提示符下，在适当的位置创建用于本教程的文件夹，然后导航到该文件夹。

1. 按照有关[创建和激活虚拟环境](functions-create-first-function-python.md#create-and-activate-a-virtual-environment)的说明创建用于本教程的虚拟环境。

1. 根据所选的语言运行以下命令，在某个文件夹中创建名为 `LocalFunctionsProject` 的函数应用项目。 `--docker` 选项生成该项目的 `Dockerfile`，其中定义了适合用于 Azure Functions 和所选运行时的自定义容器。

    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionsProject --worker-runtime dotnet --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language javascript --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionsProject --worker-runtime powershell --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionsProject --worker-runtime python --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language typescript --docker
    ```
    ::: zone-end
    
1. 导航到项目文件夹：

    ```
    cd LocalFunctionsProject
    ```
    
1. 使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数的唯一名称，`--template` 参数指定该函数的触发器。 `func new` 创建一个与函数名称匹配的、包含项目所选语言适用的代码文件的子文件夹，以及一个名为 *function.json* 的配置文件。

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. 若要在本地测试函数，请在 *LocalFunctionsProject* 文件夹中启动本地 Azure Functions 运行时主机：
   
    ::: zone pivot="programming-language-csharp"
    ```
    func start --build
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func start
    ```
    ::: zone-end    

    ::: zone pivot="programming-language-typescript"
    ```
    npm install
    ```

    ```
    npm start
    ```
    ::: zone-end

1. 看到输出中显示了 `HttpExample` 终结点后，请导航到 `http://localhost:7071/api/HttpExample?name=Functions`。 浏览器中应会显示类似于“Hello，Functions”的消息（根据所选编程语言而略有不同）。

1. 按 **Ctrl**-**C** 停止主机。

## <a name="build-the-container-image-and-test-locally"></a>生成容器映像并在本地测试

1. （可选）检查 *LocalFunctionsProj* 文件夹中的“Dockerfile”。 Dockerfile 描述了在 Linux 上运行函数应用所需的环境： 

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk AS installer-env

    COPY . /src/dotnet-function-app
    RUN cd /src/dotnet-function-app && \
        mkdir -p /home/site/wwwroot && \
        dotnet publish *.csproj --output /home/site/wwwroot
    
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/dotnet:2.0-appservice 
    FROM mcr.microsoft.com/azure-functions/dotnet:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/powershell:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY requirements.txt /
    RUN pip install -r /requirements.txt
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    > [!NOTE]
    > Azure Functions 支持的基础映像的完整列表可以在 [Azure Functions 基础映像页](https://hub.docker.com/_/microsoft-azure-functions-base)中找到。
    
1. 在 *LocalFunctionsProject* 文件夹中运行 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令，并提供名称 `azurefunctionsimage` 和标记 `v1.0.0`。 将 `<docker_id>` 替换为 Docker 中心帐户 ID。 此命令为容器生成 Docker 映像。

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    该命令完成后，可在本地运行新容器。
    
1. 若要测试生成，请使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行本地容器中的映像，并再次将 `<docker_id>` 替换为 Docker ID，同时添加端口参数 `-p 8080:80`：

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. 该映像在本地容器中运行后，请在浏览器中打开 `http://localhost:8080`。浏览器中应会显示如下所示的占位符映像。 该映像之所以在此时显示，是因为函数在本地容器中运行（与在 Azure 中一样），这意味着，该函数由 *function.json* 中定义的访问密钥使用 `"authLevel": "function"` 属性来保护。 但是，容器尚未发布到 Azure 中的函数应用，因此该密钥尚不可用。 若要在本地进行测试，请停止 Docker，将授权属性更改为 `"authLevel": "anonymous"`，重新生成映像，然后重启 Docker。 然后重置 *function.json* 中的 `"authLevel": "function"`。 有关详细信息，请参阅[授权密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。

    ![指示容器正在本地运行的占位符映像](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. 验证容器中的函数应用后，按 **Ctrl**+**C** 停止 Docker。

## <a name="push-the-image-to-docker-hub"></a>将映像推送到 Docker Hub

Docker Hub 是托管映像并提供映像和容器服务的容器注册表。 若要共享映像（包括将映像部署到 Azure），必须将其推送到注册表。

1. 如果尚未登录到 Docker，请使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令登录（将 `<docker_id>` 替换为你的 Docker ID）。 此命令会提示输入用户名和密码。 如果出现“登录成功”消息，则表示已登录。

    ```
    docker login
    ```
    
1. 登录后，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令将映像推送到 Docker Hub（同样，请将 `<docker_id>` 替换为你的 Docker ID）。

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. 首次推送映像可能需要几分钟时间，具体取决于网络速度（推送后续的更改要快得多）。 在等待过程中，可以继续学习下一部分，并在另一个终端中创建 Azure 资源。

## <a name="create-supporting-azure-resources-for-your-function"></a>创建函数的支持性 Azure 资源

若要将函数代码部署到 Azure，需要创建三个资源：

- 一个资源组：相关资源的逻辑容器。
- 一个 Azure 存储帐户：维护有关项目的状态和其他信息。
- 一个 Azure Functions 应用：提供用于执行函数代码的环境。 函数应用映射到本地函数项目，可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。

使用 Azure CLI 命令创建这些项。 完成后，每个命令将提供 JSON 输出。

1. 使用 [az login](/cli/azure/reference-index#az-login) 命令登录到 Azure：

    ```azurecli
    az login
    ```
    
1. 使用“[az group create](/cli/azure/group#az-group-create)”命令创建资源组。 以下示例在 `westeurope` 区域中创建名为 `AzureFunctionsContainers-rg` 的资源组。 （通常，你会在 `az account list-locations` 命令输出的、与你靠近的某个可用区域中创建资源组和资源。）

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > 不能在同一资源组中托管 Linux 和 Windows 应用。 如果名为 `AzureFunctionsContainers-rg` 的现有资源组有 Windows 函数应用或 Web 应用，必须使用其他资源组。
    
1. 使用 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令在资源组和区域中创建常规用途存储帐户。 在以下示例中，请将 `<storage_name>` 替换为适合自己的全局唯一名称。 名称只能包含 3 到 24 个数字和小写字母字符。 `Standard_LRS` 指定典型的常规用途帐户。

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    在本教程中使用的存储帐户只会产生几美分的费用。
    
1. 使用以下命令在“西欧”区域（`-location westeurope`，或使用附近的适当区域）和 Linux 容器 (`--is-linux`) 中的“弹性高级版 1”定价层 (`--sku EP1`) 中为 Azure Functions 创建名为 `myPremiumPlan` 的高级计划。 

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Linux 托管[专用（应用服务）计划](functions-scale.md#app-service-plan)和[高级计划](functions-premium-plan.md#features)支持的自定义 Functions 容器。 此处我们使用了可按需缩放的高级计划。 若要了解有关托管的详细信息，请参阅 [Azure Functions 托管计划比较](functions-scale.md)。 若要计算费用，请参阅 [Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)。

    该命令还会在同一资源组中预配关联的 Azure Application Insights 实例，可以使用它来监视函数应用和查看日志。 有关详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。 该实例在激活之前不会产生费用。

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>使用映像在 Azure 上创建并配置函数应用

Azure 上的函数应用管理托管计划中函数的执行。 在本部分，你将使用在上一部分创建的 Azure 资源，基于 Docker Hub 中的某个映像创建一个函数应用，然后使用 Azure 存储的连接字符串对其进行配置。

1. 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令创建 Functions 应用。 在以下示例中，请将 `<storage_name>` 替换为在上一部分中用于存储帐户的名称。 另外，请将 `<app_name>` 替换为适合自己的全局唯一名称，并将 `<docker_id>` 替换为你的 Docker ID。

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    *deployment-container-image-name* 参数指定用于函数应用的映像。 可以使用 [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) 命令查看用于部署的映像的相关信息。 还可以使用 [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) 命令从另一映像进行部署。

1. 使用 [az storage account show-connection-string](/cli/azure/storage/account) 命令检索创建的存储帐户的连接字符串，并将其分配到 shell 变量 `storageConnectionString`：

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    
1. Add this setting to the function app by using the [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command. In the following command, replace `<app_name>` with the name of your function app, and replace `<connection_string>` with the connection string from the previous step (a long encoded string that begins with "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. 该函数现在可以使用此连接字符串来访问存储帐户。

> [!TIP]
> 在 bash 中，可以使用 shell 变量来捕获连接字符串，而无需使用剪贴板。 首先，使用以下命令创建包含连接字符串的变量：
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> 然后，在第二个命令中引用该变量：
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> 如果将自定义映像发布到专用容器帐户，则应改为在连接字符串的 Dockerfile 中使用环境变量。 有关详细信息，请参阅 [ENV 指令](https://docs.docker.com/engine/reference/builder/#env)。 另外，应设置变量 `DOCKER_REGISTRY_SERVER_USERNAME` 和 `DOCKER_REGISTRY_SERVER_PASSWORD`。 若要使用这些值，必须重新生成映像，将映像推送到注册表，然后在 Azure 上重启函数应用。

## <a name="verify-your-functions-on-azure"></a>在 Azure 上验证函数

将映像部署到 Azure 上的函数应用后，以通过 HTTP 请求调用函数。 由于 *function.json* 定义包含 `"authLevel": "function"`属性，因此必须先获取访问密钥（也称为“函数密钥”），并在对终结点发出的任何请求中以 URL 参数的形式包含该密钥。

1. 使用 Azure 门户或者在 Azure CLI 中使用 `az rest` 命令检索包含访问（函数）密钥的函数 URL。

    # <a name="portal"></a>[门户](#tab/portal)

    1. 登录到 Azure 门户，在页面顶部的“搜索”框中输入函数应用的名称找到该函数应用。  在结果中选择“应用服务”资源。 

    1. 在左侧导航面板中的“函数(只读)”下，选择你的函数的名称。 

    1. 在详细信息面板中，选择“</> 获取函数 URL”： 
    
        ![Azure 门户上的“获取函数 URL”命令](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. 在弹出窗口中，依次选择“默认(函数密钥)”、“复制”。   该密钥是 `?code=` 后面的字符串。

        ![从 Azure 门户复制函数 URL](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > 由于函数应用将部署为容器，因此无法在门户中对函数代码进行更改。 必须更新本地映像中的项目，再次将该映像推送到注册表，然后重新部署到 Azure。 可以在后面的部分设置持续部署。
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. 构造采用以下格式的 URL 字符串（请将 `<subscription_id>`、`<resource_group>` 和 `<app_name>` 分别替换为你的 Azure 订阅 ID、函数应用的资源组，以及函数应用的名称）：

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        例如，URL 可能类似于以下地址：

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > 为方便起见，可将 URL 分配到某个环境变量，并在 `az rest` 命令中使用该变量。
    
    1. 运行以下 `az rest` 命令（在 Azure CLI 2.0.77 和更高版本中可用）（请将 `<uri>` 替换为在上一步骤中获取的 URI 字符串，包括引号）：

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. 该命令的输出即为函数密钥。 那么，完整的函数 URL 是 `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`（请将 `<app_name>`、`<function_name>` 和 `<key>` 替换为你的特定值）。
    
        > [!NOTE]
        > 此处检索的密钥是适用于 Functions 应用中所有函数的宿主密钥；为门户显示的方法只检索一个函数的密钥。 

    ---

1. 将函数 URL 粘贴到浏览器的地址栏中，并将参数 `&name=Azure` 添加到此 URL 的末尾。 浏览器中应会显示类似于“Hello Azure”的文本。

    ![浏览器中的函数响应。](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. 若要测试授权，请从 URL 中删除 code= 参数，并验证该函数是否不返回任何响应。


## <a name="enable-continuous-deployment-to-azure"></a>启用到 Azure 的持续部署

可以启用 Azure Functions，以便每次更新注册表中的映像时，都自动更新该映像的部署。

1. 使用 [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) 命令启用持续部署（请将 `<app_name>` 替换为你的函数应用的名称）：

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    此命令启用持续部署并返回部署 Webhook URL。 （以后随时可以使用 [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) 命令检索此 URL。）

1. 将部署 Webhook URL 复制到剪贴板。

1. 打开 [Docker Hub](https://hub.docker.com/) 并登录，然后在导航栏上选择“存储库”。  找到并选择映像，选择“Webhook”选项卡，指定一个 **Webhook 名称**，将 URL 粘贴到“Webhook URL”中，然后选择“创建”：   

    ![将 Webhook 添加到 DockerHub 存储库中](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. 设置 Webhook 后，每当在 Docker Hub 中更新映像时，Azure Functions 就会重新部署该映像。

## <a name="enable-ssh-connections"></a>启用 SSH 连接

SSH 实现容器和客户端之间的安全通信。 启用 SSH 后，可以使用应用服务高级工具 (Kudu) 连接到容器。 为了便于使用 SSH 连接到容器，Azure Functions 提供了已启用 SSH 的基础映像。 只需编辑 Dockerfile，然后重新生成并重新部署映像即可。 然后，可以通过高级工具 (Kudu) 连接到容器

1. 在 Dockerfile 中，将字符串 `-appservice` 追加到 `FROM` 指令中的基础映像：

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    [应用服务 - 自定义 Docker 映像教程](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections)中介绍了基础映像之间的差异。

1. 使用 `docker build` 命令重新生成映像（同样，请将 `<docker_id>` 替换为你的 Docker ID）：

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. 将更新的映像推送到 Docker Hub。此过程所需的时间远远少于首次推送，因为它只需上传更新的映像段。

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions 会自动将映像重新部署到 Functions 应用；此过程在一分钟内即可完成。

1. 在浏览器中打开 `https://<app_name>.scm.azurewebsites.net/`（请将 `<app_name>` 替换为你的唯一名称）。 此 URL 是函数应用容器的高级工具 (Kudu) 终结点。

1. 登录到你的 Azure 帐户，然后选择“SSH”以便与容器建立连接。  如果 Azure 仍在更新容器映像，则连接可能需要一段时间。

1. 与容器建立连接后，运行 `top` 命令查看当前正在运行的进程。 

    ![在 SSH 会话中运行的 Linux top 命令](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>写入 Azure 存储队列

无需编写自己的集成代码，即可使用 Azure Functions 将函数连接到其他 Azure 服务和资源。 这些绑定表示输入和输出，在函数定义中声明。  绑定中的数据作为参数提供给函数。 触发器是一种特殊类型的输入绑定。  尽管一个函数只有一个触发器，但它可以有多个输入和输出绑定。 有关详细信息，请参阅 [Azure Functions 触发器和绑定的概念](functions-triggers-bindings.md)。

本部分介绍如何将函数与 Azure 存储队列集成。 添加到此函数的输出绑定会将 HTTP 请求中的数据写入到队列中的消息。

## <a name="retrieve-the-azure-storage-connection-string"></a>检索 Azure 存储连接字符串

前面你已创建一个供函数应用使用的 Azure 存储帐户。 此帐户的连接字符串安全存储在 Azure 中的应用设置内。 将设置下载到 *local.settings.json* 文件中后，在本地运行函数时，可以在同一帐户中使用该连接写入存储队列。 

1. 在项目的根目录中运行以下命令（请将 `<app_name>` 替换为前一篇快速入门中所用的函数应用名称）。 此命令将覆盖该文件中的任何现有值。

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. 打开 *local.settings.json*，找到名为 `AzureWebJobsStorage` 的值，即存储帐户连接字符串。 在本文的其他部分，将使用名称 `AzureWebJobsStorage` 和该连接字符串。

> [!IMPORTANT]
> 由于 *local.settings.json* 包含从 Azure 下载的机密，因此请始终从源代码管理中排除此文件。 连同本地函数项目一起创建的 *.gitignore* 文件默认会排除该文件。

### <a name="add-an-output-binding-to-functionjson"></a>将输出绑定添加到 function.json

在 Azure Functions 中，每种类型的绑定都需要一个 `direction`、`type`，以及要在 *function.json* 文件中定义的唯一 `name`。 *function.json* 已包含“httpTrigger”类型的输入绑定，以及 HTTP 响应的输出绑定。 若要将绑定添加到存储队列，请按如下所示修改该文件，为“queue”类型（其中的队列作为名为 `msg`的输入参数显示在代码中）添加输出绑定。 队列绑定还要求使用队列的名称（在本例中为 `outqueue`），以及用于保存连接字符串的设置的名称（在本例中为 `AzureWebJobStorage`）。

::: zone pivot="programming-language-csharp"

在 C# 类库项目中，绑定被定义为函数方法上的绑定属性。 然后根据这些属性自动生成 *function.json* 文件。

1. 对于队列绑定，请运行以下 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 命令，将存储扩展包添加到项目中。

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. 打开 *HttpTrigger.cs* 文件并添加以下 `using` 语句：

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. 将以下参数添加到 `Run` 方法定义：
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    `Run` 方法定义现在应与以下代码相匹配：
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

`msg` 参数为 `ICollector<T>` 类型，表示函数完成时写入输出绑定的消息集合。 在这种情况下，输出是名为的 `outqueue` 存储队列。 存储帐户的连接字符串由 `StorageAccountAttribute` 设置。 此属性指示包含存储帐户连接字符串的设置，可以在类、方法或参数级别应用。 在本例中，可以省略 `StorageAccountAttribute`，因为你已使用默认存储帐户。

::: zone-end

::: zone pivot="programming-language-javascript"

通过在 HTTP 绑定后面添加队列绑定来更新 *function.json*，以便与以下内容相匹配：

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-powershell"

通过在 HTTP 绑定后面添加队列绑定来更新 *function.json*，以便与以下内容相匹配：

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-python"

通过在 HTTP 绑定后面添加队列绑定来更新 *function.json*，以便与以下内容相匹配：

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-typescript"

通过在 HTTP 绑定后面添加队列绑定来更新 *function.json*，以便与以下内容相匹配：

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

## <a name="add-code-to-use-the-output-binding"></a>添加使用输出绑定的代码

定义绑定后，绑定的名称（在本例中为 `msg`）将作为参数显示在函数代码中（或者 JavaScript 和 TypeScript 的 `context` 对象中）。 然后，可以使用该变量将消息写入队列。 需要编写任何用于身份验证、获取队列引用或写入数据的代码。 在 Azure Functions 运行时和队列输出绑定中可以方便地处理所有这些集成任务。

::: zone pivot="programming-language-csharp"
```csharp
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```
::: zone-end

::: zone pivot="programming-language-javascript"
```js
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);

        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```
::: zone-end

::: zone pivot="programming-language-powershell"
```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```
::: zone-end

::: zone pivot="programming-language-python"
```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```
::: zone-end

::: zone pivot="programming-language-typescript"
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);
        
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```
::: zone-end

### <a name="update-the-image-in-the-registry"></a>更新注册表中的映像

1. 在根文件夹中再次运行 `docker build`，但这次请将标记中的版本更新为 `v1.0.1`。 如前所述，将 `<docker_id>` 替换为你的 Docker Hub 帐户 ID：

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. 使用 `docker push` 将更新的映像推回到存储库：

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. 由于已配置持续交付，因此再次更新注册表中的映像会自动更新 Azure 中的函数应用。

## <a name="view-the-message-in-the-azure-storage-queue"></a>查看 Azure 存储队列中的消息

在浏览器中，使用前面所述的同一 URL 来调用函数。 浏览器应会显示与前面相同的响应，因为尚未修改函数代码的该部分。 但是，添加的代码已使用 `name` URL 参数将消息写入 `outqueue` 存储队列。

可以在 [Azure 门户](../storage/queues/storage-quickstart-queues-portal.md)或 [Microsoft Azure 存储资源管理器](https://storageexplorer.com/)中查看队列。 也可以按以下步骤中所述，在 Azure CLI 中查看队列：

1. 打开函数项目的 *local.setting.json* 文件，并复制连接字符串值。 在终端或命令窗口中运行以下命令，以创建名为 `AZURE_STORAGE_CONNECTION_STRING` 的环境变量（请在 `<connection_string>` 位置粘贴特定的连接字符串）。 （创建此环境变量后，无需在使用 `--connection-string` 参数的每个后续命令中提供连接字符串。）

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. （可选）使用 [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 命令查看帐户中的存储队列。 此命令的输出应包含名为 `outqueue` 的队列，该队列是函数将其第一条消息写入该队列时创建的。
    
    # <a name="bash"></a>[bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. 使用 [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) 命令查看此队列中的消息，队列名称应是前面在测试函数时使用的名称。 该命令检索队列中采用 [base64 编码](functions-bindings-storage-queue-trigger.md#encoding)的第一条消息，因此，还必须将此消息解码，才能以文本格式查看它。

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    由于需要取消引用消息集合并从 base64 解码，因此请运行 PowerShell 并使用 PowerShell 命令。

    ---

## <a name="clean-up-resources"></a>清理资源

若要继续使用在本教程中创建的资源来操作 Azure Functions，可以保留所有这些资源。 由于为 Azure Functions 创建了高级计划，因此每天会产生一到两美元的费用。

若要避免持续性的费用，请删除 `AzureFunctionsContainer-rg` 资源组以清理其中的所有资源： 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>后续步骤

+ [监视函数](functions-monitoring.md)
+ [缩放和托管选项](functions-scale.md)
+ [基于 Kubernetes 的无服务器托管](functions-kubernetes-keda.md)
