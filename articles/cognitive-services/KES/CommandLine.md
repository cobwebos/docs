---
title: 知识探索服务命令行接口 | Microsoft Docs
description: 使用 KES 命令行接口从结构化数据生成索引和语法文件，并将其部署为 Microsoft 认知服务中的 Web 服务。
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 71a6f5ac93e5605182a55de1bae9a99c5c3eddf4
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136349"
---
# <a name="command-line-interface"></a>命令行接口
KES 命令行接口能够从结构化数据生成索引和语法文件，并将其部署为 Web 服务。  它用的是常规语法：`kes.exe <command> <required_args> [<optional_args>]`。  可运行没有参数的 `kes.exe` 以显示命令列表，或运行 `kes.exe <command>` 显示可用于指定命令的参数列表。  下面是可用命令列表：
* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="buildindex-command"></a>build_index 命令

build_index 命令可从架构定义文件和要编制索引对象的数据文件生成二进制索引文件。  可以使用生成的索引文件评估结构化的查询表达式，或者与已编译的语法文件结合使用生成自然语言查询的解释。

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| 参数      | Description               |
|----------------|---------------------------|
| `<schemaFile>` | 输入架构路径 |
| `<dataFile>`   | 输入数据路径   |
| `<indexFile>`  | 输出索引路径 |
| `--description <description>` | 说明字符串 |
| `--remote <vmSize>`           | 远程生成 VM 的大小 |

可通过 Azure blob 的本地文件路径或 URL 路径指定这些文件。  架构文件介绍要编制索引的对象的结构以及要支持的操作（请参阅[架构格式](SchemaFormat.md)）。  数据文件将枚举要编制索引的对象和属性值（请参阅[数据格式](DataFormat.md)）。  当生成成功时，输出索引文件将包含支持所需操作的输入数据的压缩表示形式。  

可使用 describe_index 命令选择指定说明字符串，用来随后标识二进制索引。  

默认情况下，在本地计算机上生成索引。  在 Azure 环境外部，本地生成被限制为最多包含 10,000 个对象的数据文件。  指定 --remote 标记时，将在临时创建的具有指定大小的 VM 上生成索引。  这样便可使用具有更多内存的 Azure VM 有效生成大型索引。  若要避免减缓生成过程的分页，建议将 3 倍 RAM 大小的 VM 作为输入数据文件大小。  有关可用的 VM 大小列表，请参阅[虚拟机大小](../../../articles/virtual-machines/virtual-machines-windows-sizes.md)。

> [!TIP] 
> 为了更快生成，可通过降低概率来预先分类数据文件中的对象。

<a name="build_grammar-command"></a>

## <a name="buildgrammar-command"></a>build_grammar 命令

build_grammar 命令可将指定的 XML 格式的语法编译为二进制语法文件。  可将生成的语法文件与索引文件结合使用，以生成自然语言查询的解释。

`kes.exe build_grammar <xmlFile> <grammarFile>`

| 参数       | Description               |
|-----------------|---------------------------|
| `<xmlFile>`     | 输入 XML 语法规范路径 |
| `<grammarFile>` | 输出已编译语法路径         |

可通过 Azure blob 的本地文件路径或 URL 路径指定这些文件。  语法规范描述加权自然语言表达式及其语义解释集（请参阅[语法格式](GrammarFormat.md)）。  如果生成成功，输出语法文件将包含语法规范的二进制表示形式，以支持快速解码。

<a name="host_service-command"/>

## <a name="hostservice-command"></a>host_service 命令

host_service 命令在本地计算机上承载 KES 服务实例。

`kes.exe host_service <grammarFile> <indexFile> [options]`

| 参数       | Description                |
|-----------------|----------------------------|
| `<grammarFile>` | 输入二进制语法路径         |
| `<indexFile>`   | 输入二进制索引路径           |
| `--port <port>` | 本地端口号。  默认值：8000 |

可通过 Azure blob 的本地文件路径或 URL 路径指定这些文件。  Web 服务将承载于 http://localhost:&lt;port&gt;/。  请参阅 [Web API](WebAPI.md)，了解受支持操作的列表。

在 Azure 环境外，本地托管服务被限制为大小最大 1 MB 的索引文件、每秒 10 个请求和 1000 个总调用数。  若要消除这些限制，请在 Azure VM 中运行 host_service，或者使用 deploy_service 将其部署到 Azure 云服务。

<a name="deploy_service-command"/>

## <a name="deployservice-command"></a>deploy_service 命令

deploy_service 命令可将 KES 服务实例部署到 Azure 云服务。

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| 参数       | Description                  |
|-----------------|------------------------------|
| `<grammarFile>` | 输入二进制语法路径           |
| `<indexFile>`   | 输入二进制索引路径             |
| `<serviceName>` | 目标云服务的名称 |
| `<vmSize>`      | 云服务 VM 大小     |
| `--slot <slot>` | 云服务槽位：“过渡”（默认）、“生产” |

可通过 Azure blob 的本地文件路径或 URL 路径指定这些文件。  服务名称可指定预配置的 Azure 云服务（请参阅[如何创建和部署云服务](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)）。  该命令将使用指定大小的 VM 将 KES 服务自动部署到指定的 Azure 云服务。  若要避免导致性能显著下降的分页操作，建议使用比输入文件大小多 1 GB RAM 的 VM。  有关可用的 VM 大小列表，请参阅[云服务大小](../../../articles/cloud-services/cloud-services-sizes-specs.md)。

默认情况下，服务将部署到过渡环境中，可选择通过 --slot 参数重写。  请参阅 [Web API](WebAPI.md)，了解受支持操作的列表。

<a name="describe_index-command"/>

## <a name="describeindex-command"></a>describe_index 命令

describe_index 命令可输出有关索引文件的信息（包括架构和说明）。

`kes.exe describe_index <indexFile>`

| 参数     | Description      |
|---------------|------------------|
| `<indexFile>` | 输入索引路径 |

可通过 Azure blob 的本地文件路径或 URL 路径指定此文件。  可以使用 build_index 命令的 --description 参数指定输出描述字符串。

<a name="describe_grammar-command"/>

## <a name="describegrammar-command"></a>describe_grammar 命令

describe_grammar 命令输出用来生成二进制语法的原始语法规范。

`kes.exe describe_grammar <grammarFile>`

| 参数       | Description      |
|-----------------|------------------|
| `<grammarFile>` | 输入语法路径 |

可通过 Azure blob 的本地文件路径或 URL 路径指定此文件。

