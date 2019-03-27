---
title: 使用 Terratest 在 Azure 中测试 Terraform 模块
description: 了解如何使用 Terratest 来测试 Terraform 模块。
services: terraform
ms.service: azure
keywords: terraform, devops, 存储帐户, azure, terratest, 单元测试, 集成测试
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 03/19/2019
ms.openlocfilehash: 9d621905122ab7bf64432323d7d11cf8f1b50750
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223999"
---
# <a name="test-terraform-modules-in-azure-by-using-terratest"></a>使用 Terratest 在 Azure 中测试 Terraform 模块

> [!NOTE]
> 本文中的示例代码不适用于版本 0.12（及更高版本）。

可以使用 Azure Terraform 模块创建可重用、可组合且可测试的组件。 Terraform 模块整合了封装，后者用于实现基础结构即代码过程。

在创建 Terraform 模块时，必须实施质量保证。 遗憾的是，目前介绍如何在 Terraform 模块中创作单元测试和集成测试的文档有限。 本教程介绍我们在生成 [Azure Terraform 模块](https://registry.terraform.io/browse?provider=azurerm)时采用的测试性基础结构和最佳做法。

我们查看了所有最常用的测试性基础结构，选择了 [Terratest](https://github.com/gruntwork-io/terratest) 来测试 Terraform 模块。 Terratest 以 Go 库的形式实现。 Terratest 提供一系列适用于常见基础结构测试任务的帮助程序函数和模式，例如向特定的虚拟机发出 HTTP 请求以及通过 SSH 访问该虚拟机。 以下列表介绍使用 Terratest 时的一些主要优势：

- **它提供便于检查基础结构的帮助程序**。 需要在实际环境中验证实际基础结构时，此功能很有用。
- **文件夹结构组织有序**。 测试用例组织有序，并使用[标准的 Terraform 模块文件夹结构](https://www.terraform.io/docs/modules/create.html#standard-module-structure)。
- **所有测试用例以 Go 编写**。 大多数使用 Terraform 的开发人员是 Go 开发人员。 如果是 Go 开发人员，则不需要再学习一种编程语言就可以使用 Terratest。 另外，在 Terratest 中运行测试用例时，只需依赖 Go 和 Terraform。
- **基础结构高度可扩展**。 可以在 Terratest 基础上扩展其他功能，包括特定于 Azure 的功能。

## <a name="prerequisites"></a>先决条件

此动手指南文章与平台无关。 可以在 Windows、Linux 或 MacOS 上运行本文中使用的代码示例。 

在开始之前，请安装以下软件：

- **Go 编程语言**：Terraform 测试用例以 [Go](https://golang.org/dl/) 编写。
- **dep**：[dep](https://github.com/golang/dep#installation) 是适用于 Go 的依赖项管理工具。
- **Azure CLI**：[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 是可用于管理 Azure 资源的命令行工具。 （Terraform 支持通过服务主体或[通过 Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html) 向 Azure 进行身份验证。）
- **mage**：我们使用 [mage 可执行文件](https://github.com/magefile/mage/releases)来演示如何简化 Terratest 用例的运行。 

## <a name="create-a-static-webpage-module"></a>创建静态网页模块

在本教程中，请创建一个 Terraform 模块来预配静态网页，方法是将单个 HTML 文件上传到 Azure 存储 Blob。 此模块允许全世界的用户通过模块返回的 URL 访问此网页。

> [!NOTE]
> 请在 [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) 位置创建此部分所述的所有文件。

首先，请在 GoPath 的 `src` 文件夹下创建名为 `staticwebpage` 的新文件夹。 本教程的整体文件夹结构如以下示例所示。 标记了星号 `(*)` 的文件是此部分的重点。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

静态网页模块接受三种输入。 这些输入在 `./variables.tf` 中声明：

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

如本文前面所述，此模块还会输出在 `./outputs.tf` 中声明的 URL：

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

模块的主逻辑预配四项资源：
- **资源组**：资源组的名称是 `-staging-rg` 追加的 `website_name` 输入。
- **存储帐户**：存储帐户的名称是 `data001` 追加的 `website_name` 输入。 为了遵循存储帐户的名称限制，模块会删除所有特殊字符并在整个存储帐户名称中使用小写字母。
- **固定名称容器**：容器名为 `wwwroot`，在存储帐户中创建。
- **单个 HTML 文件**：此 HTML 文件从 `html_path` 输入读取并上传到 `wwwroot/index.html`。

静态网页模块逻辑在 `./main.tf` 中实现：

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>单元测试

Terratest 设计用于集成测试。 因此，Terratest 在实际环境中预配实际资源。 有时候，集成测试作业可能会变得异常大，尤其是在有很多资源需要预配的情况下。 我们在上一部分提到的用于转换存储帐户名称的逻辑就是一个很好的示例。 

但是，我们实际上不需要预配任何资源。 我们只需确保命名转换逻辑正确。 由于 Terratest 十分灵活，因此我们可以使用单元测试。 单元测试是本地的运行测试用例（不过需进行 Internet 访问）。 单元测试用例会执行 `terraform init` 和 `terraform plan` 命令，以便分析 `terraform plan` 的输出并查找要比较的属性值。

此部分的其余内容介绍如何使用 Terratest 来实施单元测试，确保用于转换存储帐户名称的逻辑是正确的。 我们只关注标记有星号 `(*)` 的文件。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

首先，请使用空的 HTML 文件 `./test/fixtures/storage-account-name/empty.html` 作为占位符。

文件 `./test/fixtures/storage-account-name/main.tf` 是测试用例框架。 它接受一个输入 (`website_name`)，这也是单元测试的输入。 逻辑如下所示：

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

主要组件是 `./test/storage_account_name_unit_test.go` 中单元测试的实现。

Go 开发人员可能会注意到，如果接受类型为 `*testing.T` 的参数，单元测试就会与经典 Go 测试函数的签名匹配。

在单元测试的正文中，我们在变量 `testCases` 中总共定义了五个用例（`key` 作为输入，而 `value` 则作为预期的输出）。 对于每个单元测试用例，请先运行 `terraform init`，以测试固定例程文件夹 (`./test/fixtures/storage-account-name/`) 为目标。 

接下来，使用特定测试用例输入（请查看 `tfOptions` 中的 `website_name` 定义）的 `terraform plan` 命令会将结果保存到 `./test/fixtures/storage-account-name/terraform.tfplan`（未在总体文件夹结构中列出）。

可使用正式的 Terraform 计划分析器将此结果文件分析成可供代码读取的结构。

现在，我们将查找感兴趣的属性（在此示例中为 `azurerm_storage_account` 的 `name`），并将结果与预期的输出进行比较：

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

若要运行单元测试，请在命令行中完成以下步骤：

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

传统的 Go 测试结果会在一分钟左右返回。

### <a name="integration-test"></a>集成测试

与单元测试不同，从端到端角度来看，集成测试必须将资源预配到实际环境。 Terratest 适合此类任务。 

Terraform 模块的最佳做法包括安装 `examples` 文件夹。 `examples` 文件夹包含一些端到端示例。 若要避免使用实际数据，为何不以集成测试的方式测试这些示例？ 在此部分，我们关注以下文件夹结构中的三个文件，每个都标记了星号 `(*)`：

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

让我们从这些示例开始。 名为 `hello-world/` 的新示例文件夹已在 `./examples/` 文件夹中创建。 下面提供一个需上传的简单 HTML 页：`./examples/hello-world/index.html`。

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

Terraform 示例 `./examples/hello-world/main.tf` 类似于在单元测试中显示的示例。 有一个显著区别：示例还以名为 `homepage` 的网页的形式输出已上传 HTML 的 URL。

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

我们在集成测试文件 `./test/hello_world_example_test.go` 中再次使用 Terratest 和经典 Go 测试函数。

与单元测试不同，集成测试会在 Azure 中创建实际资源， 因此需小心操作，避免命名冲突。 （请特别注意某些全局唯一名称，例如存储帐户名称。）因此，测试逻辑的第一步是使用 Terratest 提供的 `UniqueId()` 函数生成随机的 `websiteName`。 此函数生成一个包含小写字母、大写字母或数字的随机名称。 `tfOptions` 使所有 Terraform 命令都以 `./examples/hello-world/` 文件夹为目标， 同时还确保将 `website_name` 设置为随机的 `websiteName`。

然后逐一执行 `terraform init`、`terraform apply`、`terraform output`。 我们使用另一帮助程序函数，即 `HttpGetWithCustomValidation()`，该函数由 Terratest 提供。 我们使用帮助程序函数来确保将 HTML 上传到 `terraform output` 返回的输出 `homepage` URL。 我们将 HTTP GET 状态代码与 `200` 进行比较，在 HTML 内容中查找某些关键字。 最后，系统“承诺”利用 Go 的 `defer` 功能来执行 `terraform destroy`。

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

若要运行集成测试，请在命令行中完成以下步骤：

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

传统的 Go 测试结果会在两分钟左右返回。 也可通过执行以下命令来运行单元测试和集成测试：

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

集成测试需要的时间远远超出单元测试（一个集成用例需要两分钟，而五个单元用例只需要一分钟）。 但是，在方案中是使用单元测试还是使用集成测试由你自己决定。 通常情况下，对于使用 Terraform HCL 函数的复杂逻辑，我们首选使用单元测试。 从用户的端到端角度来看，我们通常使用集成测试。

## <a name="use-mage-to-simplify-running-terratest-cases"></a>使用 mage 简化 Terratest 用例的运行 

在 Azure Cloud Shell 中运行测试用例不是一项轻松的任务。 需转到不同的目录并执行不同的命令。 为了避免使用 Cloud Shell，我们在项目中引入生成系统。 在此部分，我们将 Go 生成系统 mage 用于此作业。

mage 需要的唯一项是 `magefile.go`，它位于项目的根目录中（在以下示例中带 `(+)` 标记）：

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

下面是 `./magefile.go` 的示例。 在这个用 Go 编写的生成脚本中，我们实施五个生成步骤：
- `Clean`：此步骤删除在测试执行过程中生成的所有文件和临时文件。
- `Format`：此步骤运行格式化代码库所需的 `terraform fmt` 和 `go fmt`。
- `Unit`：此步骤运行 `./test/` 文件夹下的所有单元测试（使用函数名称约定 `TestUT_*`）。
- `Integration`：此步骤类似于 `Unit`，但执行集成测试 (`TestIT_*`)，而不执行单元测试。
- `Full`：此步骤按顺序运行 `Clean`、`Format`、`Unit`、`Integration`。

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

可以使用以下命令执行完整的测试套件。 此代码类似于在此前的部分使用过的运行步骤。 

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only required when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage$ mage
```

可将最后一个命令行替换为其他 mage 步骤。 例如，可以使用 `mage unit` 或 `mage clean`。 最好是在 magefile 中嵌入 `dep` 命令和 `az login`。 我们不在此处显示代码。 

使用 mage 时，还可以通过 Go 包系统共享这些步骤。 在这种情况下，可以只引用一个共同的实现并声明依赖项 (`mg.Deps()`)，简化所有模块的 magefile。

**可选：设置运行验收测试所需的服务主体环境变量**
 
可以通过设置服务主体环境变量来完成 Azure 身份验证，不必在测试之前执行 `az login`。 Terraform 会发布[环境变量名称列表](https://www.terraform.io/docs/providers/azurerm/index.html#testing)。 （在这些环境变量中，只有头四个是必需的。）Terraform 还会发布详细说明，介绍如何[获取这些环境变量的值](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)。

## <a name="next-steps"></a>后续步骤

* 有关 Terratest 的详细信息，请参阅 [Terratest GitHub 页](https://github.com/gruntwork-io/terratest)。
* 有关 mage 的信息，请参阅 [mage GitHub 页](https://github.com/magefile/mage)和 [mage 网站](https://magefile.org/)。
