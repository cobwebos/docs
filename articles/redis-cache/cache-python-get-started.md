<properties
	pageTitle="如何将 Azure Redis 缓存与 Python 配合使用 | Microsoft Azure"
	description="开始将 Azure Redis 缓存与 Python 配合使用"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.date="08/17/2015"
	wacn.date=""/>

# 如何将 Azure Redis 缓存与 Python 配合使用

本主题说明如何开始将 Azure Redis 缓存与 Python 配合使用。


## 先决条件

安装 [redis-py](https://github.com/andymccurdy/redis-py)。


## 在 Azure 上创建 Redis 缓存

在 [Azure 管理门户预览版](http://go.microsoft.com/fwlink/?LinkId=398536)中，单击“新建”>“数据 + 存储”，然后选择“Redis 缓存”。

  ![][1]

输入 DNS 主机名。该名称的格式为 `<name>.redis.cache.windows.net`。单击“创建”。

  ![][2]

创建缓存后，在 Azure 门户中单击它以查看缓存设置。你将需要：

- **主机名**。 你在创建缓存时已输入此名称。
- **端口**。 单击“端口”下面的链接可查看端口。请使用 SSL 端口。
- **访问密钥**。 单击“密钥”下的链接，然后复制主密钥。

## 在缓存中添加一些内容并检索此内容

    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'

将 *&lt;name&gt;* 替换为你的缓存名称，将 *&lt;key&gt;* 替换为你的缓存访问密钥。


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png

<!---HONumber=71-->