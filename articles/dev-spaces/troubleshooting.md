---
title: 故障排除 | Microsoft 文档
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
manager: douge
ms.openlocfilehash: 371bb9195266f3511d115de2532e6b64f49ef26f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199290"
---
# <a name="troubleshooting-guide"></a>故障排除指南

本指南介绍使用 Azure Dev Spaces 时可能会碰到的常见问题。

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>“上游连接错误或者在标头之前存在断开连接/重置设置”错误
可能会在尝试访问服务时看到此错误。 例如，可能会在浏览器中访问服务的 URL 时看到此错误。 

### <a name="reason"></a>原因 
容器端口不可用。 这可能是因为： 
* 容器仍在生成和部署过程中。 如果先运行 `azds up` 或启动调试器，然后尝试在容器成功部署之前访问容器，则可能会出现这种情况。
* 端口配置在 Dockerfile、Helm 图表以及任何用于打开端口的服务器代码中不一致。

### <a name="try"></a>请尝试：
1. 如果容器正在生成/部署过程中，则可等待 2-3 秒，然后尝试再次访问服务。 
1. 检查端口配置。 指定的端口号在下面的所有资产中应该**相同**：
    * **Dockerfile：** 通过 `EXPOSE` 指令指定。
    * **[Helm 图表](https://docs.helm.sh)：** 通过服务的 `externalPort` 和 `internalPort` 值（通常位于 `values.yml` 文件中）指定。
    * 在应用程序代码（例如，Node.js 的 `var server = app.listen(80, function () {...}` ）中打开的任何端口


## <a name="config-file-not-found"></a>找不到配置文件
运行 `azds up`，然后出现以下错误：`Config file not found: .../azds.yaml`

### <a name="reason"></a>原因
必须从要运行的代码的根目录运行 `azds up`，并且必须初始化代码文件夹，以便使用 Azure Dev Spaces 来运行。

### <a name="try"></a>请尝试：
1. 将当前目录切换到包含服务代码的根文件夹。 
1. 如果代码文件夹中没有 azds.yaml 文件，请运行 `azds prep`，以便生成 Docker、Kubernetes 和 Azure Dev Spaces 资产。

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>错误：“管道程序 'azds' 意外退出，代码为 126。”
启动 VS Code 调试器有时可能会导致此错误。 这是已知问题。

### <a name="try"></a>请尝试：
1. 关闭 VS Code，再将其重新打开。
2. 再次按 F5。


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>调试错误“已配置的调试类型 'coreclr' 不受支持”
运行 VS Code 调试器时，报告错误：`Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>原因
未在开发计算机上安装适用于 Azure Dev Spaces 的 VS Code 扩展。

### <a name="try"></a>请尝试：
安装[适用于 Azure Dev Spaces 的 VS Code 扩展](get-started-netcore.md)。

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>找不到类型或命名空间名称“MyLibrary”。

### <a name="reason"></a>原因 
生成上下文默认位于项目/服务级别，因此找不到要使用的库项目。

### <a name="try"></a>请尝试：
需执行的操作：
1. 修改 azds.yaml 文件，将生成上下文设置为解决方案级别。
2. 修改 Dockerfile 和 Dockerfile.develop 文件，使之正确引用 csproj 文件（相对于新的生成上下文来说）。
3. 将 .dockerignore 文件置于 .sln 文件旁边，根据需要进行修改。

可以在 https://github.com/sgreenmsft/buildcontextsample 中找到一个示例

## <a name="microsoftconnectedenvironmentregisteraction-authorization-error"></a>'Microsoft.ConnectedEnvironment/register/action' 授权错误
若要管理 Azure Dev Space 并在 Azure 订阅中工作，但没有该订阅的“所有者”或“参与者”访问权限，则可能会看到以下错误。
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.ConnectedEnvironment/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>原因
所选 Azure 订阅尚未注册 Microsoft.ConnectedEnvironment 命名空间。

### <a name="try"></a>请尝试：
某些具有 Azure 订阅的“所有者”或“参与者”访问权限的用户可以运行以下 Azure CLI 命令，以便手动注册 Microsoft.ConnectedEnvironment 命名空间：

```cmd
az provider register --namespace Microsoft.ConnectedEnvironment
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces 似乎没有使用我的现有 Dockerfile 来生成容器 

### <a name="reason"></a>原因
Azure Dev Spaces 可能会配置为指向项目中的特定 Dockerfile。 如果 Azure Dev Spaces 看起来并未使用预期的 Dockerfile 来生成容器，则可能需要显式告知 Azure Dev Spaces 其位置。 

### <a name="try"></a>请尝试：
打开 `azds.yaml` 文件，该文件是由 Azure Dev Spaces 在项目中生成的。 若要指向要使用的 Dockerfile，请使用 `configurations->develop->build->dockerfile` 指令：

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```