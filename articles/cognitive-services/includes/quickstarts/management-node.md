---
title: 快速入门：适用于 Node.js 的 Azure 管理客户端库
description: 在本快速入门中，开始使用适用于 Node.js 的 Azure 管理客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: fcfefb2b6cd5c23e9b77d7d27413d1ff6d775bfe
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321536"
---
[参考文档](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [包 (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="prerequisites"></a>先决条件

* 一个有效的 Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。
* 最新版本的 [Node.js](https://nodejs.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

运行 `npm init` 命令以使用 `package.json` 文件创建一个 node 应用程序。 

```console
npm init
```

继续之前，请先创建一个名为 index.js 的文件。

### <a name="install-the-client-library"></a>安装客户端库

安装以下 NPM 包：

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

应用的 `package.json` 文件将使用依赖项进行更新。

### <a name="import-libraries"></a>导入库

打开 index.js 脚本并导入以下库。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>验证客户端

将以下字段添加到脚本的根目录中，并使用所创建的服务主体和 Azure 帐户信息填写其值。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

接下来，添加以下 `quickstart` 函数来处理程序的主要工作。 第一个代码块使用你在上面输入的凭据变量构造一个 CognitiveServicesManagementClient 对象。 所有 Azure 管理操作都需要此对象。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>调用管理函数

将以下代码添加到 `quickstart` 函数的末尾，以列出可用资源、创建示例资源、列出拥有的资源，然后删除示例资源。 你将在后续步骤中定义这些函数。

## <a name="create-a-cognitive-services-resource"></a>创建认知服务资源

### <a name="choose-a-service-and-pricing-tier"></a>选择服务和定价层

创建新资源时，需要知道要使用的服务的种类，以及所需的[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/)（或 SKU）。 创建资源时，将此信息和其他信息用作参数。 以下函数列出了可用的认知服务种类。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="create-a-cognitive-services-resource"></a>创建认知服务资源

若要创建并订阅新的认知服务资源，请使用 Create 函数。 此函数向传入的资源组添加新的可计费资源。 创建新资源时，需要知道要使用的服务的种类，以及其定价层（或 SKU）和 Azure 位置。 下面的函数使用所有这些参数并创建资源。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

## <a name="view-your-resources"></a>查看资源

若要查看 Azure 帐户下的所有资源（跨所有资源组），请使用以下函数：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>删除资源

下面的函数从给定的资源组中删除指定的资源。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>运行应用程序

将以下代码添加到脚本底部，以调用包含错误处理的主 `quickstart` 函数。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

然后，在控制台窗口中，使用 `node` 命令运行应用程序。

```console
node index.js
```

## <a name="see-also"></a>另请参阅

* [Azure 管理 SDK 参考文档](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [什么是 Azure 认知服务？](../../Welcome.md)
* [对 Azure 认知服务的请求进行身份验证](../../authentication.md)
* [使用 Azure 门户创建新资源](../../cognitive-services-apis-create-account.md)