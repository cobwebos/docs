---
ms.openlocfilehash: dba7a3cc7a68d360fd6e56511b71ae364f624646
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569266"
---
CRON 表达式使用的默认时区为协调世界时 (UTC)。 若要让 CRON 表达式基于其他时区，请为你的函数应用创建一个名为 `WEBSITE_TIME_ZONE` 的应用设置。 

此设置的值取决于运行函数应用的操作系统和计划。

|操作系统 |计划 |值 |
|-|-|-|
| **Windows** |All | 将值设置为所需时区的名称，如 [Microsoft 时区索引](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10))中所示。 |
| **Linux** |高级<br/>专用 |将值设置为所需时区的名称，如 [tz 数据库](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)中所示。 |

> [!NOTE]
> Linux 消耗计划目前不支持 `WEBSITE_TIME_ZONE`。

例如东部标准时间 (Windows) 或美国/纽约 (Linux) 为 UTC-05:00 。 若要让计时器触发器每天在美国东部时间上午 10:00 触发，可使用表示 UTC 时区的以下 NCRONTAB 表达式：

```
"0 0 15 * * *"
``` 

或者为你的函数应用创建一个名为 `WEBSITE_TIME_ZONE` 的应用设置并将值设置为 `Eastern Standard Time` (Windows) 或 `America/New_York` (Linux)，然后使用以下 NCRONTAB 表达式： 

```
"0 0 10 * * *"
``` 

当使用 `WEBSITE_TIME_ZONE`，时间将针对特定时区中的时间更改进行调整，例如夏令时。 
