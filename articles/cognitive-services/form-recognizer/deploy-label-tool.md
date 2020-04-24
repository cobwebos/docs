---
title: 如何部署窗体识别器示例标签工具
titleSuffix: Azure Cognitive Services
description: 了解部署表单识别器示例标记工具的不同方法，以帮助进行监督。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 27afbafcadb4c482e97e1d003706e7d2712e63c9
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117261"
---
# <a name="deploy-the-sample-labeling-tool"></a>部署示例标记工具

窗体识别器示例标记工具是提供简单用户界面（UI）的应用程序，可以使用它来手动标记窗体（文档）以供监督学习。 本文介绍如何执行以下操作的链接和说明：

* [在本地运行示例标签工具](#run-the-sample-labeling-tool-locally)
* [将示例标记工具部署到 Azure 容器实例（ACI）](#deploy-with-azure-container-instances-aci)
* [使用并参与开源 OCR 窗体标签工具](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>在本地运行示例标签工具

开始标记数据的最快方法是在本地运行示例标记工具。 以下快速入门使用窗体识别器 REST API 和示例标记工具来训练包含手动标记的数据的自定义模型。 

* [快速入门：标签窗体，为模型定型，并使用示例标记工具分析窗体](./quickstarts/label-tool.md)。

## <a name="deploy-with-azure-container-instances-aci"></a>用 Azure 容器实例（ACI）部署

在开始之前，请务必注意，可以通过两种方法将示例标记工具部署到 Azure 容器实例（ACI）。 这两个选项用于使用 ACI 运行示例标签工具： 

* [使用 Azure 门户](#azure-portal)
* [使用 Azure CLI](#azure-cli)

### <a name="azure-portal"></a>Azure 门户

按照以下步骤使用 Azure 门户创建新资源： 

1. 登录 [Azure 门户](https://portal.azure.com/signin/index/)。
2. 选择“创建资源”。  
3. 接下来，选择 " **Web 应用**"。 

   > [!div class="mx-imgBorder"]
   > ![选择 web 应用](./media/quickstarts/formre-create-web-app.png)
   
4. 首先，请确保已选择 "**基本**信息" 选项卡。 接下来，需要提供一些信息： 

   > [!div class="mx-imgBorder"]
   > ![选择基本信息](./media/quickstarts/formre-select-basics.png)
   * 订阅-选择现有的 Azure 订阅
   * 资源组-可以重复使用现有资源组，也可以为此项目创建一个新资源组。 建议创建新的资源组。
   * 名称-为 web 应用命名。 
   * 发布-选择**Docker 容器**
   * 操作系统-选择**Linux**
   * 区域-选择适合你的区域。
   * Linux 计划-为应用服务选择定价层/计划。 

   > [!div class="mx-imgBorder"]
   > ![配置 web 应用](./media/quickstarts/formre-select-docker-linux.png)

5. 接下来，选择 " **Docker** " 选项卡。 

   > [!div class="mx-imgBorder"]
   > ![选择 Docker](./media/quickstarts/formre-select-docker.png)

6. 现在，让我们配置 Docker 容器。 除非另有说明，否则所有字段都是必需的：

   * 选项-选择**单个容器**
   * 图像源-选择**专用注册表** 
   * 服务器 URL-将此项设置为`https://mcr.microsoft.com`
   * Username （可选）-创建用户名。 
   * 密码（可选）-创建要记住的安全密码。
   * Image 和 tag-将此项设置为`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * 连续部署-如果想要在开发团队对示例标签工具进行更改时接收自动更新，请将此项设置为**On** 。
   * 启动命令-将此项设置为`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![配置 Docker](./media/quickstarts/formre-configure-docker.png)

7. 就这么简单。 接下来，依次选择 "**查看**" 和 "创建"，然后单击 "**创建**" 以部署 web 应用。 完成后，你可以在资源的 "**概述**" 中提供的 URL 访问你的 web 应用。

> [!NOTE]
> 创建 web 应用时，还可以配置授权/身份验证。 这并不是必需的。 

### <a name="azure-cli"></a>Azure CLI

作为使用 Azure 门户的替代方法，可以使用 Azure CLI 创建资源。 继续之前，需要安装[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 如果已使用 Azure CLI，则可以跳过此步骤。 

有关此命令，需要了解以下几个事项：

* `DNS_NAME_LABEL=aci-demo-$RANDOM`生成一个随机 DNS 名称。 
* 此示例假设您有一个可用于创建资源的资源组。 将`<resource_group_name>`替换为与订阅关联的有效资源组。 
* 你需要指定要创建资源的位置。 将`<region name>`替换为 web 应用所需的区域。 
* 此命令自动接受 EULA。

在 Azure CLI 中运行以下命令，为示例标签工具创建 web 应用资源： 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>连接到 Azure AD 进行授权

建议将 web 应用连接到 Azure Active Directory。 这可确保只有具有有效凭据的用户可以登录并使用你的 web 应用。 按照[将应用服务应用配置](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)为连接到 Azure Active Directory 中的说明进行操作。

## <a name="open-source-on-github"></a>GitHub 上的开放源代码

OCR 窗体标签工具也可作为 GitHub 上的开源项目。 该工具是使用 React + Redux 生成并采用 TypeScript 编写的 web 应用程序。 若要了解详细信息或参与，请参阅 [OCR 窗体标记工具](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)。

## <a name="next-steps"></a>后续步骤

使用[带标签的训练](./quickstarts/label-tool.md)快速入门，了解如何使用该工具手动标记定型数据并执行监督式学习。
