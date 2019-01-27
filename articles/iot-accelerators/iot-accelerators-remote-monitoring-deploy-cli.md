---
title: 使用 CLI 部署远程监视解决方案 - Azure | Microsoft Docs
description: 本操作指南介绍如何使用 CLI 预配远程监视解决方案加速器。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/30/2018
ms.topic: conceptual
ms.openlocfilehash: 61da17d1dc59a08128671da163d8b3ea27f83994
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463212"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>使用 CLI 部署远程监视解决方案加速器

本操作指南介绍如何部署远程监视解决方案加速器。 使用 CLI 部署解决方案。 还可以从 azureiotsuite.com 使用基于 Web 的 UI 部署解决方案。有关此选项的详细信息，请参阅[部署远程监视解决方案加速器](quickstart-remote-monitoring-deploy.md)快速入门。

## <a name="prerequisites"></a>先决条件

若要部署远程监视解决方案加速器，需要一个有效的 Azure 订阅。

如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。

要运行 CLI，需在本地计算机上安装 [Node.js](https://nodejs.org/)。

## <a name="install-the-cli"></a>安装 CLI

要安装 CLI，请在命令行环境中运行以下命令：

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>登录到 CLI

在部署解决方案加速器案之前，必须先使用 CLI 登录到 Azure 订阅：

```cmd/sh
pcs login
```

按照屏幕说明完成登录过程。

## <a name="deployment-options"></a>部署选项

在部署解决方案加速器时，有几种选项可用于配置部署进程：

| 选项 | 值 | 说明 |
| ------ | ------ | ----------- |
| SKU    | `basic`、`standard`、`local` | 基本部署适用于测试和演示，它将所有微服务部署到一个虚拟机上。 标准部署适用于生产，它将微服务部署到多个虚拟机上。 本地部署通过配置 Docker 容器在本地计算机中运行微服务，并使用 Azure 云服务（如存储和 Cosmos DB）。 |
| 运行时 | `dotnet`、`java` | 选择微服务的语言实现。 |

若要了解如何使用本地部署选项，请参阅[在本地运行远程监视解决方案](iot-accelerators-remote-monitoring-deploy-local.md)。

## <a name="basic-and-standard-deployments"></a>基本和标准部署

本部分汇总基本与标准部署之间的主要差别。

### <a name="basic"></a>基本

基本部署的目的是展示解决方案。 为了降低成本，将在单个虚拟机中部署所有微服务。 此部署不使用生产就绪型体系结构。

基本部署在 Azure 订阅中创建以下服务：

| Count | 资源                       | 类型         | 用途 |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux 虚拟机](https://azure.microsoft.com/services/virtual-machines/) | 标准 D1 V2  | 托管微服务 |
| 1     | [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)                  | S1 - 标准层 | 设备管理和通信 |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | 标准        | 存储配置数据、规则、警报和其他冷存储 |  
| 1     | [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | 标准        | 适用于 VM 和流式处理检查点的存储 |
| 1     | [Web 应用程序](https://azure.microsoft.com/services/app-service/web/)        |                 | 托管前端 Web 应用程序 |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | 管理用户标识和安全性 |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | 标准                | 查看资产位置 |
| 1     | [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)        |   3 个单位              | 启用实时分析 |
| 1     | [Azure 设备预配服务](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | 大规模预配设备 |
| 1     | [Azure 时序见解](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 个单位              | 存储消息数据并启用深入遥测分析 |

### <a name="standard"></a>标准

标准部署是生产就绪型部署，可供开发人员进行自定义和扩展。 准备好自定义生产就绪型体系结构时，请使用专用于缩放和可扩展性的标准部署选项。 应用程序微服务生成为 Docker 容器，并使用 Azure Kubernetes 服务进行部署。 Kubernetes 业务流程协调程序将部署、缩放和管理微服务。

标准部署在 Azure 订阅中创建以下服务：

| Count | 资源                                     | SKU/大小      | 用途 |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service)| 使用完全托管的 Kubernetes 容器业务流程服务，默认值为 3 个代理|
| 1     | [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)                     | S2 - 标准层 | 设备管理、命令和控制 |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | 标准        | 存储配置数据和设备遥测数据，例如规则、警报和消息 |
| 5     | [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | 标准        | 4 个用于 VM 存储，1 个用于流式处理检查点 |
| 1     | [应用服务](https://azure.microsoft.com/services/app-service/web/)             | S1 标准     | 基于 SSL 的应用程序网关 |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | 管理用户标识和安全性 |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | 标准                | 查看资产位置 |
| 1     | [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)        |   3 个单位              | 启用实时分析 |
| 1     | [Azure 设备预配服务](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | 大规模预配设备 |
| 1     | [Azure 时序见解](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 个单位              | 存储消息数据并启用深入遥测分析 |

> [!NOTE]
> 在 [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing) 中可以找到这些服务的定价信息。 可在 [Azure 门户](https://portal.azure.com/)中找到订阅的用量和计费详细信息。

## <a name="deploy-the-solution-accelerator"></a>部署解决方案加速器

部署示例：

### <a name="example-deploy-net-version"></a>示例：部署 .NET 版本

下面的示例展示如何部署远程监视解决方案加速器的基本 .NET 版本：

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>示例：部署 Java 版本

下面的示例展示如何部署远程监视解决方案加速器的标准 Java 版本：

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>pcs 命令选项

运行 `pcs` 命令部署解决方案时，需要提供：

- 解决方案的名称。 此名称必须唯一。
- 要使用的 Azure 订阅。
- 位置。
- 托管微服务的虚拟机的凭据。 在进行疑难解答时，可使用这些凭据访问虚拟机。

`pcs` 命令完成时，它显示新解决方案加速器的 URL。 `pcs` 命令还会创建 `{deployment-name}-output.json` 文件，其中包含创建的 IoT 中心的名称等信息。

有关该命令行参数的详细信息，请运行：

```cmd/sh
pcs -h
```

有关 CLI 的详细信息，请参阅 [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md)（如何使用 CLI）。

## <a name="next-steps"></a>后续步骤

通过本操作指南，我们已学会了：

> [!div class="checklist"]
> * 配置解决方案加速器
> * 部署解决方案加速器
> * 登录到解决方案加速器

部署远程监视解决方案后，接下来请[探索解决方案仪表板的功能](./quickstart-remote-monitoring-deploy.md)。

<!-- Next how-to guides in the sequence -->
