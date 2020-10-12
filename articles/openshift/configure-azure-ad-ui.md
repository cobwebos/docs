---
title: 运行 OpenShift 4 的 Azure Red Hat OpenShift-使用 Azure 门户和 OpenShift web 控制台配置 Azure Active Directory authentication
description: 了解如何使用 Azure 门户和 OpenShift web 控制台为运行 OpenShift 4 的 Azure Red Hat OpenShift 群集配置 Azure Active Directory 身份验证
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro、openshift、az aro、red hat、cli
ms.custom: mvc
ms.openlocfilehash: 2cb54c202af04996080cda970b3d327145f0e72b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89469875"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>为 Azure Red Hat OpenShift 4 群集 (门户配置 Azure Active Directory 身份验证) 

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="before-you-begin"></a>开始之前

构造群集的 **OAuth 回调 URL** 并记下它。 请确保将 **aro-rg** 替换为资源组的名称，并将 **aro 群集** 名称替换为群集的名称。

> [!NOTE]
> `AAD`Oauth 回调 URL 中的部分应与稍后要设置的 oauth 标识提供者名称相匹配。

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>创建用于身份验证的 Azure Active Directory 应用程序

登录到 Azure 门户，导航到 " [应用注册" 边栏选项卡](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)，然后单击 " **新建注册** " 以创建新的应用程序。

提供应用程序的名称（例如， **azuread**），并使用之前检索到的 OAUTH 回调 URL 的值填充 **重定向 URI** 。

![新应用程序注册](media/aro4-ad-registerapp.png)

导航到 " **证书" & "机密** "，单击 " **新建客户端密钥** "，并填写详细信息。 记下密钥值，因为你将在后面的阶段中使用它。 你将无法再次检索它。

![创建机密](media/aro4-ad-clientsecret.png)

导航到 " **概述** " 并记下 **应用程序 (客户端) id** 和 **目录 (租户) ID**。 稍后需要用到它们。

![检索应用程序 (客户端) 和目录 (租户) Id](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>配置可选声明

应用程序开发人员可以在其 Azure AD 的应用程序中使用 [可选的声明](../active-directory/develop/active-directory-optional-claims.md) ，以指定要发送到其应用程序的令牌中的声明。

使用可选声明可以：

* 选择要包含在应用程序令牌中的附加声明。
* 更改 Azure AD 在令牌中返回的某些声明的行为。
* 添加和访问应用程序的自定义声明。

我们会将 OpenShift 配置为使用 `email` 声明，并回退到 `upn` 以设置首选用户名，方法是将添加 `upn` 为 AZURE ACTIVE DIRECTORY 返回的 ID 令牌的一部分。

导航到 " **令牌配置 (预览") ** 并单击 " **添加可选声明**"。 选择 " **ID** "，然后检查 **电子邮件** 和 **upn** 声明。

![创建机密](media/aro4-ad-tokens.png)

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>将用户和组分配给群集 (可选) 

默认情况下，在 Azure Active Directory (Azure AD) 租户中注册的应用程序可供租户的所有已成功进行身份验证的用户使用。 租户管理员和开发人员可以通过 Azure AD 将应用限制为仅供租户中特定的一组用户或安全组使用。

按照 Azure Active Directory 文档上的说明向 [应用程序分配用户和组](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration)。

## <a name="configure-openshift-openid-authentication"></a>配置 OpenShift OpenID 身份验证

检索 `kubeadmin` 凭据。 请运行以下命令，找到 `kubeadmin` 用户的密码。

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

以下示例输出表明密码将位于 `kubeadminPassword` 中。

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

可以通过运行以下命令查找群集控制台 URL，它类似于 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`。

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

在浏览器中启动控制台 URL，使用 `kubeadmin` 凭据登录。

导航到 " **管理**"，单击 " **群集设置**"，然后选择 " **全局配置** " 选项卡。滚动以选择 " **OAuth**"。

向下滚动到 "**标识提供者**" 下的 "**添加**"，然后选择 " **OpenID connect**"
![从标识提供程序下拉列表中选择 OpenID Connect](media/aro4-oauth-idpdrop.png)

将名称填充为 **AAD**，将 **客户端 Id** 作为 **应用程序 id** 和 **客户端密钥**。 **颁发者 URL**的格式如下： `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 。 将占位符替换为前面检索到的租户 ID。

![填写 OAuth 详细信息](media/aro4-oauth-idp-1.png)

向下滚动到 " **声明** " 部分，并更新 **首选用户名** 以使用 **upn** 声明中的值。

![填写声明详细信息](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>验证登录名 Azure Active Directory

如果你现在注销了 OpenShift Web 控制台并再次尝试登录，则会看到一个新选项，用于使用 **AAD**登录。 可能需要等待几分钟时间。

![Azure Active Directory 选项的登录屏幕](media/aro4-login-2.png)