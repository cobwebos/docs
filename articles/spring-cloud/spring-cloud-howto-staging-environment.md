---
title: 在 Azure Spring Cloud 中设置过渡环境 | Microsoft Docs
description: 了解如何在 Azure Spring Cloud 中使用蓝绿部署
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 4adeb5593f86bdf3f8a4ea5f844c31a8314e0f15
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276917"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>在 Azure 春季云中设置过渡环境

本文介绍如何使用 Azure 春季云中的蓝绿色部署模式设置过渡部署。 它还说明了如何将此过渡部署置于生产环境中，而无需直接更改生产部署。

## <a name="prerequisites"></a>必备组件

本文假设你已部署了[有关启动 Azure 春季云应用程序的教程](spring-cloud-quickstart-launch-app-portal.md)中的 PiggyMetrics 应用程序。 PiggyMetrics 由三个应用程序组成： "网关"、"帐户-服务" 和 "身份验证服务"。  

如果要在本示例中使用其他应用程序，则需要对应用程序的面向公众的部分进行简单的更改。  此项更改会将过渡部署与生产部署区分开来。

>[!TIP]
> Azure Cloud Shell 是一种免费的交互式 Shell，可以使用它运行本文中的说明。  它具有常见的预安装的 Azure 工具，包括最新版本的 Git、JDK、Maven 和 Azure CLI。 如果你已登录到 Azure 订阅，请开始[Azure Cloud Shell](https://shell.azure.com)。  若要了解详细信息，请参阅[Azure Cloud Shell 的概述](../cloud-shell/overview.md)。

若要在 Azure 春季云中设置过渡环境，请按照后续部分中的说明进行操作。

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

使用以下命令安装用于 Azure CLI 的 Azure Spring Cloud 扩展：

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>查看所有部署

在 Azure 门户中转到服务实例，并选择 "**部署管理**" 以查看所有部署。 若要查看更多详细信息，可以选择每个部署。

## <a name="create-a-staging-deployment"></a>创建分阶段部署

1. 在本地开发环境中，对 PiggyMetrics 网关应用程序进行少许修改。 例如，在*网关/src/main/resources/static/css/启动 .css*文件中更改颜色。 这样做可以轻松区分这两个部署。 若要生成 jar 包，请运行以下命令： 

    ```azurecli
    mvn clean package
    ```

1. 在 Azure CLI 中，创建一个新部署，并为其分配过渡部署名称 "绿色"。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 部署成功完成后，从**应用程序仪表板**访问 "网关" 页，并在左侧的 "**应用程序实例**" 选项卡中查看所有实例。
  
> [!NOTE]
> 将*OUT_OF_SERVICE*发现状态，以便在验证完成之前不会将流量路由到此部署。

## <a name="verify-the-staging-deployment"></a>验证过渡部署

1. 返回到 "**部署" 管理**页，并选择你的新部署。 部署状态应显示为“正在运行”。 由于环境是过渡环境，"**分配/取消分配域**" 按钮应显示为灰色。

1. 在**概述**窗格中，应会看到**测试终结点**。 将其复制并粘贴到新的浏览器窗口，并显示 "新建 PiggyMetrics" 页。

>[!TIP]
> * 确认测试终结点以斜杠（/）结尾，以确保正确加载 CSS 文件。  
> * 如果浏览器要求在查看该页之前输入登录凭据，请使用 [URL decode](https://www.urldecoder.org/) 来解码测试终结点。 URL decode 将返回“https://\<用户名>:\<密码>@\<群集名称>.test.azureapps.io/gateway/green”格式的 URL。  使用此窗体可访问终结点。

>[!NOTE]    
> 配置服务器设置同时适用于过渡环境和生产环境。 例如，如果在配置服务器中将应用程序网关的上下文路径（`server.servlet.context-path`）设置为*somepath*，则绿色部署的路径将更改为 "https://\<username >：\<password > @\<azureapps/gateway//somepath/.."。
 
 如果此时访问面向公众的应用网关，则应该会看到旧页面而不进行新的更改。
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>将绿色部署设置为生产环境

1. 在过渡环境中验证更改后，可以将其推送到生产环境。 返回到 "**部署管理**"，然后选择 "**网关**应用程序" 复选框。

2. 选择 "**设置部署**"。
3. 在 "**生产部署**" 列表中，选择 "**绿色**"，然后选择 "**应用**"。
4. 转到网关应用程序的“概述”页。 如果已为网关应用程序分配了域，则 URL 将显示在 "**概述**" 窗格中。 若要查看修改后的 PiggyMetrics 页，请选择该 URL，然后前往站点。

>[!NOTE]
> 将绿色部署设置为生产环境后，以前的部署将成为过渡部署。

## <a name="modify-the-staging-deployment"></a>修改过渡部署

如果你对更改不满意，则可以修改应用程序代码，生成新的 jar 包，并使用 Azure CLI 将其上传到绿色部署。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>删除过渡部署

若要从 Azure 端口删除过渡部署，请前往过渡部署页，然后选择 "**删除**" 按钮。

或者，通过运行以下命令从 Azure CLI 删除过渡部署：

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
