---
title: 快速入门：你的第一个 Go 查询
description: 本快速入门介绍为 Go 启用 Resource Graph 包并运行第一个查询的步骤。
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: 748f6bfa673a2e9fabdcba0c91dc314931df268a
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057444"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>快速入门：使用 Go 运行你的第一个 Resource Graph 查询

使用 Azure Resource Graph 的第一步是确保为 Go 安装了所需的包。 本快速入门将指导你完成将该包添加到 Go 安装的过程。

在此过程结束时，你已将该包添加到 Go 安装中，并将运行你的第一个 Resource Graph 查询。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="add-the-resource-graph-package"></a>添加 Resource Graph 包

若要使 Go 能够查询 Azure Resource Graph，则必须添加该包。 此包适用于可使用 Go 的任何情况，包括 [Windows 10 上的 bash](/windows/wsl/install-win10) 或本地安装的 bash。

1. 请确保安装最新的 Go（至少为 1.14）。 如果尚未安装，请在 [Golang.org](https://golang.org/dl/) 下载。

1. 请确保安装最新的 Azure CLI（至少为 2.5.1）。 如果尚未安装，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

   > [!NOTE]
   > 在以下示例中，需要 Azure CLI 来启用 Go 以使用 `auth.NewAuthorizerFromCLI()` 方法。 有关其他选项的信息，请参阅 [Azure SDK for Go - 更多身份验证详细信息](https://github.com/Azure/azure-sdk-for-go#more-authentication-details)。

1. 通过 Azure CLI 进行身份验证。

   ```azurecli
   az login
   ```

1. 在所选的 Go 环境中，安装 Azure Resource Graph 所需的包：

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>运行首个 Resource Graph 查询

将 Go 包添加到所选环境中后，即可尝试一个简单的 Resource Graph 查询。 该查询返回前五个 Azure 资源，以及每个资源的名称和资源类型 。

1. 创建 Go 应用程序并将以下源保存为 `argQuery.go`：

   ```Go
   package main
   
   import (
       "fmt"
       "os"
       "context"
       "strconv"
       arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
       "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
      // Get variables from command line arguments
      var query = os.Args[1]
      var subList = os.Args[2:]
   
      // Create and authorize a ResourceGraph client
      argClient := arg.New()
      authorizer, err := auth.NewAuthorizerFromCLI()
      if err == nil {
          argClient.Authorizer = authorizer
      } else {
          fmt.Printf(err.Error())
      }
   
      // Set options
      RequestOptions := arg.QueryRequestOptions {
          ResultFormat: "objectArray",
      }
   
      // Create the query request
      Request := arg.QueryRequest {
          Subscriptions: &subList,
          Query: &query,
          Options: &RequestOptions,
      }
   
      // Run the query and get the results
      var results, queryErr = argClient.Resources(context.Background(), Request)
      if queryErr == nil {
          fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
          fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
      } else {
          fmt.Printf(queryErr.Error())
      }
   }
   ```

1. 构建 Go 应用程序：

   ```bash
   go build argQuery.go
   ```

1. 使用已编译的 Go 应用程序运行首个 Azure Resource Graph 查询。 将 `<SubID>` 替换为订阅 ID：

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > 由于此查询示例未提供排序修饰符（例如 `order by`），因此多次运行此查询可能会为每个请求生成一组不同的资源。

1. 将第一个参数更改为 `argQuery`，并将查询更改为 `order by` Name 属性。 将 `<SubID>` 替换为订阅 ID：

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > 与第一个查询一样，多次运行此查询可能会为每个请求生成一组不同的资源。 查询命令的顺序非常重要。 在本例中，`order by` 位于 `limit` 之后。 命令按此顺序执行，首先会限制查询结果，然后对它们进行排序。

1. 将第一个参数更改为 `argQuery`，并将查询更改为先 `order by` Name 属性，然后对前五个结果应用 `limit`。 将 `<SubID>` 替换为订阅 ID：

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

假设环境中没有任何变化，则多次运行最后一个查询时，返回的结果将是一致的且按 Name 属性排序，但仍限制为前五个结果。

## <a name="clean-up-resources"></a>清理资源

如果希望从 Go 环境中删除已安装的包，可使用以下命令执行此操作：

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何将 Resource Graph 包添加到 Go 环境并运行第一个查询。 若要详细了解 Resource Graph 语言，请继续阅读查询语言详细信息页。

> [!div class="nextstepaction"]
> [获取有关查询语言的详细信息](./concepts/query-language.md)