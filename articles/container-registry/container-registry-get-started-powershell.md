---
title: 快速入门 - 使用 PowerShell 在 Azure 中创建专用 Docker 注册表
description: 快速了解如何使用 PowerShell 创建专用 Docker 容器注册表。
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ae21fc7ab016071d075324bf813243cef58dcd04
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>教程：使用 PowerShell 创建 Azure 容器注册表

Azure 容器注册表是托管的 Docker 容器注册表服务，用于存储专用的 Docker 容器映像。 本指南详述了如何通过 PowerShell 创建一个 Azure 容器注册表实例，如何将容器映像推送到注册表中，以及如何最终将容器从注册表部署到 Azure 容器实例 (ACI) 中。

本快速入门需要 Azure PowerShell 模块 3.6 版或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

还必须在本地安装 Docker。 Docker 提供的包可在任何 [Mac][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系统上轻松配置 Docker。

## <a name="log-in-to-azure"></a>登录 Azure

使用 `Connect-AzureRmAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>创建容器注册表

使用 [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry) 命令创建 ACR 实例。

注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 以下示例使用 myContainerRegistry007。 将其更新为唯一值。

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>登录到 ACR

在推送和拉取容器映像之前，必须登录到 ACR 实例。 首先，使用 [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) 命令来获取 ACR 实例的管理员凭据。

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

接下来，使用 [docker login][docker-login] 命令登录到 ACR 实例。

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

该命令在完成后返回 `Login Succeeded`。 可能会看见建议使用 `--password-stdin` 参数的安全警告。 虽然本文中未介绍它的用法，但我们建议按照此最佳做法进行操作。 有关详细信息，请参阅 [docker login][docker-login] 命令参考。

## <a name="push-image-to-acr"></a>将映像推送到 ACR

要将映像推送到 Azure 容器注册表，首先必须具有一个映像。 如有必要，请运行以下命令，从 Docker 中心拉取预先创建的映像。

```powershell
docker pull microsoft/aci-helloworld
```

必须使用 ACR 登录服务器名称标记此映像。 为此，请使用 [docker tag][docker-tag] 命令。

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

最后，使用 [docker push][docker-push] 将映像推送到 ACR。

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>将映像部署到 ACI
若要在 Azure 容器实例 (ACI) 中将映像部署为容器实例，请首先将注册表凭据转换为 PSCredential。

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

若要从容器注册表部署包含 1 个 CPU 核心和 1 GB 内存的容器映像，请运行以下命令：

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

此时会返回 Azure 资源管理器的初始响应，其中包含容器的详细信息。 若要监视容器的状态并查看其何时运行，请重复 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] 命令。 应该只需要不到一分钟的时间。

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

示例输出：`Succeeded`

## <a name="view-the-application"></a>查看应用程序
成功部署到 ACI 以后，请使用 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] 命令检索容器的公用 IP 地址：

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

示例输出：`"13.72.74.222"`

若要查看正在运行的应用程序，请从喜欢的浏览器中导航到此公共 IP 地址。 应该会看到类似下面的屏幕：

![浏览器中的 Hello World 应用][qs-portal-15]

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、Azure 容器注册表和所有 Azure 容器实例，可以使用 [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] 命令将其删除。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure CLI 创建 Azure 容器注册表，以及如何在 Azure 容器实例中启动该注册表的一个实例。 若要加深对 ACI 的了解，请继续阅读 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
