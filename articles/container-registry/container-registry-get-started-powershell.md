---
title: "快速入门 - 使用 PowerShell 在 Azure 中创建专用 Docker 注册表"
description: "快速了解如何使用 PowerShell 创建专用 Docker 容器注册表。"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2017
ms.author: nepeters
ms.openlocfilehash: b58b10e644e934cc38a6e0512ba7642ab8bf27c4
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2017
---
# <a name="create-an-azure-container-registry-using-powershell"></a>使用 PowerShell 创建 Azure 容器注册表

Azure 容器注册表是托管的 Docker 容器注册表服务，用于存储专用的 Docker 容器映像。 本指南详述了如何使用 PowerShell 创建 Azure 容器注册表实例。

本快速入门需要 Azure PowerShell 模块 3.6 版或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

还必须在本地安装 Docker。 Docker 提供的包可在任何 [Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 或 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系统上轻松配置 Docker。

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

注册表的名称必须唯一。 以下示例使用 myContainerRegistry007。 请将其更新为一个唯一值。

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>登录到 ACR

在推送和拉取容器映像之前，必须登录到 ACR 实例。 首先，使用 [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) 命令来获取 ACR 实例的管理员凭据。

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

接下来，运行 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令以登录到 ACR 实例。

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

完成后，该命令会返回“登录成功”消息。

## <a name="push-image-to-acr"></a>将映像推送到 ACR

要将映像推送到 Azure 容器注册表，首先必须具有一个映像。 如有必要，请运行以下命令，从 Docker 中心拉取预先创建的映像。

```bash
docker pull microsoft/aci-helloworld
```

必须使用 ACR 登录服务器名称标记此映像。 运行 [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) 命令来返回 ACR 实例的登录服务器名称。

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

使用 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 命令标记映像。 将 *acrLoginServer* 替换为你的 ACR 实例的登录服务器名称。

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

最后，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 将映像推送到 ACR 实例。 将 *acrLoginServer* 替换为你的 ACR 实例的登录服务器名称。

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