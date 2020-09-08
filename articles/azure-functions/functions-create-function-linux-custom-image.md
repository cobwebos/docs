---
title: 在 Linux 上使用自定义映像创建 Azure Functions
description: 了解如何创建在自定义 Linux 映像中运行的 Azure Functions。
ms.date: 03/30/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: f068f91a104c15099809343438cc925fb8856248
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146855"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>在 Linux 上使用自定义容器创建函数

在本教程中，你将创建代码，并使用 Linux 基础映像将其作为自定义 Docker 容器部署到 Azure Functions。 当函数需要特定的语言版本，或者需要内置映像不提供的特定依赖项或配置时，通常你会使用自定义映像。

也可以根据[创建托管在 Linux 上的第一个函数](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python)中所述，使用默认的 Azure 应用服务容器。 Azure Functions 支持的基础映像可以在 [Azure Functions 基础映像存储库](https://hub.docker.com/_/microsoft-azure-functions-base)中找到。

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

可以在运行 Windows、macOS 或 Linux 的任何计算机上按照本教程所述进行操作。 完成本教程会在你的 Azure 帐户扣取几美元的费用。

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [Docker ID](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ 运行 `docker login` 登录到 Docker。 如果 Docker 未运行，则此命令会失败。在这种情况下，请启动 Docker，然后重试该命令。

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>创建并测试本地 Functions 项目

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
在终端或命令提示符中，根据所选的语言运行以下命令，在名为 `LocalFunctionsProject` 的文件夹中创建一个函数应用项目。  
::: zone-end  
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
::: zone pivot="programming-language-java"  
在空的文件夹中，运行以下命令以从 [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) 生成 Functions 项目。

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

`-DjavaVersion` 参数告诉 Functions 运行时要使用哪个 Java 版本。 如果希望函数在 Java 11（预览版）上运行，请使用 `-DjavaVersion=11`。 如果不指定 `-DjavaVersion`，则 Maven 默认使用 Java 8。 有关详细信息，请参阅 [Java 版本](functions-reference-java.md#java-versions)。

Maven 会请求你提供所需的值，以在部署上完成项目的生成。   
系统提示时提供以下值：

| Prompt | 值 | 说明 |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | 一个值，用于按照 Java 的[包命名规则](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)在所有项目中标识你的项目。 |
| **artifactId** | `fabrikam-functions` | 一个值，该值是 jar 的名称，没有版本号。 |
| **version** | `1.0-SNAPSHOT` | 选择默认值。 |
| **package** | `com.fabrikam.functions` | 一个值，该值是所生成函数代码的 Java 包。 使用默认值。 |

键入 `Y` 或按 Enter 进行确认。

Maven 在名为 artifactId 的新文件夹（在此示例中为 `fabrikam-functions`）中创建项目文件。 
::: zone-end
`--docker` 选项生成该项目的 `Dockerfile`，其中定义了适合用于 Azure Functions 和所选运行时的自定义容器。

导航到项目文件夹：
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数的唯一名称，`--template` 参数指定该函数的触发器。 `func new` 创建一个与函数名称匹配的、包含项目所选语言适用的代码文件的子文件夹，以及一个名为 *function.json* 的配置文件。

```
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end  
若要在本地测试函数，请启动项目文件夹的根目录中的本地 Azure Functions 运行时主机： 
::: zone pivot="programming-language-csharp"  
```
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
mvn clean package  
mvn azure-functions:run
```
::: zone-end
看到输出中显示了 `HttpExample` 终结点后，请导航到 `http://localhost:7071/api/HttpExample?name=Functions`。 浏览器会显示“hello”消息，该消息回显 `Functions`（提供给 `name` 查询参数的值）。

按 **Ctrl**-**C** 停止主机。

## <a name="build-the-container-image-and-test-locally"></a>生成容器映像并在本地测试

（可选）检查项目文件夹的根目录中的“Dockerfile”。 Dockerfile 描述了在 Linux 上运行函数应用所需的环境。  Azure Functions 支持的基础映像的完整列表可以在 [Azure Functions 基础映像页](https://hub.docker.com/_/microsoft-azure-functions-base)中找到。
    
在项目根文件夹中运行 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令，并提供名称 `azurefunctionsimage` 和标记 `v1.0.0`。 将 `<DOCKER_ID>` 替换为 Docker 中心帐户 ID。 此命令为容器生成 Docker 映像。

```
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

该命令完成后，可在本地运行新容器。
    
若要测试生成，请使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行本地容器中的映像，并再次将 `<DOCKER_ID` 替换为 Docker ID，同时添加端口参数 `-p 8080:80`：

```
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
该映像在本地容器中运行后，请在浏览器中打开 `http://localhost:8080`。浏览器中应会显示如下所示的占位符映像。 该映像之所以在此时显示，是因为函数在本地容器中运行（与在 Azure 中一样），这意味着，该函数由 *function.json* 中定义的访问密钥使用 `"authLevel": "function"` 属性来保护。 但是，容器尚未发布到 Azure 中的函数应用，因此该密钥尚不可用。 若要针对本地容器进行测试，请停止 Docker，将授权属性更改为 `"authLevel": "anonymous"`，重新生成映像，然后重启 Docker。 然后重置 *function.json* 中的 `"authLevel": "function"`。 有关详细信息，请参阅[授权密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。

![指示容器正在本地运行的占位符映像](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
映像在本地容器中运行后，请浏览到 `http://localhost:8080/api/HttpExample?name=Functions`，此时会显示与前面相同的“hello”消息。 由于 Maven 原型会生成一个使用匿名身份验证的 HTTP 触发函数，因此你仍可以调用该函数，即使它在容器中运行。 
::: zone-end  

验证容器中的函数应用后，按 Ctrl+C 停止 Docker 。

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
    ```
    
1. 使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 命令将此设置添加到函数应用。 在下面的命令中，将 `<app_name>` 替换为函数应用的名称，并将 `<connection_string>` 替换为上一步中的连接字符串（以“DefaultEndpointProtocol=”开头的长编码字符串）：
 
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

    1. 登录到 Azure 门户，然后搜索并选择“函数应用”。

    1. 选择要验证的函数。

    1. 在左侧导航面板中，选择“函数”，然后选择要验证的函数。

        ![在 Azure 门户中，选择你的函数](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. 选择“获取函数 URL”。

        ![从 Azure 门户获取函数 URL](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. 在弹出窗口中选择“默认(函数密钥)”，然后将 URL 复制到剪贴板。 该密钥是 `?code=` 后面的字符串。

        ![选择默认函数访问密钥](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


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

1. 将函数 URL 粘贴到浏览器的地址栏中，并将参数 `&name=Azure` 添加到此 URL 的末尾。 浏览器中会显示类似于“Hello, Azure”的文本。

    ![浏览器中的函数响应。](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. 若要测试授权，请从 URL 中删除 `code=` 参数，并验证该函数是否不返回任何响应。


## <a name="enable-continuous-deployment-to-azure"></a>启用到 Azure 的持续部署

可以启用 Azure Functions，以便每次更新注册表中的映像时，都自动更新该映像的部署。

1. 使用 [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) 命令启用持续部署（请将 `<app_name>` 替换为你的函数应用的名称）：

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    此命令启用持续部署并返回部署 Webhook URL。 （以后随时可以使用 [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) 命令检索此 URL。）

1. 将部署 Webhook URL 复制到剪贴板。

1. 打开 [Docker Hub](https://hub.docker.com/) 并登录，然后在导航栏上选择“存储库”。 找到并选择映像，选择“Webhook”选项卡，指定一个 **Webhook 名称**，将 URL 粘贴到“Webhook URL”中，然后选择“创建”：

    ![将 Webhook 添加到 DockerHub 存储库中](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. 设置 Webhook 后，每当在 Docker Hub 中更新映像时，Azure Functions 就会重新部署该映像。

## <a name="enable-ssh-connections"></a>启用 SSH 连接

SSH 实现容器和客户端之间的安全通信。 启用 SSH 后，可以使用应用服务高级工具 (Kudu) 连接到容器。 为了便于使用 SSH 连接到容器，Azure Functions 提供了已启用 SSH 的基础映像。 只需编辑 Dockerfile，然后重新生成并重新部署映像即可。 然后，可以通过高级工具 (Kudu) 连接到容器

1. 在 Dockerfile 中，将字符串 `-appservice` 追加到 `FROM` 指令中的基础映像：

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
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

1. 登录到你的 Azure 帐户，然后选择“SSH”以便与容器建立连接。 如果 Azure 仍在更新容器映像，则连接可能需要一段时间。

1. 与容器建立连接后，运行 `top` 命令查看当前正在运行的进程。 

    ![在 SSH 会话中运行的 Linux top 命令](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>写入 Azure 存储队列

借助 Azure Functions，无需自行编写集成代码即可将函数连接到其他 Azure 服务和资源。 这些绑定表示输入和输出，在函数定义中声明。 绑定中的数据作为参数提供给函数。 触发器是一种特殊类型的输入绑定。 尽管一个函数只有一个触发器，但它可以有多个输入和输出绑定。 有关详细信息，请参阅 [Azure Functions 触发器和绑定的概念](functions-triggers-bindings.md)。

本部分介绍如何将函数与 Azure 存储队列集成。 添加到此函数的输出绑定会将 HTTP 请求中的数据写入到队列中的消息。

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

## <a name="add-code-to-use-the-output-binding"></a>添加使用输出绑定的代码

定义队列绑定后，可以更新函数，以接收 `msg` 输出参数并将消息写入队列。

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

### <a name="update-the-image-in-the-registry"></a>更新注册表中的映像

1. 在根文件夹中再次运行 `docker build`，但这次请将标记中的版本更新为 `v1.0.1`。 如前所述，将 `<docker_id>` 替换为你的 Docker Hub 帐户 ID：

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. 使用 `docker push` 将更新的映像推回到存储库：

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. 由于已配置持续交付，因此再次更新注册表中的映像会自动更新 Azure 中的函数应用。

## <a name="view-the-message-in-the-azure-storage-queue"></a>查看 Azure 存储队列中的消息

在浏览器中，使用前面所述的同一 URL 来调用函数。 浏览器应会显示与前面相同的响应，因为尚未修改函数代码的该部分。 但是，添加的代码已使用 `name` URL 参数将消息写入 `outqueue` 存储队列。

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

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
