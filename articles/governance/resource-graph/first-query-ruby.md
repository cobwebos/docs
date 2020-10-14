---
title: 快速入门：你的第一个 Ruby 查询
description: 本快速入门介绍为 Ruby 启用 Resource Graph gem 并运行第一个查询的步骤。
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: 4ffd7f33fb83b7a1f247f687b77dd2ef9c8e5bc9
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057240"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>快速入门：使用 Ruby 运行你的第一个 Resource Graph 查询

使用 Azure Resource Graph 的第一步是确保为 Ruby 安装了所需的 gem。 本快速入门将指导你完成将该 gem 添加到 Ruby 安装的过程。

在此过程结束时，你已将该 gem 添加到 Ruby 安装中，并将运行你的第一个 Resource Graph 查询。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。
- Azure 服务主体，包括 clientId 和 clientSecret 。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>创建 Resource Graph 项目

若要使 Ruby 能够查询 Azure Resource Graph，则必须将 gem 添加到 `Gemfile`。 此 gem 适用于可使用 Ruby 的任何地方，包括 [Azure Cloud Shell](https://shell.azure.com)、[Windows 10 上的 bash](/windows/wsl/install-win10) 或本地安装的 bash。

1. 请确保安装最新的 Ruby（至少为 2.7.1）。 如果尚未安装，请在 [Ruby-Lang.org](https://www.ruby-lang.org/en/downloads/) 下载。

1. 在你选择的 Ruby 环境中，在新的项目文件夹中初始化一个捆绑包：

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. 更新 `Gemfile` 和 Azure Resource Graph 所需的 gem。 更新的文件应类似于以下示例：

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. 从项目文件夹中，运行 `bundle install`。 确认 gem 是否已通过 `bundle list` 安装。

1. 在同一项目文件夹中，使用以下代码创建 `argQuery.rb` 并保存更新的文件：

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>运行首个 Resource Graph 查询

保存好 Ruby 脚本并准备好使用后，可以尝试简单的 Resource Graph 查询。 该查询返回前五个 Azure 资源，以及每个资源的名称和资源类型 。

在对 `argQuery` 的每次调用中，都包含替换为自己的值时需要使用的变量：

- `{tenantId}` - 替换为租户 ID
- `{clientId}` -替换为服务主体的客户端 ID
- `{clientSecret}` -替换为服务主体的客户端机密
- `{subscriptionId}` - 替换为订阅 ID

1. 将目录更改为你在其中创建 `Gemfile` 和 `argClient.rb` 文件的项目文件夹。

1. 使用 gem 和 `resources` 方法运行首个 Azure Resource Graph 查询：

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > 由于此查询示例未提供排序修饰符（例如 `order by`），因此多次运行此查询可能会为每个请求生成一组不同的资源。

1. 将最后一个参数更改为 `argQuery.rb`，并将查询更改为按 Name 属性应用 `order by`：

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > 与第一个查询一样，多次运行此查询可能会为每个请求生成一组不同的资源。 查询命令的顺序非常重要。 在本例中，`order by` 位于 `limit` 之后。 命令按此顺序执行，首先会限制查询结果，然后对它们进行排序。

1. 将最后一个参数更改为 `argQuery.rb`，并将查询更改为先按 Name 属性应用 `order by`，然后对前五个结果应用 `limit`：

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

假设环境中没有任何变化，则多次运行最后一个查询时，返回的结果将是一致的且按 Name 属性排序，但仍限制为前五个结果。

## <a name="clean-up-resources"></a>清理资源

如果希望从 Ruby 环境中删除已安装的 gem，可使用以下命令执行此操作：

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> Gem `azure_mgmt_resourcegraph` 具有 `ms_rest` 和 `ms_rest_azure` 等依赖项，根据你的环境，这些依赖项也可能已安装。 如果不再需要这些 gem，还可以将它们卸载。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何将 Resource Graph gem 添加到 Ruby 环境并运行第一个查询。 若要详细了解 Resource Graph 语言，请继续阅读查询语言详细信息页。

> [!div class="nextstepaction"]
> [获取有关查询语言的详细信息](./concepts/query-language.md)
