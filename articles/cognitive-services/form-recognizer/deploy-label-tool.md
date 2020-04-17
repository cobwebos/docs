---
title: 如何部署表单识别器示例标签工具
titleSuffix: Azure Cognitive Services
description: 了解部署表单识别器示例标签工具以帮助进行监督学习的不同方法。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 7ddb4b2cd465b5e9542d777d33b9bd8cb952becd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531331"
---
# <a name="deploy-the-sample-labeling-tool"></a>部署示例标记工具

窗体识别器示例标签工具是一个应用程序，它提供了一个简单的用户界面 （UI），可用于手动标记表单（文档），以便进行监督学习。 在本文中，我们将提供链接和说明，教您如何：

* [在本地运行示例标记工具](#run-the-sample-labeling-tool-locally)
* [将示例标记工具部署到 Azure 容器实例 （ACI）](#deploy-with-azure-container-instances-aci)
* [使用和贡献开源 OCR 表单标签工具](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>在本地运行示例标记工具

开始标记数据的最快方法是在本地运行示例标记工具。 以下快速入门使用表单识别器 REST API 和示例标记工具使用手动标记的数据训练自定义模型。 

* [快速启动：使用示例标记工具标记窗体、训练模型和分析窗体](./quickstarts/label-tool.md)。

## <a name="deploy-with-azure-container-instances-aci"></a>使用 Azure 容器实例 （ACI） 部署

在开始之前，请务必注意，有两种方法可以将示例标记工具部署到 Azure 容器实例 （ACI）。 这两个选项都用于使用 ACI 运行示例标记工具： 

* [使用 Azure 门户](#azure-portal)
* [使用 Azure CLI](#azure-cli)

### <a name="azure-portal"></a>Azure 门户

按照以下步骤使用 Azure 门户创建新资源： 

1. 登录 [Azure 门户](https://portal.azure.com/signin/index/)。
2. 选择“创建资源”。  
3. 接下来，选择**Web 应用程序**。 

   > [!div class="mx-imgBorder"]
   > ![选择 Web 应用](./media/quickstarts/formre-create-web-app.png)
   
4. 首先，请确保选择了 **"基础知识"** 选项卡。 现在，您需要提供一些信息： 

   > [!div class="mx-imgBorder"]
   > ![选择基础知识](./media/quickstarts/formre-select-basics.png)
   * 订阅 - 选择现有的 Azure 订阅
   * 资源组 - 您可以重用现有资源组或为此项目创建新资源组。 建议创建新的资源组。
   * 名称 - 为 Web 应用指定名称。 
   * 发布 - 选择**Docker 容器**
   * 操作系统 - 选择**Linux**
   * 区域 - 选择适合的区域。
   * Linux 计划 - 为您的应用服务选择定价层/计划。 

   > [!div class="mx-imgBorder"]
   > ![配置 Web 应用](./media/quickstarts/formre-select-docker-linux.png)

5. 接下来，选择**Docker**选项卡。 

   > [!div class="mx-imgBorder"]
   > ![选择 Docker](./media/quickstarts/formre-select-docker.png)

6. 现在，让我们配置 Docker 容器。 除非另有说明，否则所有字段都是必需的：

   * 选项 - 选择**单个容器**
   * 图像源 - 选择**专用注册表** 
   * 服务器 URL - 将此设置为`https://mcr.microsoft.com`
   * 用户名（可选） - 创建用户名。 
   * 密码（可选） - 创建您记得的安全密码。
   * 图像和标记 - 将此设置为`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * 连续部署 - 如果要在开发团队更改示例标记工具时接收自动更新，则将其设置为 **"打开**"。
   * 启动命令 - 将此设置为`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![配置 Docker](./media/quickstarts/formre-configure-docker.png)

7. 就这么简单。 接下来，选择 **"查看 + 创建**"，然后**创建**以部署 Web 应用。 完成后，您可以在资源**概述**中提供的 URL 访问 Web 应用。

> [!NOTE]
> 创建 Web 应用时，还可以配置授权/身份验证。 这是没有必要的开始。 

### <a name="azure-cli"></a>Azure CLI

作为使用 Azure 门户的替代方法，可以使用 Azure CLI 创建资源。 在继续之前，您需要安装[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 如果您已经在使用 Azure CLI，则可以跳过此步骤。 

关于此命令，您需要了解一些事项：

* `DNS_NAME_LABEL=aci-demo-$RANDOM`生成随机 DNS 名称。 
* 此示例假定您有一个可用于创建资源的资源组。 替换为`<resource_group_name>`与您的订阅关联的有效资源组。 
* 您需要指定要创建资源的位置。 替换为`<region name>`Web 应用所需的区域。 
* 此命令自动接受 EULA。

在 Azure CLI 中，运行此命令以创建示例标签工具的 Web 应用资源： 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resorunce_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>连接到 Azure AD 进行授权

建议您将 Web 应用连接到 Azure 活动目录。 这可确保只有具有有效凭据的用户才能登录并使用 Web 应用。 按照["配置应用服务"应用](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)中的说明进行连接到 Azure 活动目录。

## <a name="open-source-on-github"></a>GitHub 上的开源

OCR 表单标签工具也可作为 GitHub 上的开源项目提供。 该工具是使用 React + Redux 生成并采用 TypeScript 编写的 web 应用程序。 若要了解详细信息或参与，请参阅 [OCR 窗体标记工具](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)。

## <a name="next-steps"></a>后续步骤

使用[带有标签的"训练"](./quickstarts/label-tool.md)快速入门学习如何使用该工具手动标记训练数据并执行监督学习。
