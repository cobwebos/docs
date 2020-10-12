---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 95bd83575809f6ecda716ff751b47b7bb499cae3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85073206"
---
## <a name="configure-an-environment-variable-for-authentication"></a>配置用于身份验证的环境变量

应用程序需要对它们使用的认知服务的访问进行身份验证。 若要进行身份验证，我们建议创建一个环境变量来存储 Azure 资源的密钥。 

获得密钥后，将其写入运行应用程序的本地计算机上的新环境变量。 若要设置环境变量，请打开控制台窗口，并遵照适用于操作系统的说明。 将 `your-key` 替换为资源的密钥之一。

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

添加环境变量后，可能需要重启任何正在运行的、需要读取环境变量的程序（包括控制台窗口）。 例如，如果使用 Visual Studio 作为编辑器，请在运行示例之前重启 Visual Studio。

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

添加环境变量后，请从控制台窗口运行 `source ~/.bashrc`，使更改生效。

#### <a name="macos"></a>[macOS](#tab/unix)

编辑 .bash_profile，然后添加环境变量：

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

添加环境变量后，请从控制台窗口运行 `source ~/.bash_profile`，使更改生效。

***
