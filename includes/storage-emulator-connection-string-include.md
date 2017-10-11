存储模拟器支持单一固定的帐户和众所周知的身份验证密钥的共享密钥身份验证。 此帐户和密钥是允许用于存储模拟器的唯一共享密钥凭据。 它们是：

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> 存储仿真程序支持的身份验证密钥仅用于测试客户端身份验证代码的功能。 它不用于任何安全用途。 不能使用存储仿真程序中使用你的生产存储帐户和密钥。 你应将开发帐户用于生产数据。
> 
> 存储模拟器支持仅通过 HTTP 连接。 但是，HTTPS 是用于访问的生产 Azure 存储帐户中的资源的建议的协议。
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>连接到使用快捷方式的仿真程序帐户
从你的应用程序连接到存储模拟器的最简单方法是在你的应用程序配置文件引用快捷方式配置连接字符串`UseDevelopmentStorage=true`。 下面是存储仿真程序中的连接字符串的示例*app.config*文件： 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>连接到使用的已知帐户名称和密钥的仿真程序帐户
若要创建的连接字符串的引用的仿真程序帐户名称和密钥，必须为每个你想要使用的模拟器中的连接字符串从服务中指定的终结点。 这是必需的因此，连接字符串将引用有别于那些生产存储帐户的模拟器终结点。 例如，你的连接字符串的值将如下所示：

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

此值等同于如上所示的快捷方式`UseDevelopmentStorage=true`。

#### <a name="specify-an-http-proxy"></a>指定一个 HTTP 代理
你还可以指定您要测试你针对存储模拟器的服务时要使用 HTTP 代理。 这可用于当调试针对存储服务的操作时观察 HTTP 请求和响应。 若要指定代理，将添加`DevelopmentStorageProxyUri`选项设为连接字符串，并将其值设置为代理 URI。 例如，下面是指向存储模拟器并配置 HTTP 代理的连接字符串：

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

