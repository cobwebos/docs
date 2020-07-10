---
ms.openlocfilehash: b4fbeb6baa4516ca4cf3ca6194195fae2c688b07
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165646"
---
CRON 表达式使用的默认时区为协调世界时 (UTC)。 若要让 CRON 表达式基于其他时区，请为你的函数应用创建一个名为 `WEBSITE_TIME_ZONE` 的应用设置。 

此设置的值取决于操作系统和运行函数应用的计划。

|操作系统 |计划 |值 |
|-|-|-|
| Windows |全部 | 将值设置为所需时区的名称，如 [Microsoft Time Zone Index] (中所示 https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10) 。 |
| **Linux** |高级<br/>专用 |将值设置为所需时区的名称，如[tz 数据库](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)中所示。 |

> [!NOTE]
> `WEBSITE_TIME_ZONE`Linux 消耗计划当前不支持。

例如，*东部标准时间* (Windows) 或*北美/New_York* (Linux) 为05:00。 若要让计时器触发器每天在美国东部时间上午 10:00 触发，可使用表示 UTC 时区的以下 NCRONTAB 表达式：

```
"0 0 15 * * *"
``` 

或创建名为的函数应用的应用设置 `WEBSITE_TIME_ZONE` ，将此值设置为 `Eastern Standard Time` (Windows) 或 `America/New_York` (Linux) ，然后使用以下 NCRONTAB 表达式： 

```
"0 0 10 * * *"
``` 

当使用 `WEBSITE_TIME_ZONE`，时间将针对特定时区中的时间更改进行调整，例如夏令时。 
