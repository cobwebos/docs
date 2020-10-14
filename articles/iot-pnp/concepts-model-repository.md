---
title: 了解设备模型存储库的概念 |Microsoft Docs
description: 作为解决方案开发人员或 IT 专业人员，请了解有关设备模型存储库的基本概念。
author: rido-min
ms.author: rmpablos
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cfdf22ac9b97ff7187bc360efe07cfe16249bd6b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042875"
---
# <a name="device-model-repository"></a>设备模型存储库

设备模型存储库 (DMR) 使设备构建者能够管理和共享 IoT 即插即用设备型号。 设备模型是使用 [数字孪生建模语言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)定义的 JSON LD 文档。

DMR 定义了一个模式，用于根据设备克隆模型标识符 (DTMI) 在文件夹结构中存储 DTDL 接口。 可以通过将 DTMI 转换为相对路径，在 DMR 中查找接口。 例如， `dtmi:com:example:Thermostat;1` DTMI 转换为 `/dtmi/com/example/thermostat-1.json` 。

## <a name="public-device-model-repository"></a>公用设备模型存储库

Microsoft 托管了具有以下特征的公共 DMR：

- 特选模型。 Microsoft 使用开放 GitHub PR 验证工作流查看并批准所有可用的接口。
- 不可变性.  发布后，无法更新接口。
- 超大规模。 Microsoft 提供了创建安全且高度可缩放的终结点所需的所有基础结构。

## <a name="custom-device-model-repository"></a>自定义设备模型存储库

可以在任何存储介质（如本地文件系统或自定义 HTTP web 服务器）中使用相同的 DMR 模式来创建自定义 DMR。 只需更改用于访问 DMR 的基 URL，就可以像从公共 DRM 检索模型。

> [!NOTE]
> 用于验证公共 DMR 中的模型的工具可在自定义存储库中重复使用。

## <a name="public-models"></a>公共模型

存储在模型存储库中的公共数字克隆模型可供每个人在其应用程序中使用和集成。 公共模型允许设备制造商和解决方案开发人员开放的 eco 系统共享和重复使用其 IoT 即插即用设备型号。

有关如何在模型存储库中发布模型以使其公开的说明，请参阅 [发布模型](#publish-a-model) 部分。

用户可以浏览、搜索和查看官方 [GitHub 存储库](https://github.com/Azure/iot-plugandplay-models)中的公共接口。

`dtmi`还可以通过公共终结点使用文件夹中的所有接口[https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>解析模型

若要以编程方式访问这些接口，需要将 dtmi 转换为可用于查询公共终结点的相对路径。 下面的代码示例演示如何执行此操作：

若要将 DTMI 转换为绝对路径，我们将使用 `DtmiToPath` 函数，使用 `IsValidDtmi` ：

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

对于存储库的结果路径和基 URL，我们可以获取接口：

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>发布模型

> [!Important]
> 您必须具有 GitHub 帐户才能向公用 DMR 提交模型。

1. 分叉公共 GitHub 存储库： [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) 。
1. 克隆分叉的存储库。 （可选）创建一个新分支，使更改与 `main` 分支隔离。
1. `dtmi`使用文件夹/文件名约定将新接口添加到文件夹。 请参阅 [添加模型](#add-model) 工具。
1. 使用 [脚本验证更改](#validate-files) 部分本地验证模型。
1. 在本地提交更改并推送到你的分支。
1. 从分叉创建一个目标为分支的 PR `main` 。 请参阅 [创建问题或](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) 请求文档。
1. 查看 [PR 要求](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)。

PR 触发一系列 GitHub 操作，这些操作将验证新提交的接口，并确保你的 PR 满足所有检查。

Microsoft 将在三个工作日内通过所有检查来响应 PR。

### <a name="add-model"></a>添加模型

以下步骤演示了 add-model.js 脚本如何帮助您添加新的接口。 此脚本需要运行 Node.js：

1. 在命令提示符下，导航到本地 git 存储库
1. `npm install`运行
1. `npm run add-model <path-to-file-to-add>`运行

在控制台输出中查看任何错误消息。

### <a name="local-validation"></a>本地验证

你可以在提交 PR 之前本地运行相同的验证检查，以帮助提前诊断问题。

#### <a name="validate-files"></a>验证-文件

`npm run validate-files <file1.json> <file2.json>` 检查文件路径是否与预期的文件夹和文件名匹配。

#### <a name="validate-ids"></a>验证 id

`npm run validate-ids <file1.json> <file2.json>` 检查文档中定义的所有 Id 是否使用与主 ID 相同的根。

#### <a name="validate-deps"></a>验证-.deps.json

`npm run validate-deps <file1.json> <file2.json>` 检查文件夹中是否有所有依赖项 `dtmi` 。

#### <a name="validate-models"></a>验证-模型

您可以运行 [DTDL 验证示例](https://github.com/Azure-Samples/DTDL-Validator) 以在本地验证模型。

## <a name="next-steps"></a>后续步骤

建议的下一步是查看 [IoT 即插即用的体系结构](concepts-architecture.md)。