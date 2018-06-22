---
title: 部署 Java 远程监视解决方案 - Azure | Microsoft Docs
description: 本教程演示如何使用 CLI 预配远程监视解决方案加速器。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 603ce00a036822fd0c7411b47cf3fe630671cc5a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628117"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>使用 CLI 部署远程监视解决方案加速器

本教程演示如何预配远程监视解决方案加速器。 使用 CLI 部署解决方案。 还可以从 azureiotsuite.com 使用基于 Web 的 UI 部署解决方案。有关此选项的详细信息，请参阅[部署远程监视解决方案加速器](iot-accelerators-remote-monitoring-deploy.md)。

## <a name="prerequisites"></a>先决条件

若要部署远程监视解决方案加速器，需要一个有效的 Azure 订阅。

如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](http://azure.microsoft.com/pricing/free-trial/)。

要运行 CLI，需在本地计算机上安装 [Node.js](https://nodejs.org/)。

## <a name="install-the-cli"></a>安装 CLI

要安装 CLI，请在命令行环境中运行以下命令：

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>登录到 CLI

在部署解决方案加速器案之前，必须先使用 CLI 登录到 Azure 订阅，如下所示：

```cmd/sh
pcs login
```

按照屏幕说明完成登录过程。

## <a name="deployment-options"></a>部署选项

在部署解决方案加速器时，有几种选项可用于配置部署进程：

| 选项 | 值 | 说明 |
| ------ | ------ | ----------- |
| SKU    | `basic`、`standard`、`local` | 基本部署适用于测试和演示，它将所有微服务部署到一个虚拟机上。 标准部署适用于生产，它将微服务部署到多个虚拟机上。 本地部署通过配置 Docker 容器在本地计算机中运行微服务，并在云中使用 Azure 服务（如存储和 Cosmos DB）。 |
| 运行时 | `dotnet`、`java` | 选择微服务的语言实现。 |

要了解如何使用本地部署，请参阅[在本地运行远程监视解决方案](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables)。

## <a name="basic-vs-standard-deployments"></a>基本部署与标准部署

### <a name="basic"></a>基本
基本部署的目的是展示解决方案。 为了降低此演示的成本，所有微服务都部署在单个虚拟机上；不应将此视为生产就绪型体系结构。

准备好自定义生产就绪型体系结构时，应使用专用于缩放和可扩展性的“标准”部署选项。

创建基本解决方案时，以下 Azure 服务会按原价预配到 Azure 订阅中： 

| Count | 资源                       | Type         | 用途 |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux 虚拟机](https://azure.microsoft.com/services/virtual-machines/) | 标准 D1 V2  | 托管微服务 |
| 1     | [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)                  | S1 – 基本层 | 设备管理和通信 |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | 标准        | 存储配置数据和设备遥测数据，例如规则、警报和消息 |  
| 1     | [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | 标准        | 适用于 VM 和流式处理检查点的存储 |
| 1     | [Web 应用程序](https://azure.microsoft.com/services/app-service/web/)        |                 | 托管前端 Web 应用程序 |

### <a name="standard"></a>标准
标准部署是生产就绪型部署，可供开发人员根据需求进行自定义和扩展。 出于可靠性和缩放考虑，可将应用程序微服务构建为 Docker 容器，并使用某个业务流程协调程序（默认为 [Kubernetes](https://kubernetes.io/)）进行部署。 该业务流程协调程序负责部署、缩放和管理应用程序。

创建标准解决方案时，以下 Azure 服务会按原价预配到 Azure 订阅中：

| Count | 资源                                     | SKU/大小      | 用途 |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Linux 虚拟机](https://azure.microsoft.com/services/virtual-machines/)   | 标准 D2 V2  | 1 个 master，3 个代理，用于在确保冗余的情况下托管微服务 |
| 1     | [Azure 容器服务](https://azure.microsoft.com/services/container-service/) |                 | [Kubernetes](https://kubernetes.io) 业务流程协调程序 |
| 1     | [Azure IoT 中心][https://azure.microsoft.com/services/iot-hub/]                     | S1 – 基本层 | 设备管理、命令和控制 |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | 标准        | 存储配置数据和设备遥测数据，例如规则、警报和消息 |
| 5     | [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | 标准        | 4 个用于 VM 存储，1 个用于流式处理检查点 |
| 1     | [应用服务](https://azure.microsoft.com/services/app-service/web/)             | S1 标准     | 基于 SSL 的应用程序网关 |

> [此处](https://azure.microsoft.com/pricing)提供这些服务的定价信息。 [Azure 门户](https://portal.azure.com/)中提供订阅的使用量和计费详细信息。

## <a name="deploy-the-solution-accelerator"></a>部署解决方案加速器

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

`pcs` 命令完成时，它显示解决方案加速器新部署的 URL。 `pcs` 命令还创建一个含有其他信息（例如之前预配的 IoT 中心的名称）的文件 `{deployment-name}-output.json`。

有关该命令行参数的详细信息，请运行：

```cmd/sh
pcs -h
```

有关 CLI 的详细信息，请参阅 [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md)（如何使用 CLI）。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 配置解决方案加速器
> * 部署解决方案加速器
> * 登录到解决方案加速器

现在已部署远程监视解决方案，下一步是[浏览解决方案仪表板的功能](./iot-accelerators-remote-monitoring-deploy.md)。

<!-- Next tutorials in the sequence -->