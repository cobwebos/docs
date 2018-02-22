---
title: "快速入门 - 使用 PowerShell 在 Azure 中创建专用 Docker 注册表"
description: "快速了解如何使用 PowerShell 创建专用 Docker 容器注册表。"
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 80b5055dee35cd6efe62ee949c05aef386a3ba14
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2018
---
# <a name="create-an-azure-container-registry-using-powershell"></a>使用 PowerShell 创建 Azure 容器注册表

Azure 容器注册表是托管的 Docker 容器注册表服务，用于存储专用的 Docker 容器映像。 本指南详述了如何使用 PowerShell 创建 Azure 容器注册表实例。

本快速入门需要 Azure PowerShell 模块 3.6 版或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

还必须在本地安装 Docker。 Docker 提供的包可在任何 [Mac][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系统上轻松配置 Docker。

## <a name="log-in-to-azure"></a>登录 Azure

使用 `Login-AzureRmAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Login-AzureRmAccount
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

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

该命令在完成后返回 `Login Succeeded`。 可能会看见建议使用 `--password-stdin` 参数的安全警告。 虽然本文中未介绍它的用法，但我们建议按照此最佳做法进行操作。 有关详细信息，请参阅 [docker login][docker-login] 命令参考。

## <a name="push-image-to-acr"></a>将映像推送到 ACR

要将映像推送到 Azure 容器注册表，首先必须具有一个映像。 如有必要，请运行以下命令，从 Docker 中心拉取预先创建的映像。

```bash
docker pull microsoft/aci-helloworld
```

必须使用 ACR 登录服务器名称标记此映像。 运行 [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) 命令来返回 ACR 实例的登录服务器名称。

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

使用 [docker tag][docker-tag] 命令标记映像。 将 *acrLoginServer* 替换为你的 ACR 实例的登录服务器名称。

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

最后，使用 [docker push][docker-push] 将映像推送到 ACR 实例。 将 *acrLoginServer* 替换为你的 ACR 实例的登录服务器名称。

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、ACR 实例和所有容器映像，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令将其删除。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，你已使用 Azure CLI 创建 Azure 容器注册表。 如果要对 Azure 容器实例使用 Azure 容器注册表，请继续学习 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/