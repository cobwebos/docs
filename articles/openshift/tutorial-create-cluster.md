---
title: 教程 - 创建 Azure Red Hat OpenShift 群集 | Microsoft Docs
description: 了解如何使用 Azure CLI 创建 Microsoft Azure Red Hat OpenShift 群集
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/14/2019
ms.openlocfilehash: 9094fa441ff5ffdd5f579fd072ffaa303961314d
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304276"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>教程：创建 Azure Red Hat OpenShift 群集

本教程是一个系列中的第一部分。 其中将会介绍如何使用 Azure CLI 创建 Microsoft Azure Red Hat OpenShift 群集，对其进行缩放，然后删除该群集以清理资源。

本教程系列的第一部分介绍了如何：

> [!div class="checklist"]
> * 创建 Azure Red Hat OpenShift 群集

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 创建 Azure Red Hat OpenShift 群集
> * [缩放 Azure Red Hat OpenShift 群集](tutorial-scale-cluster.md)
> * [删除 Azure Red Hat OpenShift 群集](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> 本教程需要 Azure CLI 2.0.65 版。
>    
> 需要购买至少 4 个 Azure Red Hat OpenShift 保留应用程序节点（如[设置 Azure Red Hat OpenShift 开发环境](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances)所述），然后才能使用 Azure Red Hat OpenShift。

在开始学习本教程之前：

确保已[设置开发环境](howto-setup-environment.md)，包括：
- 安装最新版的 CLI（2.0.65 或更高版本）
- 如果还没有租户，请创建一个
- 如果还没有 Azure 应用对象，请创建一个
- 添加安全组
- 创建 Active Directory 用户以登录到群集。

## <a name="step-1-sign-in-to-azure"></a>步骤 1：登录 Azure

如果在本地运行 Azure CLI，请打开 Bash 命令 shell，然后运行 `az login` 登录到 Azure。

```bash
az login
```

 如果你有权访问多个订阅，请运行 `az account set -s {subscription ID}`（将 `{subscription ID}` 替换为要使用的订阅）。

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>步骤 2：创建 Azure Red Hat OpenShift 群集

在 Bash 命令窗口中，设置以下变量：

> [!IMPORTANT]
> 为群集选择唯一的名称，且所有小写或群集创建操作都将失败。

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

选择创建群集所在的位置。 有关支持 OpenShift on Azure 的 Azure 区域列表，请参阅[支持的区域](supported-resources.md#azure-regions)。 例如：`LOCATION=eastus`。

```bash
LOCATION=<location>
```

将 `APPID` 设置为在[创建 Azure AD 应用注册](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)的步骤 5 中保存的值。  

```bash
APPID=<app ID value>
```

将“GROUPID”设置为在[创建 Azure AD 安全组](howto-aad-app-configuration.md#create-an-azure-ad-security-group)的步骤 10 中保存的值。

```bash
GROUPID=<group ID value>
```

将 `SECRET` 设置为在[创建客户端密码](howto-aad-app-configuration.md#create-a-client-secret)的步骤 8 中保存的值。  

```bash
SECRET=<secret value>
```

将 `TENANT` 设置为在[创建新租户](howto-create-tenant.md#create-a-new-azure-ad-tenant)的步骤 7 中保存的租户 ID 值。  

```bash
TENANT=<tenant ID>
```

为群集创建资源组。 在用于定义上述变量的同一 Bash shell 中运行以下命令：

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>可选：将群集的虚拟网络连接到现有的虚拟网络

如果不需要通过对等互连将所创建的群集的虚拟网络 (VNET) 连接到现有 VNET，请跳过此步骤。

如果与默认订阅之外的网络对等互连，则在该订阅中，还需要注册提供程序 Microsoft.ContainerService。 为此，请在该订阅中运行以下命令。 否则，如果对等互连的 VNET 位于同一订阅中，则可以跳过注册步骤。 

`az provider register -n Microsoft.ContainerService --wait`

首先获取现有 VNET 的标识符。 该标识符采用以下格式：`/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`。

如果你不知道现有 VNET 所属的网络名称或资源组，请转到[“虚拟网络”边栏选项卡](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks)并单击你的虚拟网络。 此时会出现“虚拟网络”页，其中列出了该 VNET 所属的网络名称和资源组。

在 BASH shell 中使用以下 CLI 命令定义一个 VNET_ID 变量：

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

例如： `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>创建群集

现在已准备好创建群集。 以下命令将在指定的 Azure AD 租户中创建群集、指定要用作安全主体的 Azure AD 应用对象和机密，以及包含对群集具有管理访问权限的成员的安全组。

如果**没有**将群集对等互连到虚拟网络，请使用以下命令：

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

如果**要**将群集对等互连到虚拟网络，请使用添加 `--vnet-peer` 标记的以下命令：
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> 如果有错误指出主机名不可用，原因可能是群集名称不唯一。 尝试删除原始应用注册，并使用不同的群集名称重新执行[创建新应用注册] (howto-aad-app-configuration.md#create-a-new-app-registration) 中的步骤（省略创建新用户和安全组的步骤）。

`az openshift create` 将在几分钟后完成。

### <a name="get-the-sign-in-url-for-your-cluster"></a>获取群集的登录 URL

通过运行以下命令获取用于登录到群集的 URL：

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

在输出中查找 `publicHostName`，例如：`"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

群集的登录 URL 将为 `https://`，后跟 `publicHostName` 值。  例如：`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`。  下一步中将使用此 URI 作为应用注册重定向 URI 的一部分。

## <a name="step-3-update-your-app-registration-redirect-uri"></a>步骤 3：更新应用注册重定向 URI

现在已拥有群集的登录 URL，接下来可设置应用注册重定向 UI：

1. 打开 [应用注册](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) 边栏选项卡。
2. 单击应用注册对象。
3. 单击“添加重定向 URI”  。
4. 确保“类型”为“Web”，并使用以下模式设置“重定向 URI”：`https://<public host name>/oauth2callback/Azure%20AD`    。 例如： `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. 单击“保存” 

## <a name="step-4-sign-in-to-the-openshift-console"></a>步骤 4：登录到 OpenShift 控制台

现可登录到新群集的 OpenShift 控制台。 使用 [OpenShift Web 控制台](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html)可以可视化、浏览和管理 OpenShift 项目的内容。

需要一个全新的浏览器实例，其中尚未缓存平时用来登录 Azure 门户的标识。

1. 打开 *incognito* 窗口 (Chrome) 或 *InPrivate* 窗口 (Microsoft Edge)。
2. 导航到之前获取的登录 URL，例如：`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

使用在[创建新的 Azure Active Directory 用户](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user)的步骤 3 中创建的用户名登录。

此时将显示“请求权限”对话框  。 依次单击“代表组织同意”和“接受”   。

现已登录到群集控制台。

![OpenShift 群集控制台的屏幕截图](./media/aro-console.png)

 在 [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) 文档中详细了解如何[使用 OpenShift 控制台](https://docs.openshift.com/aro/getting_started/developers_console.html)来创建和生成映像。

## <a name="step-5-install-the-openshift-cli"></a>步骤 5：安装 OpenShift CLI

[OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html)（或 OC 工具）提供了用于管理应用程序的命令，以及用于与 OpenShift 群集各组件进行交互的低级别实用工具。 

在 OpenShift 控制台中，单击右上角登录名旁边的问号，然后选择“命令行工具”。   单击“最新版本”链接下载并安装适用于 Linux、MacOS 或 Windows 的受支持 oc CLI。 

> [!NOTE]
> 如果右上角未显示问号图标，请从左上方的下拉列表中选择“服务目录”或“应用程序控制台”。  
>
> 或者，可以直接[下载 oc CLI](https://www.okd.io/download.html)。

“命令行工具”页提供了一个采用 `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>` 格式的命令。   请单击“复制到剪贴板”按钮以复制此命令。   在终端窗口中，将[路径设置](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli)为包含 oc 工具的本地安装。 然后使用复制的 oc CLI 命令登录到群集。

如果使用上述步骤无法获取令牌值，请从 `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request` 获取令牌值。

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 创建 Azure Red Hat OpenShift 群集

转到下一教程：
> [!div class="nextstepaction"]
> [缩放 Azure Red Hat OpenShift 群集](tutorial-scale-cluster.md)
