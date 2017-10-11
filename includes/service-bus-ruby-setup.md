## <a name="create-a-ruby-application"></a>创建 Ruby 应用程序
有关说明，请参阅[在 Azure 上创建 Ruby 应用程序](../articles/virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)。

## <a name="configure-your-application-to-use-service-bus"></a>应用程序配置为使用服务总线
若要使用 Service Bus，下载并使用 Azure Ruby 程序包，其中包括一组便于与存储 REST 服务进行通信的库。

### <a name="use-rubygems-to-obtain-the-package"></a>使用 RubyGems 获取该程序包
1. 使用命令行接口，如**PowerShell** (Windows)、**终端**(Mac) 或**Bash** (Unix)。
2. 若要安装 gem 和依赖项的命令窗口中键入"gem install azure"。

### <a name="import-the-package"></a>将包导入
使用你最喜欢的文本编辑器，将以下代码添加到你打算在其中使用存储的 Ruby 文件的顶部：

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>设置服务总线连接
下面的代码用于设置命名空间的值、 密钥、 密钥、 签署和主机的名称：

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

命名空间值设置为你创建而不是整个 URL 的值。 例如，使用**"yourexamplenamespace"**，不是"yourexamplenamespace.servicebus.windows.net"。
