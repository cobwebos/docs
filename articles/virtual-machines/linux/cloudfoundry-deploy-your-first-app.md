---
title: "将首个应用部署到 Microsoft Azure 上的 Cloud Foundry | Microsoft Docs"
description: "将应用程序部署到 Azure 上的 Cloud Foundry"
services: virtual-machines-linux
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: b617127fc0a3f8dcae293e356ea669edcfa5deff
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>将首个应用部署到 Microsoft Azure 上的 Cloud Foundry

[Cloud Foundry](http://cloudfoundry.org) 是 Microsoft Azure 上提供的一个常见开源应用程序平台。 本文演示如何在 Azure 环境中部署和管理 Cloud Foundry 上的应用程序。

## <a name="create-a-cloud-foundry-environment"></a>创建 Cloud Foundry 环境

可通过几种方式在 Azure 上创建 Cloud Foundry 环境：

- 使用 Azure Marketplace 中的 [Pivotal Cloud Foundry 产品][pcf-azuremarketplace]创建包括 PCF Ops Manager 和 Azure Service Broker 的标准环境。 可在 Pivotal 文档中找到有关部署应用商店产品的[完整说明][pcf-azuremarketplace-pivotaldocs]。
- 通过[手动部署 Pivotal Cloud Foundry ][pcf-custom]创建自定义环境。
- 通过设置 [BOSH](http://bosh.io) 控制器（一种协调 Cloud Foundry 环境部署的 VM），[直接部署开源 Cloud Foundry 包][oss-cf-bosh]。

> [!IMPORTANT] 
> 如果要从 Azure Marketplace 部署 PCF，请记下访问 Pivotal 应用管理器所需的 SYSTEMDOMAINURL 和管理员凭据，应用商店部署指南中对两者都有介绍。 完成本教程也需要它们。 在应用商店部署中，SYSTEMDOMAINURL 的形式为 https://system.*ip-address*.cf.pcfazure.com。

## <a name="connect-to-the-cloud-controller"></a>连接到云控制器

云控制器是部署和管理应用程序的 Cloud Foundry 环境的主要入口点。 核心云控制器 API (CCAPI) 是一种 REST API，但可通过各种工具访问。 在本例中，通过 [Cloud Foundry CLI][cf-cli] 与其进行交互。 可以在 Linux 、MacOS 或 Windows 上安装 CLI，但如果不想安装，可将其预安装在 [Azure Cloud Shell][cloudshell-docs] 中。

若要登录，请将 `api` 追加到从应用商店部署中获得的 SYSTEMDOMAINURL 中。 由于默认部署使用自签名证书，因此还应设置 `skip-ssl-validation` 开关。

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

系统将提示登录云控制器。 使用从应用商店部署步骤获取的管理员帐户凭据。

Cloud Foundry 提供组织和空间作为命名空间，隔离共享部署中的团队和环境。 PCF 应用商店部署包括默认值系统组织和一组用于包含自动缩放服务和 Azure Service Broker 等基本组件的空间。 目前选择系统空间。


## <a name="create-an-org-and-space"></a>创建组织和空间

如果键入 `cf apps`，则会看到一组已部署在系统组织的系统空间中的系统应用程序。 

应为系统应用程序保留系统组织，因此请创建组织和空间以容纳示例应用程序。

```bash
cf create-org myorg
cf create-space dev -o myorg
```

使用目标命令切换到新的组织和空间：

```bash
cf target -o testorg -s dev
```

现在，部署应用程序时，其会自动创建在新的组织和空间中。 若要确认新的组织/空间中目前没有任何应用，请再次键入 `cf apps`。

> [!NOTE] 
> 若要深入了解组织和空间以及如何将其用于基于角色的访问控制 (RBAC)，请参阅 [Cloud Foundry 文档][cf-orgs-spaces-docs]。

## <a name="deploy-an-application"></a>部署应用程序

使用名为 Hello Spring Cloud 的示例 Cloud Foundry 应用程序，该应用程序基于 [Spring Framework](http://spring.io) 及 [Spring Boot](http://projects.spring.io/spring-boot/) 并以 Java 语言编写。

### <a name="clone-the-hello-spring-cloud-repository"></a>克隆 Hello Spring 云存储库

GitHub 中提供了 Hello Spring Cloud 示例云应用程序。 将其克隆到你的环境，并将其更改到新的目录中：

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>构建应用程序

使用 [Apache Maven ](http://maven.apache.org) 生成应用。

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>通过 cf push 部署应用程序

使用 `push` 命令，可将大多数应用程序部署到 Cloud Foundry：

```bash
cf push
```

推送应用程序时，Cloud Foundry 会检测应用程序的类型（此例中是 Java 应用），并标识其依赖项（此例中是 Spring Framework）。 然后，它将运行代码所需的所有内容打包到独立的容器映像中，称为水滴。 最后，Cloud Foundry 会在你环境中的一台可用计算机上安排应用程序，并创建一个可访问的 URL（命令输出中提供）。

![cf push 命令输出][cf-push-output]

若要查看 hello-spring-cloud 云应用程序，请在浏览器中打开所提供的 URL：

![Hello Spring Cloud 的默认 UI][hello-spring-cloud-basic]

> [!NOTE] 
> 若要深入了解 `cf push` 过程，请参阅 Cloud Foundry 文档中的[如何暂存应用程序][cf-push-docs]。

## <a name="view-application-logs"></a>查看应用程序日志

可使用 Cloud Foundry CLI 通过应用程序名称查看其日志：

```bash
cf logs hello-spring-cloud
```

默认情况下，日志命令会使用结尾，显示新写入的日志。 若要查看显示的新日志，请在浏览器中刷新 hello-spring-cloud 应用程序。

若要查看已写入的日志，请添加 `recent` 开关：

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>缩放应用程序

默认情况下，`cf push` 仅创建单个应用程序实例。 为确保高可用性并能扩大以提高吞吐量，通常需要运行多个应用程序实例。 使用 `scale` 命令，可轻松地扩大已部署的应用程序：

```bash
cf scale -i 2 hello-spring-cloud
```

在应用程序上运行 `cf app` 命令，显示 Cloud Foundry 正在创建应用程序的另一个实例。 应用程序启动后，Cloud Foundry 将自动启动到该应用程序的负载平衡通信。


## <a name="next-steps"></a>后续步骤

- [阅读 Cloud Foundry 文档 ][cloudfoundry-docs]
- [为 Cloud Foundry 安装 Visual Studio Team Services 插件 ][vsts-plugin]
- [为 Cloud Foundry 配置 Microsoft Log Analytics Nozzle ][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: http://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png