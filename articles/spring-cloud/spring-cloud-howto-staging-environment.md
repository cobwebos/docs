---
title: 在 Azure Spring Cloud 中设置过渡环境 | Microsoft Docs
description: 了解如何在 Azure Spring Cloud 中使用蓝绿部署
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 454eeaa2568891ec35fe698cdb20c5448e10887e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038296"
---
# <a name="how-to-set-up-a-staging-environment"></a>如何设置过渡环境

本文介绍如何通过 Azure Spring Cloud 中的蓝绿部署模式来利用过渡部署。 其中还会介绍如何在不直接更改生产部署的情况下，将此过渡部署投入生产。

## <a name="prerequisites"></a>先决条件

本文假设已根据[有关启动应用程序的教程](spring-cloud-quickstart-launch-app-portal.md)部署了 PiggyMetrics 应用程序。 PiggyMetrics 包括三个应用程序：“gateway”、“account-service”和“auth-service”。  

如果要对本示例使用其他应用程序，需要在该应用程序的面向公众的部分进行一项简单的更改。  此项更改会将过渡部署与生产部署区分开来。

>[!NOTE]
> 在开始本快速入门之前，请确保你的 Azure 订阅有权访问 Azure Spring Cloud。  由于此服务为预览版服务，我们要求用户先与我们联系，然后我们才能将其订阅添加到允许列表中。  若要探索 Azure Spring Cloud 的功能，请通过电子邮件 (azure-spring-cloud@service.microsoft.com) 联系我们。

>[!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。  它预装有常用的 Azure 工具，其中包括最新版的 Git、JDK、Maven 和 Azure CLI。 如果你已登录到 Azure 订阅，请从 shell.azure.com 启动 [Azure Cloud Shell](https://shell.azure.com)。  若要详细了解 Azure Cloud Shell，请[阅读我们的文档](../cloud-shell/overview.md)

若要完成本文，需要做好以下准备：

1. [安装 Git](https://git-scm.com/)
1. [安装 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
1. [安装 Maven 3.0 或更高版本](https://maven.apache.org/download.cgi)
1. [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
1. [注册 Azure 订阅](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

使用以下命令安装用于 Azure CLI 的 Azure Spring Cloud 扩展

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```
    
## <a name="view-all-deployments"></a>查看所有部署

在 Azure 门户中转到你的服务实例，然后选择“部署管理”查看所有部署。  可以选择每个部署来查看更多详细信息。

## <a name="create-a-staging-deployment"></a>创建分阶段部署

1. 在本地开发环境中，对 Piggy Metric 的网关应用程序进行少许修改。 例如，更改 `gateway/src/main/resources/static/css/launch.css` 中的颜色。 这样，就可以轻松区分这两个部署。 运行以下命令生成 jar 包： 

    ```azurecli
    mvn clean package
    ```

1. 使用 Azure CLI 创建新的部署，并指定过渡部署名称“green”。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 部署成功完成后，从“应用程序仪表板”访问网关页，然后在左侧的“应用实例”选项卡中查看所有实例。  
  
> [!NOTE]
> 发现状态为“OUT_OF_SERVICE”，因此，在验证完成之前，流量不会路由到此部署。

## <a name="verify-the-staging-deployment"></a>验证过渡部署

1. 返回“部署管理”页并选择你的新部署。  部署状态应显示为“正在运行”。  由于它是过渡环境，因此将禁用“分配/取消分配域”按钮。

1. 在“概述”页中，应会看到“测试终结点”。   将其复制并粘贴到新的浏览器页；应会看到新的 Piggy Metrics 页。

>[!TIP]
> * 确认测试终结点以“/”结尾，以确保正确加载 CSS。  
> * 如果浏览器要求在查看该页之前输入登录凭据，请使用 [URL decode](https://www.urldecoder.org/) 来解码测试终结点。 URL decode 将返回“https://\<用户名>:\<密码>@\<群集名称>.test.azureapps.io/gateway/green”格式的 URL。  请使用此 URL 来访问终结点。

>[!NOTE]    
> 配置服务器设置将应用到过渡环境和生产环境。 例如，如果在配置服务器中将应用程序网关的上下文路径 (`server.servlet.context-path`) 设置为 *somepath*，则 green 部署的路径将会更改：“https://\<用户名>:\<密码>@\<群集名称>.test.azureapps.io/gateway/green/somepath/...”
 
 如果此时访问面向公众的应用程序网关，应会看到旧页面，其中不包含新的更改。
    
## <a name="set-the-green-as-production-deployment"></a>将 green 设置为生产部署

1. 在过渡环境中验证更改后，可将其推送到生产环境。 返回“部署管理”，选中“网关”应用程序前面的复选框。 
2. 选择“设置部署”。
3. 从“生产部署”菜单中选择“Green”，然后选择“应用” 
4. 转到网关应用程序的“概述”页。  如果已经为网关应用程序分配了一个域，URL 将显示在“概述”页上。  访问该 URL，将会看到修改后的 Piggy Metrics 页。

>[!NOTE]
> 将 green 部署设置为生产环境后，以前的部署将变成过渡部署。

## <a name="modify-the-staging-deployment"></a>修改过渡部署

如果你对更改不满意，可以修改应用程序代码，生成新的 jar 包，然后使用 Azure CLI 将其上传到 green 部署。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>删除过渡部署

在 Azure 门户中导航到过渡部署页，然后选择“删除”按钮来删除过渡部署。 

或者，在 Azure CLI 中使用以下命令删除过渡部署：

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
