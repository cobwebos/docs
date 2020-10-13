---
title: 排查使用 Azure Cosmos 模拟器时遇到的问题
description: 了解如何在使用 Azure Cosmos 模拟器时对服务不可用、证书、加密和版本控制问题进行故障排除。
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperfq1
ms.openlocfilehash: af9122aaa0233fe5248f31ffe805e01a98831eae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447428"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-emulator"></a>排查使用 Azure Cosmos 模拟器时遇到的问题

Azure Cosmos 模拟器提供了一个模拟 Azure Cosmos DB 服务以进行开发的本地环境。 使用本文中的提示可帮助解决在安装或使用 Azure Cosmos 模拟器时遇到的问题。 

如果安装了新版本的模拟器并遇到错误，请务必重置数据。 可以通过右键单击系统托盘上的 "Azure Cosmos 模拟器" 图标，然后单击 "重置数据 ..." 来重置数据。 如果这不能修复错误，则可以卸载模拟器和任何较早版本的仿真程序（如果找到），删除 *C:\Program files\Azure Cosmos DB 模拟器* 目录并重新安装模拟器。 有关说明，请参阅[卸载本地模拟器](local-emulator.md#uninstall)。 或者，如果重置数据不起作用，请导航到 " `%LOCALAPPDATA%\CosmosDBEmulator` 位置" 并删除该文件夹。

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>Windows 性能计数器损坏疑难解答

* 如果 Azure Cosmos 模拟器崩溃，请从文件夹收集转储文件 `%LOCALAPPDATA%\CrashDumps` ，对其进行压缩，并从 [Azure 门户](https://portal.azure.com)中打开支持票证。

* 如果 `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` 中出现崩溃，这可能表示性能计数器处于损坏状态。 通常，从管理员命令提示符处运行以下命令即可解决此问题：

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>解决连接问题

* 如果遇到连接问题，请[收集跟踪文件](#trace-files)，对其进行压缩，然后在 [Azure 门户](https://portal.azure.com)中开具支持票证。

* 如果收到“服务不可用”消息，模拟器可能无法初始化网络堆栈。 请查看是否安装了 Pulse 安全客户端或 Juniper 网络客户端，因为其网络筛选器驱动程序可能会导致该问题。 卸载第三方网络筛选器驱动程序通常可修复此问题。 或者，使用 /DisableRIO 启动模拟器，这会将模拟器网络通信切换到常规 Winsock。 

* 如果你遇到 **"禁止"，则 "消息"： "请求是在传输协议或密码中使用禁止的加密进行的。检查帐户 SSL/TLS 允许的最小协议设置 ... "** 连接问题，这可能是由于 OS 中的全局更改 (例如有问必答 Preview Build 20170) 或启用 TLS 1.3 作为默认值的浏览器设置导致的。 使用 SDK 对 Cosmos 模拟器执行请求时，可能会发生类似错误，例如“Microsoft.Azure.Documents.DocumentClientException:正在使用传输协议或密码中禁止的加密方法发出请求。请检查帐户 SSL/TLS 允许的最低协议设置”。 此时这是预期情况，因为 Cosmos 模拟器仅接受并使用 TLS 1.2 协议。 建议的解决方法是将设置和默认值更改为 TLS 1.2;例如，在 IIS 管理器中，导航到 "站点"-> "默认网站"，找到端口8081的 "网站绑定" 并对其进行编辑以禁用 TLS 1.3。 可以通过“设置”选项对 Web 浏览器执行类似操作。

* 在模拟器运行时，如果计算机进入了睡眠模式或运行了任何 OS 更新，则你可能会看到“服务当前不可用”消息。 请右键单击 Windows 通知托盘中显示的图标，再选择“重置数据”来重置模拟器的数据。

## <a name="collect-trace-files"></a><a id="trace-files"></a>收集跟踪文件

若要收集调试跟踪，请从管理命令提示符运行以下命令：

1. 导航到安装了模拟器的路径：

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. 关闭模拟器并观看系统任务栏，以确保程序已关闭。 可能需要一分钟才能完成。 你还可以在 Azure Cosmos 模拟器用户界面中选择 " **退出** "。

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. 用以下命令开始记录：

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. 启动模拟器

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. 再现问题。 如果数据资源管理器不工作，只需等待浏览器打开几秒钟即可捕获错误。

1. 用以下命令停止日志记录：

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. 导航到 `%ProgramFiles%\Azure Cosmos DB Emulator` "路径"，并查找 *docdbemulator_000001 .etl* 文件。

1. 在 [Azure 门户](https://portal.azure.com)中开具支持票证，并提供 .etl 文件以及再现步骤。

## <a name="next-steps"></a>后续步骤

本文介绍了如何调试本地模拟器的问题。 现在，你可以继续学习下一篇文章：

* [导出 Azure Cosmos 模拟器证书以用于 Java、Python 和 Node.js 应用](local-emulator-export-ssl-certificates.md)
* [使用命令行参数和 PowerShell 命令控制模拟器](emulator-command-line-parameters.md)
