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
ms.openlocfilehash: b2ef450a429b26843cf770a6243c6f4de932de43
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247311"
---
# <a name="troubleshooting-guide"></a>故障排除指南

本指南介绍使用 Azure Dev Spaces 时可能会碰到的常见问题。

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