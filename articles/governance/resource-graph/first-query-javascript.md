---
title: 快速入门：你的第一个 JavaScript 查询
description: 本快速入门介绍为 JavaScript 启用 Resource Graph 库并运行第一个查询的步骤。
ms.date: 06/23/2020
ms.topic: quickstart
ms.custom: devx-track-javascript
ms.openlocfilehash: fc9809dbe615950b4f5c9e4dc66cefcd054f5ebf
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87415918"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>快速入门：使用 JavaScript 运行你的第一个 Resource Graph 查询

本快速入门将指导你完成将该库添加到 JavaScript 安装的过程。 使用 Azure Resource Graph 的第一步是使用所需的库初始化 JavaScript 应用程序。

在此过程结束时，你应已将该库添加到 JavaScript 安装中，并可运行你的第一个 Resource Graph 查询。

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

- **Node.js**：需要 [Node.js](https://nodejs.org/) 版本 12 或更高版本。

## <a name="application-setup"></a>应用程序设置

若要启用 JavaScript 以查询 Azure Resource Graph，必须设置环境。 此设置适用于可使用 JavaScript 的任何环境，包括 [Bash on Windows 10](/windows/wsl/install-win10)。

1. 运行以下命令，设置新的 Node.js 项目。

   ```bash
   npm init -y
   ```

1. 添加对 yargs 模块的引用。

   ```bash
   npm install yargs
   ```

1. 添加对 Azure Resource Graph 模块的引用。

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. 添加对 Azure 身份验证库的引用。

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > 在 package.json 中验证 `@azure/arm-resourcegraph` 是否为版本 2.0.0 或更高版本，并且 `@azure/ms-rest-nodeauth` 是否为版本 3.0.3 或更高版本 。

## <a name="query-the-resource-graph"></a>查询 Resource Graph

1. 创建一个名为 index.js 的文件，并输入以下代码。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query && argv.subs) {
       const subscriptionList = argv.subs.split(",");

       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query,
                 subscriptions: subscriptionList,
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

1. 在终端中输入以下命令：

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   请确保将 `<YOUR_SUBSCRIPTION_ID_LIST>` 占位符替换为以逗号分隔的 Azure 订阅 ID 列表。

   > [!NOTE]
   > 由于此查询示例未提供排序修饰符（例如 `order by`），因此多次运行此查询可能会为每个请求生成一组不同的资源。

1. 将第一个参数更改为 `index.js`，并将查询更改为 `order by` Name 属性。 将 `<YOUR_SUBSCRIPTION_ID_LIST>` 替换为订阅 ID：

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   当脚本尝试进行身份验证时，会在终端中显示类似于以下所示的消息：

   > 若要登录，请使用 Web 浏览器打开页面 https://microsoft.com/devicelogin ，然后输入代码 FGB56WJUGK 进行身份验证。

   在浏览器中进行身份验证后，该脚本将继续运行。

   > [!NOTE]
   > 与第一个查询一样，多次运行此查询可能会为每个请求生成一组不同的资源。 查询命令的顺序非常重要。 在本例中，`order by` 位于 `limit` 之后。 命令按此顺序执行，首先会限制查询结果，然后对它们进行排序。

1. 将第一个参数更改为 `index.js`，并将查询更改为先 `order by` Name 属性，然后对前五个结果应用 `limit`。 将 `<YOUR_SUBSCRIPTION_ID_LIST>` 替换为订阅 ID：

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

假设环境中没有任何变化，则多次运行最后一个查询时，返回的结果将是一致的且按 Name 属性排序，但仍限制为前五个结果。

## <a name="clean-up-resources"></a>清理资源

如果希望从应用程序中删除已安装的库，请运行以下命令。

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何将 Resource Graph 库添加到 JavaScript 环境并运行第一个查询。 若要详细了解 Resource Graph 语言，请继续阅读查询语言详细信息页。

> [!div class="nextstepaction"]
> [获取有关查询语言的详细信息](./concepts/query-language.md)