---
title: 试验 - Azure 认知服务 | Microsoft Docs
description: 本文是 Azure 自定义 AI 决策服务试验指南。
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b0ac0bc049d556423493f0c48dd9a548929bcd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366585"
---
# <a name="experimentation"></a>试验

根据[上下文 Bandit (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/) 理论，自定义 AI 决策服务重复观察上下文、采取措施并观察所选操作的回报。 内容个性化示例：上下文描述用户，操作是候选情景，回报衡量多少用户喜欢建议的情景。

当从上下文映射到操作时，自定义 AI 决策服务生成一个策略。 对于特定目标策略，你想知道其预期回报。 估计回报的一种方法是联机使用策略，并让其选择操作（例如，向用户建议的情景）。 但是，此类联机评估可能成本高昂，有两个原因：

* 它向用户公开未经测试的试验策略。
* 它不会扩展至对多个目标策略进行评估。

关闭策略评估是备用范例。 如果具有的日志来自遵循日志记录策略的现有联机系统，关闭策略评估可以估计新目标策略的预期回报。

通过使用日志文件，试验将寻求预计具有最高预期回报的策略。 按 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) 参数来参数化目标策略。 在默认模式中，通过追加到 `--base_command` 脚本测试各种 Vowpal Wabbit 参数。 此脚本可执行以下操作：

* 自动检测从输入文件的第一个 `--auto_lines` 行精选命名空间。
* 对超参数（`learning rate`、`L1 regularization` 和 `power_t`）执行第一次扫描。
* 测试策略评估 `--cb_type`（逆倾向评分 (`ips`) 或双向可靠 (`dr`)）。 有关详细信息，请参阅[上下文 Bandit 示例](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example)。
* 测试边际。
* 测试二次交互功能：
   * 蛮力阶段：测试与 `--q_bruteforce_terms` 对或更少的所有组合。
   * 贪婪阶段：添加最佳对，直到 `--q_greedy_stop` 舍入没有任何改进。
* 对超参数（`learning rate`、`L1 regularization` 和 `power_t`）执行第二次扫描。

控制这些步骤的形参包括某些 Vowpal Wabbit 实参：
- 示例操作选项：
  - 共享命名空间
  - 操作命名空间
  - 边际命名空间
  - 二次功能
- 更新规则选项
  - 学习速率
  - L1 正则化
  - t 功率值

有关上述参数的深入说明，请参阅 [Vowpal Wabbit 命令行参数](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)。

## <a name="prerequisites"></a>先决条件
- Vowpal Wabbit：已安装并在你的路径上。
  - Windows：[使用 `.msi` 安装程序](https://github.com/eisber/vowpal_wabbit/releases)。
  - 其他平台：[获取源代码](https://github.com/JohnLangford/vowpal_wabbit/releases)。
- Python 3：已安装并在你的路径上。
- NumPy：使用所选的包管理器。
- Microsoft/mwt-ds 存储库：[克隆存储库](https://github.com/Microsoft/mwt-ds)。
- 决策服务 JSON 日志文件：默认情况下，基本命令包括 `--dsjson`，支持输入数据文件的决策服务 JSON 分析。 [获取此格式的示例](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json)。

## <a name="usage"></a>使用情况
转到 `mwt-ds/DataScience` 并使用相关参数运行 `Experimentation.py`，如以下代码中所述：

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

结果日志追加到 mwt-ds/DataScience/experiments.csv 文件。

### <a name="parameters"></a>parameters
| 输入 | 说明 | 默认 |
| --- | --- | --- |
| `-h`、`--help` | 显示帮助消息并退出。 | |
| `-f FILE_PATH`、`--file_path FILE_PATH` | 数据文件路径（`.json` 或 `.json.gz` 格式- 每行都是 `dsjson`）。 | 必选 |  
| `-b BASE_COMMAND`、`--base_command BASE_COMMAND` | 基本 Vowpal Wabbit 命令。  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`、`--n_proc N_PROC` | 要使用的并行进程数。 | 逻辑处理器 |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | 共享功能命名空间（例如，`abc` 意味着命名空间 `a`、`b` 和`c`）。  | 从数据文件自动检测 |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | 操作功能命名空间。 | 从数据文件自动检测 |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | 边际功能命名空间。 | 从数据文件自动检测 |  
| `--auto_lines AUTO_LINES` | 针对自动检测功能命名空间，要扫描的数据文件的行数。 | `100` |  
| `--only_hp` | 仅扫描超参数（`learning rate`、`L1 regularization` 和 `power_t`）。 | `False` |  
| `-l LR_MIN_MAX_STEPS`、`--lr_min_max_steps LR_MIN_MAX_STEPS` | 以正值 `min,max,steps` 表示的学习速率范围。 | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`、`--reg_min_max_steps REG_MIN_MAX_STEPS` | 以正值 `min,max,steps` 表示的 L1 正则化范围。 | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`、`--pt_min_max_steps PT_MIN_MAX_STEPS` | 以正值 `min,max,step` 表示的 Power_t 范围。 | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | 要在蛮力阶段中测试的二次对数。 | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | 舍入（无需改进），此后暂停二次贪婪搜索阶段。 | `3` |  

### <a name="examples"></a>示例
使用预设默认值：
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

同样，Vowpal Wabbit 也可以引入 `.json.gz` 文件：
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

若要仅扫描超参数（`learning rate`、`L1 regularization` 和 `power_t`，在步骤 2 后停止）：
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
