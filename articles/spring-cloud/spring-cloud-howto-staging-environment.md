---
title: 在 Azure 春季云中设置过渡环境 |Microsoft Docs
description: 了解如何在 Azure 春季云中使用蓝绿色部署
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 24ce4dee04e4daf3eaee4144f8dc56de5867bbca
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607212"
---
# <a name="how-to-set-up-a-staging-environment"></a>如何设置过渡环境

本文介绍如何使用 Azure 春季云中的蓝绿色部署模式来利用过渡部署。 它还将演示如何将此过渡部署置于生产环境中，而无需直接更改生产部署。

## <a name="prerequisites"></a>先决条件

本文假设已在[启动应用程序的教程](spring-cloud-quickstart-launch-app-portal.md)中部署了 PiggyMetrics 应用程序。 PiggyMetrics 包含三个应用程序： "网关"、"帐户服务" 和 "身份验证服务"。  

如果要在此示例中使用其他应用程序，则需要在应用程序的面向公众的部分进行简单的更改。  此更改将过渡部署与生产区别开来。

>[!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。  它预安装了常见的 Azure 工具，包括最新版本的 Git、JDK、Maven 和 Azure CLI。 如果已登录到 Azure 订阅，请从 shell.azure.com 启动[Azure Cloud Shell](https://shell.azure.com) 。  若要了解有关 Azure Cloud Shell 的详细信息，请[阅读我们的文档](../cloud-shell/overview.md)

若要完成本文：


## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

使用以下命令为 Azure CLI 安装 Azure 春季云扩展

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>查看所有部署

在 Azure 门户中转到服务实例，然后选择 "**部署管理**" 以查看所有部署。 可以选择每个部署以了解更多详细信息。

## <a name="create-a-staging-deployment"></a>创建分阶段部署

1. 在本地开发环境中，对非法携带指标的网关应用程序进行少许修改。 例如，在 `gateway/src/main/resources/static/css/launch.css`中更改颜色。 这将允许你轻松区分这两个部署。 运行以下命令以生成 jar 包： 

    ```azurecli
    mvn clean package
    ```

1. 使用 Azure CLI 创建新的部署，并为其提供过渡部署名称 "绿色"。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 部署成功完成后，从**应用程序仪表板**访问 "网关" 页，并在左侧的 "**应用实例**" 选项卡中查看所有实例。
  
> [!NOTE]
> 发现状态为 "OUT_OF_SERVICE"，以便在验证完成之前，不会将流量路由到此部署。

## <a name="verify-the-staging-deployment"></a>验证过渡部署

1. 返回到 "**部署" 管理**页并选择你的新部署。 部署状态应显示 "**正在运行**"。 由于它是过渡环境，因此将禁用 "分配/取消分配域" 按钮。

1. 在 "**概述**" 页中，应会看到**测试终结点**。 将其复制并粘贴到新的浏览器页面，你应该会看到新的 "非法携带度量值" 页。

>[!TIP]
> * 确认测试终结点以 "/" 结尾，以确保正确加载 CSS。  
> * 如果你的浏览器要求你输入登录凭据以查看页面，请使用[URL 解码](https://www.urldecoder.org/)对测试终结点进行解码。 URL 解码返回格式为 "https://\<用户名 >：\<password > @\<azureapps/网关/绿色" 的 URL。  使用此访问终结点。

>[!NOTE]    
> 配置服务器设置适用于过渡环境和生产环境。 例如，如果在配置服务器中将应用程序网关的上下文路径（`server.servlet.context-path`）设置为*somepath*，则绿色部署的路径将更改： "https://\<用户名 >：\<密码 > @\<>。test.azureapps.io/gateway/green/somepath/... "
 
 如果此时访问面向公众的应用网关，应会看到旧页面而不进行新的更改。
    
## <a name="set-the-green-as-production-deployment"></a>将绿色设置为生产部署

1. 在过渡环境中验证更改后，可以将其推送到生产环境。 返回到 "**部署管理**"，然后选择 "网关" 应用程序之前的复选框。
2. 选择 "设置部署"。
3. 从 "生产部署" 菜单中选择 "绿色"，并选择 "**应用**"
4. 请参阅网关应用程序**概述**页。 如果已为网关应用程序分配了域，则 URL 将显示在 "**概述**" 页上。 访问 URL，你将看到修改后的非法携带指标页。

>[!NOTE]
> 将绿色部署设置为生产环境后，以前的部署将成为过渡部署。

## <a name="modify-the-staging-deployment"></a>修改过渡部署

如果你对更改不满意，则可以修改应用程序代码，生成新的 jar 包，并使用 Azure CLI 将其上传到绿色部署。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>删除过渡部署

通过导航到过渡部署页面并选择 "**删除**" 按钮，从 Azure 端口删除过渡部署。

或者，通过以下命令从 Azure CLI 删除过渡部署：

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
