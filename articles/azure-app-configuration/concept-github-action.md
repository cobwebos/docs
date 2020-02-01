---
title: 在 Azure 应用配置同步中使用 GitHub 操作
description: 使用 GitHub 操作可在 GitHub 存储库上执行定义的操作时触发对应用配置实例的更新
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/14/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 269ae5630d1524cb8f89d3af8728892079f6eb5f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899621"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>使用 GitHub 操作同步应用配置实例
Azure 应用配置使用 GitHub 操作来更新应用配置实例，该实例由对 GitHub 存储库执行的操作触发。 你可以利用 GitHub 工作流来更新应用配置，使应用配置更新与用于更新应用程序代码的工作流集成。

GitHub 操作[工作流](https://help.github.com/articles/about-github-actions#workflow)是在 GitHub 存储库中定义的自动化过程。 此过程告知 GitHub 如何生成和部署 GitHub 项目。 Azure 应用配置提供*Azure 应用配置同步*操作，以便在对源存储库进行更改时启用对应用配置实例的更新。 

工作流是由存储库的 `/.github/workflows/` 路径中的 YAML （docker-compose.override.yml）文件定义的。 此定义包含定义工作流的各种步骤和参数。

GitHub 事件（例如推送到存储库）可以触发 GitHub 操作工作流。  Azure 提供*Azure 应用配置同步*操作，使你能够在指定的 GitHub 操作发生时触发应用配置实例的更新。 这样，团队就可以在推送、查看或分支应用配置文件时利用 GitHub 的核心功能，就像它们处理应用代码一样。

GitHub[文档](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow)提供 github 工作流和操作的深入视图。 

## <a name="enable-github-actions-in-your-repository"></a>在存储库中启用 GitHub 操作
若要开始使用此 GitHub 操作，请在存储库中选择 "**操作**" 选项卡。在 marketplace 中搜索 "Azure 应用配置同步"，找到并选择 GitHub 操作。 

> [!div class="mx-imgBorder"]
> ![选择 "操作" 选项卡](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![选择 "应用配置" "syn 操作"](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>推送后同步配置文件
将更改推送到 `appsettings.json`时，此操作将同步 Azure 应用配置文件。 当开发人员进行更改并将更改推送到 `appsettings.json`时，应用配置同步操作会使用新值更新应用配置实例。

此工作流的第一部分指定操作在包含 `appsettings.json` 到*主*分支的*推送* *上*触发。 第二部分列出了在触发操作后运行的作业。 操作将签出相关文件并使用存储为存储库中的机密的连接字符串更新应用配置实例。  有关在 Github 中使用机密的详细信息，请参阅[此文](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets)，了解如何创建和使用加密的机密。

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-a-dynamic-label-on-sync"></a>在同步时使用动态标签
当 `appsettings.json` 更新时，上一操作只更新应用配置实例。 此操作在每次同步时插入一个动态标签，确保每个同步都可以唯一地标识。  这允许代码更改快速映射到配置更改。

此工作流的第一部分指定操作在包含 `appsettings.json` 到*主*分支的*推送* *上*触发。 第二部分运行一个作业，该作业将根据提交哈希为配置更新创建唯一的标签。 然后，该作业将更新应用配置实例，其中包含此更新的新值和唯一标签。

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-strict-sync"></a>使用严格同步
当启用严格模式时，同步确保应用配置实例与给定前缀和标签的配置文件完全匹配。 不在配置文件中的前缀和标签相同的键/值对将被删除。 
 
如果未启用 strict 模式，则同步仅会从配置文件设置键值。 不会删除任何键值对。 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```

## <a name="use-max-depth-to-limit-github-action"></a>使用最大深度限制 GitHub 操作
嵌套的 JSON 特性的默认行为是对整个对象进行平展。  下面的 JSON 定义此键值对：

| 密钥 | 值 |
| --- | --- |
| 对象：内部： InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```
如果要将嵌套的对象作为推送到配置实例的值，则可以使用*深度*值以适当的深度停止平展。 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

如果深度为2，则上面的示例将返回以下键：值对：

| 密钥 | 值 |
| --- | --- |
| 对象：内部 | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>了解操作输入
输入参数指定操作在运行时使用的数据。  下表包含应用配置同步接受的输入参数以及每个参数所需的值。  有关 GitHub 操作的操作输入的详细信息，请参阅 GitHub 的[文档](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)。

> [!Note]
> 输入 Id 不区分大小写。


| 输入名称 | 必需？ | 值 |
|----|----|----|
| Configurationfile.ini | 是 | 指向存储库中的配置文件的路径（相对于存储库的根目录）。  支持 Glob 模式，并且可以包含多个文件。 |
| format | 是 | 配置文件的文件格式。  有效的格式为： JSON、YAML、properties。 |
| connectionString | 是 | 应用配置实例的连接字符串。 连接字符串应作为机密存储在 GitHub 存储库中，并且仅应在工作流中使用机密名称。 |
| separator | 是 | 将配置文件平展到键值对时使用的分隔符。  有效值为：。 , ;: - _ __ / |
| 前缀 | 否 | 要添加到键的开头的前缀。 |
| label | 否 | 设置键值对时使用的标签。 如果未指定，则使用 null 标签。 |
| strict | 否 | 确定是否已启用严格模式的布尔值。 默认值是 False。 |
| 长度 | 否 | 平展配置文件的最大深度。  深度必须是正数。  默认值不具有最大深度。 |
| 标记 | 否 | 指定对键值对设置的标记。  预期格式是以下形状的 JSON 对象的字符串化形式： {[propertyName： string]： string;}每个属性名称-值变成一个标记。 |

## <a name="next-steps"></a>后续步骤

本文介绍了应用配置同步 GitHub 操作，以及如何使用它来自动更新应用配置实例。 若要了解 Azure 应用配置如何对键值对中的更改做出反应，请继续学习下一篇[文章](./concept-app-configuration-event.md)。
