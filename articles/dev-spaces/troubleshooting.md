---
title: 故障排除 | Microsoft 文档
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 09/11/2018
ms.topic: article
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
manager: douge
ms.openlocfilehash: 3f30a62a2f351aecabc37206607c3e28ec5e3ab5
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353352"
---
# <a name="troubleshooting-guide"></a>故障排除指南

本指南介绍使用 Azure Dev Spaces 时可能会碰到的常见问题。

## <a name="enabling-detailed-logging"></a>启用详细日志记录

为了更有效地解决问题，它可能有助于创建更详细的日志以供查看。

对于 Visual Studio 扩展，请将 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 环境变量设置为 1。 请务必重新启动 Visual Studio 以使环境变量生效。 启用后，详细的日志将写入 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 目录。

在 CLI 中，可以通过使用 `--verbose` 切换在命令执行过程中输出更多信息。 还可以在 `%TEMP%\Azure Dev Spaces` 中浏览更详细的日志。 在 Mac 上，可以通过从终端窗口运行 `echo $TMPDIR` 找到 TEMP 目录。 在 Linux 计算机上，TEMP 目录通常为 `/tmp`。

## <a name="debugging-services-with-multiple-instances"></a>使用多个实例调试服务

此时，Azure Dev Spaces 在调试单个实例 (pod) 时效果最佳。 Azds.yaml 文件包含设置 replicaCount，指示将为服务运行的 pod 数。 如果更改 replicaCount 来配置应用以运行给定服务的多个 pod，调试程序将附加到第一个 pod（当按字母顺序列出）。 如果出于任何原因回收该 pod，调试程序将附加到其他 pod，可能会导致意外行为。

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>错误“无法创建 Azure Dev Spaces 控制器”

控制器创建出现问题时，可能会看到此错误。 如果这是一个暂时性错误，删除并重新创建该控制器将修复此错误。

### <a name="try"></a>请尝试：

若要删除控制器，请使用 Azure Dev Spaces CLI。 不能在 Visual Studio 或 Cloud Shell 中执行此操作。 若要安装 AZDS CLI，请先安装 Azure CLI，然后运行此命令：

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

然后运行此命令以删除控制器：

```cmd
azds remove -g <resource group name> -n <cluster name>
```

可以从 CLI 或 Visual Studio 重新创建该控制器。 按照教程中的说明操作，就像第一次开始操作一样。


## <a name="error-service-cannot-be-started"></a>错误“无法启动服务”。

当服务代码无法启动时，你可能会看到此错误。 原因通常在用户代码中。 若要获取更多诊断信息，请对命令和设置进行以下更改：

### <a name="try"></a>请尝试：

在命令行上：

使用 _azds.exe_ 时，请使用 --verbose 命令行选项，并使用 --output 命令行选项指定输出格式。
 
    ```cmd
    azds up --verbose --output json
    ```

在 Visual Studio 中：

1. 打开“工具”>“选项”，在“项目和解决方案”下，选择“生成并运行”。
2. 将“MSBuild 项目生成输出详细级别”的设置更改为“详细”或“诊断”。

    ![“工具选项”对话框的屏幕截图](media/common/VerbositySetting.PNG)
    
尝试使用多阶段 Dockerfile 时，可能会看到此错误。 详细输出如下所示：

```cmd
$ azds up
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

这是因为 AKS 节点运行的旧版 Docker 不支持多阶段生成。 需重写 Dockerfile 以避免多阶段生成。

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>对与 Dev Spaces 服务关联的公用 URL 进行 DNS 名称解析失败

如果 DNS 名称解析失败，当尝试连接到与 Dev Spaces 服务关联的公用 URL 时，可能会在 Web 浏览器中看到“页面无法显示”或“无法访问此站点”错误。

### <a name="try"></a>请尝试：

可以使用以下命令列出与 Dev Spaces 服务关联的所有 URL：

```cmd
azds list-uris
```

如果某个 URL 处于“挂起”状态，则意味着该 Dev Spaces 仍然在等待 DNS 注册完成。 有时，注册需要几分钟的时间才能完成。 Dev Spaces 还为每个服务打开一个本地主机隧道，在等待 DNS 注册时你可以使用该隧道。

如果某个 URL 保持在“挂起”状态超过 5 分钟，则可能指示创建公共终结点的外部 DNS Pod 和/或获取公共终结点的 nginx 入口控制器 Pod 有问题。 可以使用以下命令删除这些 Pod。 这些 Pod 将自动重新创建。

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>错误“缺少必需的工具和配置”

启动 VS Code 时可能会发生此错误：“[Azure Dev Spaces] 缺少生成和调试 '[项目名称]' 所需的工具和配置。”
此错误意味着 azds.exe 不在 PATH 环境变量中，如 VS Code 中所示。

### <a name="try"></a>请尝试：

从已正确设置 PATH 环境变量的命令提示符启动 VS Code。

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>错误“azds”未识别为内部或外部命令、可运行程序或批处理文件
 
如果 azds.exe 未安装或未正确配置，可能会看到此错误。

### <a name="try"></a>请尝试：

1. 检查位置 %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI (Preview) 中是否有 azds.exe。 如果它在那里，将该位置添加到 PATH 环境变量。
2. 如果未安装 azds.exe，请运行以下命令：

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>警告“由于语言不受支持，无法生成 Dockerfile”
Azure Dev Spaces 为 C# 和 Node.js 提供本机支持。 在包含以下列语言之一编写的代码的目录中运行 azds prep 时，Azure Dev Spaces 将自动为你创建相应的 Dockerfile。

你仍可以将 Azure Dev Spaces 与其他语言编写的代码一起使用，但需要在第一次运行 azds up 之前自行创建 Dockerfile。

### <a name="try"></a>请尝试：
如果你的应用程序使用 Azure Dev Spaces 本身不支持的语言编写，你将需要提供相应的 Dockerfile 以生成运行代码的容器映像。 Docker 提供一个[用于编写 Dockerfile 的最佳做法列表](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)以及一个 [Dockerfile 参考](https://docs.docker.com/engine/reference/builder/)，可帮助你编写满足你需求的 Dockerfile。

一旦有了相应的 Dockerfile，就可以继续运行 azds up 以在 Azure Dev Spaces 中运行应用程序。

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>“上游连接错误或者在标头之前存在断开连接/重置设置”错误
可能会在尝试访问服务时看到此错误。 例如，可能会在浏览器中访问服务的 URL 时看到此错误。 

### <a name="reason"></a>原因 
容器端口不可用。 可能会发生此问题，因为： 
* 容器仍在生成和部署过程中。 如果先运行 `azds up` 或启动调试器，然后在成功部署容器之前尝试访问容器，则会出现此问题。
* 端口配置在 _Dockerfile_、Helm 图表以及任何用于打开端口的服务器代码中不一致。

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
1. 如果代码文件夹中没有 _azds.yaml_ 文件，请运行 `azds prep`，以便生成 Docker、Kubernetes 和 Azure Dev Spaces 资产。

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>错误：“管道程序 'azds' 意外退出，代码为 126。”
启动 VS Code 调试器有时可能会导致此错误。

### <a name="try"></a>请尝试：
1. 关闭 VS Code，再将其重新打开。
2. 再次按 F5。

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>调试错误“无法找到类型为 coreclr 的调试器扩展”
运行 VS Code 调试器时，报告错误：`Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>原因
未在开发计算机上安装适用于 C# 的 VS Code 扩展。 C# 扩展包括调试对 .Net Core (CoreCLR) 的支持。

### <a name="try"></a>请尝试：
安装[适用于 C# 的 VS Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。

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
1. 修改 _azds.yaml_ 文件，将生成上下文设置为解决方案级别。
2. 修改 _Dockerfile_ 和 _Dockerfile.develop_ 文件，使之正确引用项目 (_.csproj_) 文件（相对于新的生成上下文来说）。
3. 将 _.dockerignore_ 文件置于 .sln 文件旁边，根据需要进行修改。

可以在 https://github.com/sgreenmsft/buildcontextsample 中找到一个示例

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>“Microsoft.DevSpaces/register/action”授权错误
若要管理 Azure Dev Space 并在 Azure 订阅中工作，但没有该订阅的“所有者”或“参与者”访问权限，则可能会看到以下错误。
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>原因
所选 Azure 订阅尚未注册 `Microsoft.DevSpaces` 命名空间。

### <a name="try"></a>请尝试：
某个具有 Azure 订阅的“所有者”或“参与者”访问权限的用户可以运行以下 Azure CLI 命令，以便手动注册 `Microsoft.DevSpaces` 命名空间：

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>启动 Dev Spaces 时出现“Error: could not find a ready tiller pod”（错误: 找不到准备好的 Tiller Pod）

### <a name="reason"></a>原因
如果 Helm 客户端无法再与群集中运行的 Tiller Pod 通信，则会发生此错误。

### <a name="try"></a>请尝试：
重新启动群集中的代理节点通常可以解决此问题。

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces 似乎没有使用我的现有 Dockerfile 来生成容器 

### <a name="reason"></a>原因
Azure Dev Spaces 可以配置为指向项目中的特定 _Dockerfile_。 如果 Azure Dev Spaces 看起来并未使用预期的 _Dockerfile_ 来生成容器，则可能需要显式告知 Azure Dev Spaces 其位置。 

### <a name="try"></a>请尝试：
打开项目中 Azure Dev Spaces 生成的 _azds.yaml_ 文件。 若要指向要使用的 Dockerfile，请使用 `configurations->develop->build->dockerfile` 指令：

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```
