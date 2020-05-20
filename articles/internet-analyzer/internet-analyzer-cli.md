---
title: 使用 CLI 创建 Internet 分析器测试 | Microsoft Docs
description: 本文介绍如何创建第一个 Internet 分析器测试。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: d474442086e2a114f26df279ab2682cd7628a5f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "74184270"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>使用 CLI（预览版）创建 Internet 分析器测试

创建 Internet 分析器资源有两种方法：使用 [Azure 门户](internet-analyzer-create-test-portal.md)或使用 CLI。 本本分将介绍如何使用 CLI 体验创建新的 Azure Internet 分析器资源。 


> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>开始之前

公开预览版可以全局使用；但是，在预览期间，只有美国西部 2 支持数据存储  。

## <a name="object-model"></a>对象模型
Internet 分析器 CLI 将公开以下类型的资源：
* **测试** - 测试比较了两个 Internet 终结点（A 和 B）随时间变化的最终用户性能。
* **配置文件** - 测试是在 Internet 分析器配置文件下创建的。 配置文件允许对相关测试进行分组；单个配置文件可以包含一个或多个测试。
* **预配置的终结点** - 我们已使用各种配置（区域、加速技术等）设置了终结点。 你可以在测试中使用任何这些预配置的终结点。
* **记分卡** - 记分卡提供了快速而有意义的测量结果摘要。 请参阅[解释记分卡](internet-analyzer-scorecard.md)。
* **时序** - 时序显示指标如何随时间变化。

## <a name="profile-and-test-creation"></a>配置文件和测试创建
1. 按照 [Azure Internet 分析器常见问题解答](internet-analyzer-faq.md)的“如何参与预览？”中的说明获得 Internet 分析器的预览版访问权限。
2. [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
3. 运行 `login` 命令以启动 CLI 会话：
    ```azurecli-interactive
    az login
    ```

    如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。
    否则，请在 https://aka.ms/devicelogin 处打开浏览器页，然后输入终端中显示的授权代码。

4. 在浏览器中使用帐户凭据登录。

5. 选择已获得 Internet 分析器公共预览版访问权限的订阅 ID。

    登录后，你将看到与你的 Azure 帐户关联的订阅列表。 在使用 `isDefault: true` 的情况下显示的订阅信息是登录后当前已激活的订阅。 若要选择另一个订阅，请将 [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) 命令与要切换到的订阅 ID 配合使用。 有关订阅选择的详细信息，请参阅[使用多个 Azure 订阅](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。

    有多种方法可用来以非交互方式登录，[使用 Azure CLI 登录](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)中详细介绍了这些方法。

6.  [可选] 创建新的 Azure 资源组：
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. 安装 Azure CLI Internet 分析器扩展：
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. 创建新的 Internet 分析器配置文件：
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. 列出所有可用于新创建的配置文件的预配置终结点：
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. 在新创建的 InternetAnalyzer 配置文件下创建新测试：
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    上面的命令假设 `www.contoso.com` 和 `www.microsoft.com` 都在自定义路径下承载单像素图像 ([trans.gif](https://fpc.msedge.net/apc/trans.gif))。 如果未显式指定对象路径，则 Internet 分析器将默认使用 `/apc/trans.gif` 作为对象路径，这是预配置的终结点承载单像素图像的位置。 另请注意，无需指定架构 (https/http)；Internet 分析器仅支持 HTTPS 终结点，因此假定使用 HTTPS。

11. 新测试应出现在 Internet 分析器配置文件下：
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    示例输出：
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. 若要开始生成测量，必须将测试的“scriptFileUri”指向的 JavaScript 文件嵌入 Web 应用程序中  。 可在[嵌入 Internet 分析器客户端](internet-analyzer-embed-client.md)页上找到具体的说明。

13. 可以通过跟踪测试的“状态”值来监视测试的进度：
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. 可以通过为测试生成时序或记分卡来检查测试收集的结果：
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>后续步骤

* 有关支持的命令和用法示例的完整列表，请浏览 [Internet 分析器 CLI 参考](https://docs.microsoft.com/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest)。
* 阅读 [Internet 分析器常见问题解答](internet-analyzer-faq.md)。
* 了解有关嵌入 [Internet 分析器客户端](internet-analyzer-embed-client.md)和创建[自定义终结点](internet-analyzer-custom-endpoint.md)的详细信息。 
