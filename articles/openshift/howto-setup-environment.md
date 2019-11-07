---
title: 设置 Azure Red Hat OpenShift 开发环境 |Microsoft Docs
description: 下面是使用 Microsoft Azure Red Hat OpenShift 的先决条件。
services: openshift
keywords: red hat openshift 安装程序设置
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
manager: jeconnoc
ms.openlocfilehash: fa09ed90258a62d37dafeea5f4760e1fabdc210b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581600"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>设置 Azure Red Hat OpenShift 开发环境

若要生成并运行 Microsoft Azure Red Hat OpenShift 应用程序，需要：

* 安装 Azure CLI 的版本2.0.65 （或更高版本）（或使用 Azure Cloud Shell）。
* 注册 `AROGA` 功能和关联的资源提供程序。
* 创建 Azure Active Directory （Azure AD）租户。
* 创建 Azure AD 应用程序对象。
* 创建 Azure AD 用户。

以下说明将指导你完成所有这些先决条件。

## <a name="install-the-azure-cli"></a>安装 Azure CLI

Azure Red Hat OpenShift 要求 Azure CLI 2.0.65 或更高版本。 如果你已经安装了 Azure CLI，则可以通过运行来检查你的版本：

```bash
az --version
```

输出的第一行将包含 CLI 版本，例如 `azure-cli (2.0.65)`。

如果需要全新安装或升级，请参阅以下[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)说明。

或者，您可以使用[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。 使用 Azure Cloud Shell 时，请确保选择**Bash**环境（如果打算按照[创建和管理 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程系列中的说明进行操作）。

## <a name="register-providers-and-features"></a>注册提供程序和功能

在部署第一个 Azure Red Hat OpenShift 群集之前，必须向你的订阅手动注册 `Microsoft.ContainerService AROGA` 功能、`Microsoft.Solutions`、`Microsoft.Compute`、`Microsoft.Storage`、`Microsoft.KeyVault` 和 `Microsoft.Network` 提供程序。

若要手动注册这些提供程序和功能，请使用 Bash shell 中的以下说明（如果已安装 CLI），或从 Azure 门户中的 Azure Cloud Shell （Bash）会话使用：

1. 如果有多个 Azure 订阅，请指定相关订阅 ID：

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. 注册 ContainerService AROGA 功能：

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. 注册 Microsoft. 存储提供程序：

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. 注册 Microsoft. 计算提供程序：

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. 注册 Microsoft 解决方案提供商：

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. 注册 Microsoft 网络提供程序：

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. 注册 KeyVault 提供程序：

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. 刷新 ContainerService 资源提供程序的注册：

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>创建 Azure Active Directory （Azure AD）租户

Azure Red Hat OpenShift 服务需要一个关联的 Azure Active Directory （Azure AD）租户，它代表你的组织及其与 Microsoft 之间的关系。 你的 Azure AD 租户使你能够注册、构建和管理应用，并使用其他 Azure 服务。

如果没有 Azure AD 用作 Azure Red Hat OpenShift 群集的租户，或者想要创建用于测试的租户，请按照为[Azure Red Hat OpenShift 群集创建 Azure AD 租户](howto-create-tenant.md)中的说明，然后再继续本指南.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>创建 Azure AD 用户、安全组和应用程序对象

Azure Red Hat OpenShift 需要权限才能在群集上执行任务，如配置存储。 这些权限通过[服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)来表示。 还需要创建新的 Active Directory 用户，以便测试在 Azure Red Hat OpenShift 群集上运行的应用。

按照[创建 Azure AD 应用对象和用户](howto-aad-app-configuration.md)中的说明，创建服务主体，为你的应用生成客户端密钥和身份验证回叫 URL，并创建新的 Azure AD 安全组和用户来访问该群集。

## <a name="next-steps"></a>后续步骤

你现在已准备好使用 Azure Red Hat OpenShift！

尝试教程：
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
