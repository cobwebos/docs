---
title: "部署 Java 远程监视解决方案 - Azure | Microsoft Docs"
description: "本教程演示如何使用 CLI 预配远程监视预配置解决方案 Java 微服务。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 12/12/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: ea3764299d07f548abbc2857a3adbfb4dc50dec8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-using-the-cli"></a>使用 CLI 部署远程监视预配置解决方案

本教程演示如何预配远程监视预配置解决方案。 使用 CLI 部署解决方案。 还可以从 azureiotsuite.com 使用基于 Web 的 UI 部署解决方案，有关此选项的详细信息，请参阅[部署远程监视预配置解决方案](iot-suite-remote-monitoring-deploy.md)。

## <a name="prerequisites"></a>先决条件

要部署远程监视预配置解决方案，需要一个有效的 Azure 订阅。

如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](http://azure.microsoft.com/pricing/free-trial/)。

要运行 CLI，需在本地计算机上安装 [Node.js](https://nodejs.org/)。

## <a name="install-the-cli"></a>安装 CLI

要安装 CLI，请在命令行环境中运行以下命令：

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>登录到 CLI

在部署预配置解决方案之前，必须先使用 CLI 登录到 Azure 订阅，如下所示：

```cmd/sh
pcs login
```

按照屏幕说明完成登录过程。

## <a name="deployment-options"></a>部署选项

在部署预配置解决方案时，有几种选项可用于配置部署进程：

| 选项 | 值 | 说明 |
| ------ | ------ | ----------- |
| SKU    | `basic`、`standard` | 基本部署适用于测试和演示，它将所有微服务部署到一个虚拟机上。 标准部署适用于生产，它将微服务部署到多个虚拟机上。 |
| 运行时 | `dotnet`、`java` | 选择微服务的语言实现。 |

## <a name="deploy-the-preconfigured-solution"></a>部署预配置解决方案

### <a name="example-deploy-net-version"></a>示例：部署 .NET 版本

下面的示例展示如何部署远程监视预配置解决方案的基本 .NET 版本：

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>示例：部署 Java 版本

下面的示例展示如何部署远程监视预配置解决方案的标准 Java 版本：

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>pcs 命令选项

运行 `pcs` 命令部署解决方案时，需要提供：

- 解决方案的名称。 此名称必须唯一。
- 要使用的 Azure 订阅。
- 位置。
- 托管微服务的虚拟机的凭据。 在进行疑难解答时，可使用这些凭据访问虚拟机。

`pcs` 命令完成时，它显示预配置解决方案新部署的 URL。 `pcs` 命令还创建一个含有其他信息（例如之前预配的 IoT 中心的名称）的文件 `{deployment-name}-output.json`。

有关该命令行参数的详细信息，请运行：

```cmd/sh
pcs -h
```

有关 CLI 的详细信息，请参阅 [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md)（如何使用 CLI）。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 配置预配置解决方案
> * 部署预配置解决方案
> * 登录到预配置解决方案

现在已部署远程监视解决方案，下一步是[浏览解决方案仪表板的功能](./iot-suite-remote-monitoring-deploy.md)。

<!-- Next tutorials in the sequence -->