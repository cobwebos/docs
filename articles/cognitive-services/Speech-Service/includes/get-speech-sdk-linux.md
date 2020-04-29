---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399942"
---
:::row:::
    :::column span="3":::
        语音 SDK 仅支持以下目标体系结构上的**Ubuntu 16.04/18.04**、 **Debian 9**、 **Red Hat Enterprise Linux （RHEL） 7/8**和**CentOS 7/8** ：
        - X64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> 面向 Linux ARM64 并使用 c # 时，需要 .NET Core 2.x （dotnet package）。 如果面向的是 ARM32 或 ARM64，则不支持 Python。

> [!NOTE]
> Ubuntu 16.04、Ubuntu 18.04 和 Debian 9 的 x86 体系结构仅支持通过语音 SDK 进行 c + + 开发。

### <a name="system-requirements"></a>系统要求

对于本机应用程序，语音 SDK 依赖于`libMicrosoft.CognitiveServices.Speech.core.so`。 请确保目标体系结构（x86、x64）与应用程序匹配。 可能需要其他依赖项，具体取决于 Linux 版本。

- GNU C 库的共享库（包括 POSIX 线程编程库 `libpthreads`）
- OpenSSL 库（`libssl.so.1.0.0` 或 `libssl.so.1.0.2`）
- ALSA 应用程序的共享库 (`libasound.so.2`)

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 和 CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> 按照[如何配置 RHEL/CentOS 7 For SPEECH SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)中的说明进行操作。

> [!TIP]
> 在 RHEL/CentOS 8 上，按照[如何配置 OpenSSL for Linux](../how-to-configure-openssl-linux.md) 上的说明进行操作。

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
