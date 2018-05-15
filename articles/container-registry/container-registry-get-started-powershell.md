---
title: 快速入门 - 使用 PowerShell 在 Azure 中创建专用 Docker 注册表
description: 快速了解如何使用 PowerShell 在 Azure 中创建专用 Docker 容器注册表。
services: container-registry
author: marsma
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/08/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 282cd4bc9256fc483014b53626c02106d0de236a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>教程：使用 PowerShell 创建 Azure 容器注册表

Azure 容器注册表是托管的专用 Docker 容器注册表服务，用于生成、存储和提供 Docker 容器映像。 本快速入门介绍如何使用 PowerShell 创建 Azure 容器注册表。 创建注册表以后，请向其推送容器映像，然后将容器从注册表部署到 Azure 容器实例 (ACI) 中。

## <a name="prerequisites"></a>先决条件

本快速入门需要 Azure PowerShell 模块 5.7.0 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可确定已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

还必须在本地安装 Docker。 Docker 提供的包适用于 [macOS][docker-mac]、[Windows][docker-windows] 和 [Linux][docker-linux] 系统。

由于 Azure Cloud Shell 并不包括所有必需的 Docker 组件（`dockerd` 守护程序），因此不能将 Cloud Shell 用于本快速入门。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 [Connect-AzureRmAccount][Connect-AzureRmAccount] 命令登录到 Azure 订阅，然后按屏幕说明操作。

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>创建资源组

通过 Azure 进行身份验证以后，请使用 [New-AzureRmResourceGroup][New-AzureRmResourceGroup] 创建资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>创建容器注册表

接下来，请使用 [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry] 命令在新的资源组中创建容器注册表。

注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 以下示例创建名为“myContainerRegistry007”的注册表。 替换以下命令中的 *myContainerRegistry007*，然后运行该命令以创建注册表：

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-registry"></a>登录到注册表

在推送和拉取容器映像之前，必须登录到注册表。 使用 [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential] 命令先获取注册表的管理员凭据：

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

接下来，运行用于登录的 [docker login][docker-login] 命令：

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

登录成功会返回`Login Succeeded`：

```console
PS Azure:\> $creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
Login Succeeded
```

## <a name="push-image-to-registry"></a>将映像推送到注册表

登录到注册表以后，即可向其推送容器映像。 若要获取可以推送到注册表的映像，请从 Docker 中心拉取公共的 [aci-helloworld][aci-helloworld-image] 映像。 [aci-helloworld][aci-helloworld-github] 映像是一个小型 Node.js 应用程序，其提供的静态 HTML 页面显示 Azure 容器实例徽标。

```powershell
docker pull microsoft/aci-helloworld
```

拉取映像时，输出类似于：

```console
PS Azure:\> docker pull microsoft/aci-helloworld
Using default tag: latest
latest: Pulling from microsoft/aci-helloworld
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
3be0618266da: Pull complete
9e232827e52f: Pull complete
b53c152f538f: Pull complete
Digest: sha256:a3b2eb140e6881ca2c4df4d9c97bedda7468a5c17240d7c5d30a32850a2bc573
Status: Downloaded newer image for microsoft/aci-helloworld:latest
```

将映像推送到 Azure 容器注册表之前，必须使用注册表的完全限定域名对其进行标记。 Azure 容器注册表的 FQDN 采用 *\<registry-name\>.azurecr.io* 格式。

在变量中填充完整的映像标记。 包括登录服务器、存储库名称（“aci-helloworld”）和映像版本（“v1”）：

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
```

现在，请使用 [docker tag][docker-tag] 来标记映像：

```powershell
docker tag microsoft/aci-helloworld $image
```

最后，请使用 [docker push][docker-push] 将其推送到注册表：

```powershell
docker push $image
```

Docker 客户端推送映像时，输出应类似于：

```console
PS Azure:\> docker push $image
The push refers to repository [myContainerRegistry007.azurecr.io/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

祝贺你！ 你已将第一个容器映像推送到注册表。

## <a name="deploy-image-to-aci"></a>将映像部署到 ACI

映像到了注册表中以后，请将容器直接部署到 Azure 容器实例，观察其在 Azure 中的运行情况。

首先，将注册表凭据转换为 *PSCredential*。 用于创建容器实例的 `New-AzureRmContainerGroup` 命令要求它采用该格式。

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

另外，容器的 DNS 名称标签在创建时所在的 Azure 区域中必须唯一。 执行以下命令，在变量中填充生成的名称：

```powershell
$dnsname = "aci-demo-" + (Get-Random -Maximum 9999)
```

最后运行 [New-AzureRmContainerGroup][New-AzureRmContainerGroup]，以便部署注册表的映像中的容器，并将核心数设置为“1 CPU”，内存设置为“1 GB”：

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
```

此时会获得 Azure 的初始响应，其中包含容器的详细信息，其状态一开始为“Pending”（挂起）：

```console
PS Azure:\> New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials : {myContainerRegistry007}
RestartPolicy            : Always
IpAddress                : 40.117.255.198
DnsNameLabel             : aci-demo-8751
Fqdn                     : aci-demo-8751.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Linux
Volumes                  :
State                    : Pending
Events                   : {}
```

若要监视其状态并确定其运行时间，可多次运行 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] 命令。 容器启动所需时间应该不到一分钟。

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

在下面可以看到，容器的 ProvisioningState 一开始为“Creating”（正在创建），在启动并运行后转为“Succeeded”（成功）状态：

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Creating
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Succeeded
```

## <a name="view-running-application"></a>查看正在运行的应用程序

成功部署到 ACI 且容器已启动并运行以后，请在浏览器中导航到容器的完全限定域名 (FQDN)，以便查看在 Azure 中运行的应用。

使用 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] 获取容器的 FQDN：


```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
```

命令的输出为容器实例的 FQDN：

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
aci-demo-8571.eastus.azurecontainer.io
```

有了 FQDN 以后，即可在浏览器中导航到该 FQDN：

![浏览器中的 Hello World 应用][qs-psh-01-running-app]

祝贺你！ 你已经有了一个在 Azure 中运行应用程序的容器，该容器是直接从专用 Azure 容器注册表中的容器映像部署的。

## <a name="clean-up-resources"></a>清理资源

使用完在本快速入门中创建的资源以后，请通过 [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] 命令删除资源组、容器注册表和容器实例：

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure CLI 创建 Azure 容器注册表，以及如何在 Azure 容器实例中启动该注册表的一个实例。 若要加深对 ACI 的了解，请继续阅读 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[aci-helloworld-github]: https://github.com/Azure-Samples/aci-helloworld
[aci-helloworld-image]: https://hub.docker.com/r/microsoft/aci-helloworld/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[New-AzureRMContainerRegistry]: /powershell/module/containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-psh-01-running-app]: ./media/container-registry-get-started-powershell/qs-psh-01-running-app.png
