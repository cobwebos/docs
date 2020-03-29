---
title: 安全性
titleSuffix: Azure Cognitive Services
description: 了解认知服务使用的各种安全注意事项。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131537"
---
# <a name="azure-cognitive-services-security"></a>Azure 认知服务安全性

在开发任何和所有应用程序时，安全性应被视为重中之重。 随着启用人工智能的应用程序的建立，安全性就显得尤为重要。 在本文中，概述了 Azure 认知服务安全性的各个方面，例如使用传输层安全性、身份验证和安全配置敏感数据。

## <a name="transport-layer-security-tls"></a>传输层安全 (TLS) (Transport Layer Security) (TLS)

通过 HTTP 公开的所有认知服务终结点都强制实施 TLS 1.2。 使用强制安全协议，尝试调用认知服务终结点的使用者应遵守以下准则：

* 客户端操作系统 （OS） 需要支持 TLS 1.2
* 用于进行 HTTP 调用的语言（和平台）需要指定 TLS 1.2 作为请求的一部分
  * 根据语言和平台的不同，指定 TLS 是隐式或显式完成的

对于 .NET 用户，请考虑<a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">传输层安全最佳实践<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="authentication"></a>身份验证

在讨论身份验证时，有几个常见的误解。 身份验证和授权经常相互混淆。 身份也是安全性的重要组成部分。 标识是有关<a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">主体<span class="docon docon-navigate-external x-hidden-focus"></span></a>的信息的集合。 标识提供程序 （IdP） 向身份验证服务提供标识。 身份验证是验证用户身份的行为。 授权是指定给定标识的资源访问权限和权限的规范。 一些认知服务产品包括基于角色的访问控制 （RBAC）。 RBAC 可用于简化手动管理主体所涉及的一些仪式。 有关详细信息，请参阅[Azure 资源的基于角色的访问控制](../role-based-access-control/overview.md)。

有关使用订阅密钥、访问令牌和 Azure 活动目录 （AAD） 进行身份验证的详细信息，请参阅<a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">对 Azure<span class="docon docon-navigate-external x-hidden-focus"></span>认知服务的身份验证请求</a>。

## <a name="environment-variables-and-application-configuration"></a>环境变量和应用程序配置

环境变量是名称值对，存储在特定环境中。 对敏感数据使用硬编码值的一个更安全的替代方法是使用环境变量。 硬编码值不安全，应避免使用。

> [!CAUTION]
> **不要**对敏感数据使用硬编码值，这样做是一个主要的安全漏洞。

> [!NOTE]
> 当环境变量以纯文本形式存储时，它们被隔离到环境中。 如果环境遭到破坏，则环境中的变量也会受到影响。

### <a name="set-environment-variable"></a>设置环境变量

要设置环境变量，请使用以下命令之一 - 其中`ENVIRONMENT_VARIABLE_KEY`是命名键，`value`是存储在环境变量中的值。

# <a name="command-line"></a>[命令行](#tab/command-line)

创建和分配持久化环境变量（给定值）。

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

在**命令提示符**的新实例中，读取环境变量。

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[电源外壳](#tab/powershell)

创建和分配持久化环境变量（给定值）。

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

在**Windows PowerShell**的新实例中，读取环境变量。

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

创建和分配持久化环境变量（给定值）。

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

在**Bash**的新实例中，读取环境变量。

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> 设置环境变量后，重新启动集成开发环境 （IDE），以确保新添加的环境变量可用。

### <a name="get-environment-variable"></a>获取环境变量

要获取环境变量，必须将其读取到内存中。 根据所使用的语言，请考虑以下代码段。 这些代码段演示如何获取环境变量给定`ENVIRONMENT_VARIABLE_KEY`并分配给名为 的`value`变量。

# <a name="c"></a>[C#](#tab/csharp)

有关详细信息，请参阅<a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank">`Environment.GetEnvironmentVariable`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

有关详细信息，请参阅<a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank">`getenv`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

有关详细信息，请参阅<a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank">`System.getenv`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

有关详细信息，请参阅<a href="https://nodejs.org/api/process.html#process_process_env" target="_blank">`process.env`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

有关详细信息，请参阅<a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank">`os.environ`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[目标C](#tab/objective-c)

有关详细信息，请参阅<a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank">`environment`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>后续步骤

* 探索各种[认知服务](welcome.md)
* 了解有关[认知服务虚拟网络的更多信息](cognitive-services-virtual-networks.md)
