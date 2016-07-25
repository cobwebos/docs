## 将消息发送到事件中心

事件中心的 Java 客户端库可用于 [Maven 中央存储库](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)中的 Maven 项目，并且可以使用 Maven 项目文件中的以下依赖项声明进行引用：

``` XML
<dependency>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>azure-eventhubs</artifactId>
	<version>0.7.2</version>
</dependency>
```
 
对于不同类型的生成环境，你可以从 [Maven 中央存储库](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)或 [GitHub 上的版本分发点](https://github.com/Azure/azure-event-hubs/releases)显式获取最新发布的 JAR 文件。

对于简单的事件发布服务器，请导入事件中心客户端类的 *com.microsoft.azure.eventhubs* 包和实用程序类（如与 Azure 服务总线消息传递客户端共享的常见异常）的 *com.microsoft.azure.servicebus* 包。

对于下面的示例，请首先在你最喜欢的 Java 开发环境中为控制台/shell 应用程序创建一个新的 Maven 项目。该类将称为 ```Send```。

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
	public static void main(String[] args) 
			throws ServiceBusException, ExecutionException, InterruptedException, IOException
	{
```

将命名空间和事件中心名称替换为创建事件中心时使用的值。

``` Java
	final String namespaceName = "----ServiceBusNamespaceName-----";
	final String eventHubName = "----EventHubName-----";
	final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
	final String sasKey = "---SharedAccessSignatureKey----";
	ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

然后，通过将字符串转换为其 UTF-8 字节编码创建单一事件。然后再使用连接字符串创建一个新的事件中心客户端实例并发送该消息。

``` Java 
				
	byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
	EventData sendEvent = new EventData(payloadBytes);
	
	EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
	ehClient.sendSync(sendEvent);
	}
}

``` 

<!---HONumber=Mooncake_0718_2016-->