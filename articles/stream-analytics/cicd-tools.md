---
title: 使用 CI/CD 工具自动执行 Azure 流分析作业的生成、测试和部署
description: 本文介绍如何使用 Azure 流分析 CI/CD 工具自动生成、测试和部署 Azure 流分析项目。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: e772701396f172eaab906f99463bd9019728b531
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933509"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>使用 CI/CD 工具自动执行 Azure 流分析作业的生成、测试和部署

可以使用 Azure 流分析 CI/CD npm 包自动生成、测试和部署 Azure 流分析 Visual Studio Code 或 Visual Studio 项目。 可以使用开发工具创建项目，也可以从现有的流分析作业中导出这些项目。 本文介绍了如何将 npm 包与任何 CI/CD 系统一起使用。 有关 Azure Pipelines 的部署，请参阅 [使用 Azure DevOps 为流分析作业创建 CI/CD 管道](set-up-cicd-pipeline.md)。

## <a name="installation"></a>安装

可以直接 [下载包](https://www.npmjs.com/package/azure-streamanalytics-cicd) ，也可以使用命令 [全局](https://docs.npmjs.com/downloading-and-installing-packages-globally) 安装包 `npm install -g azure-streamanalytics-cicd` 。 建议使用命令，该命令还可以在 **Azure Pipelines**的生成管道的 PowerShell 或 Azure CLI 脚本任务中使用。

## <a name="build-the-project"></a>生成项目

**Streamanalytics-default-central-us-cicd** npm 包提供了用于生成流分析[Visual Studio Code 项目](quick-create-vs-code.md)或[Visual Studio 项目](stream-analytics-quick-create-vs.md)的 Azure 资源管理器模板的工具。 你还可以在 Windows、macOS 和 Linux 上使用 npm 包，而无需安装 Visual Studio Code 或 Visual Studio。

安装程序包后，请使用以下命令生成流分析项目。

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

" *生成* " 命令执行关键字语法检查并输出 Azure 资源管理器模板。

| 参数 | 说明 |
|---|---|
| `-project` | Visual Studio Code 项目的文件的绝对 **asaproj.js** 路径，或 **[项目名称]. Asaproj** for Visual Studio 项目。 |
| `-outputPath` | Azure 资源管理器模板的输出文件夹的路径。 如果未指定此项，则将模板放在当前目录中。 |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

当流分析项目成功生成时，它会在 output 文件夹下生成以下两个文件：

* Azure 资源管理器模板文件

   `[ProjectName].JobTemplate.json`

* Azure 资源管理器参数文件

   `[ProjectName].JobTemplate.parameters.json`

文件 parameters.js上的默认参数来自 Visual Studio Code 或 Visual Studio 项目中的设置。 如果要部署到其他环境，请相应地替换参数。

所有凭据的默认值均为 **null**。 在部署到 Azure 之前，需要设置这些值。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

若要将托管标识用于 Azure Data Lake Store Gen1 作为输出接收器，需要在部署到 Azure 之前，使用 PowerShell 提供对服务主体的访问权限。 了解有关如何[使用资源管理器模板部署具有托管标识的 ADLS Gen1](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment) 的详细信息。

## <a name="local-run"></a>本地运行

如果你的项目已指定本地输入文件，则可使用命令在本地运行流分析脚本 `localrun` 。

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| 参数 | 说明 |
|---|---|
| `-project` | Visual Studio Code 项目的 **asaproj.js** 文件路径，或 **[项目名称]. Asaproj** for Visual Studio 项目。 |
| `-outputPath` | 输出文件夹的路径。 如果未指定，则输出结果文件将放在当前目录中。 |
| `-customCodeZipFilePath` | C # 自定义代码（如 UDF 或反序列化程序）的 zip 文件的路径（如果使用）。 将 Dll 打包到一个 zip 文件中，并指定此路径。 |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> JavaScript UDF 仅适用于 Windows。

## <a name="automated-test"></a>自动测试

可以使用 CI/CD npm 包来配置和运行流分析脚本的自动测试。

### <a name="add-a-test-case"></a>添加测试用例

测试用例在测试配置文件中进行了介绍。 若要开始，请使用 `addtestcase` 命令将测试用例模板添加到测试配置文件中。 如果测试配置文件不存在，则默认情况下会创建一个。

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| 参数 | 说明 |
|---|---|
| `-project` | Visual Studio Code 项目的 **asaproj.js** 文件路径，或 **[项目名称]. Asaproj** for Visual Studio 项目。 |
| `-testConfigPath` | 测试配置文件的路径。 如果未指定此值，则会在 **\test** 文件的当前 **asaproj.js** 目录下的文件中搜索文件，默认文件名 **testConfig.js为 on**。 如果不存在，则将创建新的文件。 |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

如果测试配置文件为空，则会将以下内容写入文件中。 否则，会将一个测试用例添加到 **测试用例**的数组中。 必要的输入配置会根据输入配置文件（如果存在）自动填充。 否则，将配置默认值。 在运行测试之前，必须指定每个输入和预期输出的**FilePath** 。 你可以手动修改配置。

如果希望测试验证忽略特定输出，请将预期输出的 **必填** 字段设置为 **false**。

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": "Required",
          "Required": true
        }
      ]
    }
  ]
}
```

### <a name="run-a-unit-test"></a>运行单元测试

可以使用以下命令为项目运行多个测试用例。 在输出文件夹中生成测试结果的摘要。 对于所有已传递的测试，进程退出，代码为 **0** ;如果发生异常，则为 **-1** ; **-2** 个测试失败。

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| 参数 | 说明 |
|---|---|
| `-project` | Visual Studio Code 项目的 **asaproj.js** 文件路径，或 **[项目名称]. Asaproj** for Visual Studio 项目。 |
| `-testConfigPath` | 测试配置文件的路径。 如果未指定此值，则会在 **\test** 文件的当前 **asaproj.js** 目录下的文件中搜索文件，默认文件名 **testConfig.js为 on**。
| `-outputPath` | 测试结果输出文件夹的路径。 如果未指定，则输出结果文件将放在当前目录中。 |
| `-customCodeZipFilePath` | 自定义代码（如 UDF 或反序列化程序）的 zip 文件的路径（如果使用）。 |

完成所有测试后，将在输出文件夹中生成 JSON 格式的测试结果的摘要。 摘要文件的名称为 **testResultSummary.js**。

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>“部署到 Azure”

可以使用 "生成" 中生成的 Azure 资源管理器模板和参数文件将 [作业部署到 Azure](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析持续集成和持续部署](cicd-overview.md)
* [使用 Azure Pipelines 为流分析作业设置 CI/CD 管道](set-up-cicd-pipeline.md)
