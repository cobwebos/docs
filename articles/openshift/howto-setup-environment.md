---
title: 设置 Azure 红帽开放移位开发环境
description: 以下是使用 Microsoft Azure 红帽 OpenShift 的先决条件。
keywords: 红帽开档设置设置
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477028"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>设置 Azure Red Hat OpenShift 开发环境

要构建和运行 Microsoft Azure 红帽 OpenShift 应用程序，您需要：

* 安装 Azure CLI 的版本 2.0.65（或更高版本）（或使用 Azure 云外壳）。
* 注册功能`AROGA`和相关资源提供程序。
* 创建 Azure 活动目录 （Azure AD） 租户。
* 创建 Azure AD 应用程序对象。
* 创建 Azure AD 用户。

以下说明将引导您完成所有这些先决条件。

## <a name="install-the-azure-cli"></a>安装 Azure CLI

Azure 红帽 OpenShift 需要 Azure CLI 的版本 2.0.65 或更高版本。 如果已经安装了 Azure CLI，则可以通过运行以下版本来检查具有的版本：

```azurecli
az --version
```

第一行输出将具有 CLI 版本，例如`azure-cli (2.0.65)`。

以下是安装 Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)的说明，如果需要新安装或升级。

或者，可以使用[Azure 云外壳](https://docs.microsoft.com/azure/cloud-shell/overview)。 使用 Azure 云外壳时，如果计划遵循["创建和管理 Azure 红帽 OpenShift"群集](tutorial-create-cluster.md)教程系列，请确保选择**Bash**环境。

## <a name="register-providers-and-features"></a>注册提供程序和功能

在`Microsoft.ContainerService AROGA`部署第`Microsoft.Solutions`一`Microsoft.Compute`个`Microsoft.Storage` `Microsoft.KeyVault` Azure 红帽 OpenShift 群集之前，必须手动将功能 、、、和`Microsoft.Network`提供程序注册到订阅中。

要手动注册这些提供程序和功能，请在安装了 CLI 或 Azure 门户中的 Azure 云外壳 （Bash） 会话时使用 Bash shell 中的以下说明：

1. 如果您有多个 Azure 订阅，请指定相关的订阅 ID：

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. 注册 Microsoft.集装箱服务 AROGA 功能：

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. 注册 Microsoft.存储提供程序：

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. 注册 Microsoft.计算提供程序：

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. 注册 Microsoft.解决方案提供商：

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. 注册 Microsoft.网络提供商：

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. 注册 Microsoft.KeyVault 提供程序：

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. 刷新 Microsoft.Container 服务资源提供程序的注册：

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>创建 Azure 活动目录 （Azure AD） 租户

Azure 红帽 OpenShift 服务需要一个关联的 Azure 活动目录 （Azure AD） 租户，该租户表示您的组织及其与 Microsoft 的关系。 Azure AD 租户使您能够注册、生成和管理应用，以及使用其他 Azure 服务。

如果没有 Azure AD 用作 Azure 红帽 OpenShift 群集的租户，或者希望创建租户进行测试，请按照[Azure 红帽 OpenShift 群集创建 Azure AD 租户](howto-create-tenant.md)中的说明操作，然后再继续本指南。

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>创建 Azure AD 用户、安全组和应用程序对象

Azure 红帽 OpenShift 需要权限才能在群集上执行任务，例如配置存储。 这些权限通过[服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)表示。 您还需要创建新的活动目录用户，用于测试在 Azure 红帽 OpenShift 群集上运行的应用。

按照创建 Azure [AD 应用对象和用户](howto-aad-app-configuration.md)中的说明创建服务主体，为应用生成客户端机密和身份验证回调 URL，并创建新的 Azure AD 安全组和用户以访问群集。

## <a name="next-steps"></a>后续步骤

现在，您已准备好使用 Azure 红帽 OpenShift！

请尝试本教程：
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
