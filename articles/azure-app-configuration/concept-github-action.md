---
title: 将 GitHub 存储库同步到应用配置
description: 使用 GitHub 操作在更新 GitHub 存储库时自动更新应用配置实例。
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 602ccddf97938022df3c5903b573608558fe5d35
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585479"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>将 GitHub 存储库同步到应用配置

希望继续使用其现有源代码管理实践的团队可以使用 GitHub 操作自动将其 GitHub 存储库与其应用配置存储同步。 这允许您像通常一样对配置文件进行更改，同时获得应用配置优势，例如： <br>
&nbsp;&nbsp;&nbsp;&nbsp;• 代码之外的集中配置 <br>
&nbsp;&nbsp;&nbsp;&nbsp;• 更新配置，而无需重新部署整个应用 <br>
&nbsp;&nbsp;&nbsp;&nbsp;• 与 Azure 应用服务和功能等服务集成。 

GitHub 操作[工作流](https://help.github.com/articles/about-github-actions#workflow)定义 GitHub 存储库中的自动化进程。 对源存储库进行更改时 *，Azure 应用配置同步*操作会触发对应用配置实例的更新。 它使用在存储库路径中找到的`/.github/workflows/`YAML （.yml） 文件来定义步骤和参数。 在推送、查看或分支应用配置文件时，可以触发配置更新，就像使用应用代码一样。

GitHub[文档](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow)提供 GitHub 工作流和操作的深入视图。 

## <a name="enable-github-actions-in-your-repository"></a>在存储库中启用 GitHub 操作
要开始使用此 GitHub 操作，请转到存储库并选择"**操作"** 选项卡。单击 **"新建工作流**"，然后**自己设置工作流**。 最后，在市场搜索"Azure 应用配置同步"。
> [!div class="mx-imgBorder"]
> ![选择"操作"选项卡](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![选择应用配置同步操作](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>推送后同步配置文件
此操作在将更改推送到`appsettings.json`时同步 Azure 应用配置文件。 当开发人员将更改推送到`appsettings.json`时，应用配置同步操作使用新值更新应用配置实例。

此工作流的第一部分指定`appsettings.json`操作在包含*为主分支的**推送**上*触发。 第二部分列出了触发操作后运行的作业。 该操作检查相关文件并使用存储库中作为机密存储的连接字符串更新应用配置实例。  有关在 GitHub 中使用机密的详细信息，请参阅[GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets)有关创建和使用加密机密的文章。

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
每当更新时`appsettings.json`，前面的操作都会更新应用配置实例。 此操作在每个同步上插入一个动态标签，确保可以唯一标识每个同步，并允许将代码更改映射到配置更改。

此工作流的第一部分指定`appsettings.json`操作在包含*为主分支的**推送**上*触发。 第二部分运行一个作业，该作业基于提交哈希为配置更新创建唯一标签。 然后，作业使用新值和此更新的唯一标签更新应用配置实例。

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

## <a name="use-strict-sync"></a>使用严格的同步
启用严格模式后，同步可确保应用配置实例与给定前缀和标签的配置文件完全匹配。 删除配置文件中未具有相同前缀和标签的键值对。 
 
如果未启用严格模式，则同步将仅设置配置文件中的键值。 不会删除任何键值对。 

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
嵌套 JSON 属性的默认行为是拼平整个对象。  下面的 JSON 定义此键值对：

| 键 | 值 |
| --- | --- |
| 对象：内部键：内键 | 内部价值 |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

如果嵌套对象旨在将值推送到配置实例，则可以使用*深度*值在适当的深度停止展平。 

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

给定深度为 2，上述示例现在返回以下键值对：

| 键 | 值 |
| --- | --- |
| 对象：内部 | {"内键"："内部价值"} |

## <a name="understand-action-inputs"></a>了解操作输入
输入参数指定操作在运行时使用的数据。  下表包含应用配置同步接受的输入参数和每个参数的预期值。  有关 GitHub 操作的操作输入的详细信息，请参阅 GitHub[的文档](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)。

> [!Note]
> 输入指示号不区分大小写。


| 输入名称 | 必需？ | 值 |
|----|----|----|
| 配置文件 | 是 | 存储库中配置文件的相对路径。  Glob 模式受支持，可以包含多个文件。 |
| format | 是 | 配置文件的文件格式。  有效格式包括：JSON、YAML、属性。 |
| connectionString | 是 | 应用配置实例的连接字符串。 连接字符串应作为机密存储在 GitHub 存储库中，并且在工作流中应仅使用机密名称。 |
| separator | 是 | 将配置文件拼平到键值对时使用的分隔符。  有效值为： 。 , ; : - _ __ / |
| 前缀 | 否 | 要添加到键开头的前缀。 |
| label | 否 | 设置键值对时使用的标签。 如果未指定，则使用空标签。 |
| strict | 否 | 确定是否启用严格模式的布尔值。 默认值是 False。 |
| 深度 | 否 | 用于拼平配置文件的最大深度。  深度必须为正数。  默认值将没有最大深度。 |
| 标记 | 否 | 指定在键值对上设置的标记。  预期格式是以下形状的 JSON 对象的字符串化形式： { 属性名称： 字符串"：字符串;每个属性名称值都将成为标记。 |

## <a name="next-steps"></a>后续步骤

在本文中，您了解了应用配置同步 GitHub 操作及其如何使用它来自动更新应用配置实例。 要了解 Azure 应用配置对键值对中的更改有何反应，请继续执行下一[篇文章](./concept-app-configuration-event.md)。
