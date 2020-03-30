---
title: 在 Azure Spring Cloud 中设置过渡环境 | Microsoft Docs
description: 了解如何在 Azure Spring Cloud 中使用蓝绿部署
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 2e29f6a75b303518ac34ecf9b570bd7638cf0c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471024"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>在 Azure 春云中设置暂存环境

本文讨论如何使用 Azure Spring Cloud 中的蓝绿色部署模式设置暂存部署。 蓝/绿部署是一种 Azure DevOps 持续交付模式，它依赖于在部署新（绿色）版本时保持现有（蓝色）版本的活动性。 本文介绍如何在不直接更改生产部署的情况下将暂存部署投入生产。

## <a name="prerequisites"></a>先决条件

本文假定您已经部署了有关[启动 Azure 春云应用程序的教程](spring-cloud-quickstart-launch-app-portal.md)中的 PiggyMetrics 应用程序。 PiggyMetrics 包括三个应用程序："网关"、"帐户服务"和"服务"。  

如果要对此示例使用不同的应用程序，则需要在应用程序的面向公共的部分进行简单的更改。  此项更改会将过渡部署与生产部署区分开来。

>[!TIP]
> Azure 云外壳是一个免费的交互式外壳，可用于运行本文中的说明。  它有常见的预安装 Azure 工具，包括最新版本的 Git、JDK、Maven 和 Azure CLI。 如果已登录到 Azure 订阅，请启动 Azure[云外壳](https://shell.azure.com)。  要了解更多信息，请参阅[Azure 云外壳概述](../cloud-shell/overview.md)。

要在 Azure Spring Cloud 中设置暂存环境，请按照以下各节中的说明操作。

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

使用以下命令安装用于 Azure CLI 的 Azure Spring Cloud 扩展：

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>查看所有部署

转到 Azure 门户中的服务实例，然后选择 **"部署管理"** 以查看所有部署。 要查看更多详细信息，可以选择每个部署。

## <a name="create-a-staging-deployment"></a>创建分阶段部署

1. 在本地开发环境中，对 PiggyMetrics 网关应用程序进行小修改。 例如，更改*网关/src/主/资源/静态/css/启动文件*中的颜色。 这样，您就可以轻松区分两个部署。 要生成 jar 包，运行以下命令： 

    ```console
    mvn clean package
    ```

1. 在 Azure CLI 中，创建新部署，并将其暂存部署名称命名为"绿色"。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 成功完成部署后，从**应用程序仪表板**访问网关页，并在左侧的 **"应用实例"** 选项卡中查看所有实例。
  
> [!NOTE]
> 发现状态*OUT_OF_SERVICE*以便在验证完成之前不会将流量路由到此部署。

## <a name="verify-the-staging-deployment"></a>验证过渡部署

1. 返回到 **"部署管理**"页，然后选择新部署。 部署状态应显示为“正在运行”。** "**分配/取消分配域"** 按钮应显示为灰色，因为环境是暂存环境。

1. 在 **"概述"** 窗格中，应看到**测试终结点**。 复制并粘贴到新的浏览器窗口中，并应显示新的 PiggyMetrics 页面。

>[!TIP]
> * 确认测试终结点以斜杠 （/） 结尾，以确保 CSS 文件加载正确。  
> * 如果浏览器要求在查看该页之前输入登录凭据，请使用 [URL decode](https://www.urldecoder.org/) 来解码测试终结点。 URL decode 将返回“https://\<用户名>:\<密码>@\<群集名称>.test.azureapps.io/gateway/green”格式的 URL。  使用此窗体可以访问终结点。

>[!NOTE]    
> 配置服务器设置适用于过渡环境和生产。 例如，如果将配置服务器中的应用网关的上下文路径`server.servlet.context-path`（） 设置为*某个路径*，则绿色部署的路径将更改为"https://\<用户名>：\<密码>=\<群集名称>.test.azureapps.io/网关/绿色/某些路径/..."。
 
 如果此时访问面向公共的应用网关，您应该会看到旧页面，而不会进行新的更改。
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>将绿色部署设置为生产环境

1. 验证暂存环境中的更改后，可以将其推送到生产环境。 返回到**部署管理**，然后选择**网关**应用程序复选框。

2. 选择 **"设置部署**"。
3. 在 **"生产部署"** 列表中，选择 **"绿色**"，然后选择 **"应用**"。
4. 转到网关应用程序的“概述”页。**** 如果您已经为网关应用程序分配了域，则 URL 将显示在 **"概述"** 窗格中。 要查看修改后的 PiggyMetrics 页面，请选择 URL 并转到网站。

>[!NOTE]
> 将绿色部署设置为生产环境后，上一个部署将成为临时部署。

## <a name="modify-the-staging-deployment"></a>修改过渡部署

如果您对更改不满意，则可以修改应用程序代码、构建新的 jar 包，并使用 Azure CLI 将其上载到绿色部署。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>删除暂存部署

要从 Azure 端口中删除暂存部署，请转到暂存部署页，然后选择"**删除**"按钮。

或者，通过运行以下命令从 Azure CLI 中删除临时部署：

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
