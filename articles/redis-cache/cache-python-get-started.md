---
title: 创建使用 Azure Redis 缓存的 Python 应用快速入门 | Microsoft Docs
description: 本快速入门介绍了如何创建使用 Redis 缓存的 Python 应用
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/11/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: b03139bda44886eba13892b23dc17fd3f030cc3f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639531"
---
# <a name="quickstart-use-azure-redis-cache-with-python"></a>快速入门：将 Azure Redis 缓存与 Python 配合使用


## <a name="introduction"></a>介绍

本快速入门展示了如何通过 Python 连接到 Azure Redis 缓存来从缓存中读取数据以及向其中写入数据。 

![完成的 Python 测试](./media/cache-python-get-started/cache-python-completed.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 安装有 [pip](https://pypi.org/project/pip/) 的 [Python 2 或 Python 3 环境](https://www.python.org/downloads/)。 

## <a name="create-a-redis-cache-on-azure"></a>在 Azure 上创建 Redis 缓存
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>安装 redis-py

[Redis-py](https://github.com/andymccurdy/redis-py)是 Redis 缓存的 Python 接口。 使用 Python 包工具 *pip* 安装 redis-py 包。 

以下示例通过以提升的管理员权限运行的 Visual Studio 2017 开发人员命令提示使用 Python3 的 *pip3* 在 Windows 10 上安装 redis-py 包。

    pip3 install redis

![安装 redis-py](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>从缓存中读取数据以及向其中写入数据

从命令行运行 Python 并使用缓存进行测试。 将 `<Your Host Name>` 和 `<Your Access Key>` 替换为你的 Redis 缓存的值。 

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>.redis.cache.windows.net',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

## <a name="create-a-python-script"></a>创建 Python 脚本

创建一个名为 *PythonApplication1.py* 的新的脚本文本文件。

向 *PythonApplication1.py* 添加以下脚本并保存该文件。 该脚本将测试缓存访问。 将 `<Your Host Name>` 和 `<Your Access Key>` 替换为你的 Redis 缓存的值。 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,password=myPassword,ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ") 
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

使用 Python 运行该脚本。

![完成的 Python 测试](./media/cache-python-get-started/cache-python-completed.png)


## <a name="clean-up-resources"></a>清理资源

如果将继续学习下一教程，可以保留本快速入门中创建的资源，以便重复使用。

如果已完成快速入门示例应用程序，可以删除本快速入门中创建的 Azure 资源，以免产生费用。 

> [!IMPORTANT]
> 删除资源组的操作不可逆，资源组以及其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有资源组（其中包含要保留的资源）中为托管此示例而创建了相关资源，可从各自的边栏选项卡逐个删除这些资源，而不要删除资源组。
>

登录到 [Azure 门户](https://portal.azure.com)，并单击“资源组”。

在“按名称筛选...”文本框中键入资源组的名称。 本文的说明使用了名为 *TestResources* 的资源组。 在结果列表中的资源组上，单击“...”，然后单击“删除资源组”。

![删除](./media/cache-web-app-howto/cache-delete-resource-group.png)

系统会要求确认是否删除资源组。 键入资源组的名称进行确认，然后单击“删除”。

片刻之后，将会删除该资源组及其包含的所有资源。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建简单的使用 Azure Redis 缓存的 ASP.NET Web 应用。](./cache-web-app-howto.md)



<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
