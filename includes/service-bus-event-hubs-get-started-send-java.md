## 将消息发送到事件中心
在本节中，我们将编写用于将事件发送到事件中心的 Java 控制台应用。我们将使用 JMS AMQP 提供程序从[Apache Qpid 项目](http://qpid.apache.org/)。这是类似于与 AMQP 通过 Java 使用 Service Bus 队列和主题，如所示[此处](http://www.windowsazure.cn/zh-cn/documentation/articles/service-bus-java-how-to-use-jms-api-amqp/)。有关详细信息，请参阅[Qpid JMS 文档](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html)和[Java 消息服务](http://www.oracle.com/technetwork/java/jms/index.html)。

1. 在 Eclipse 中，创建一个名为新的 Java 项目**发件人**。

2. 下载最新版本的**Qpid JMS AMQP 1.0**库从[此处](http://qpid.apache.org/components/qpid-jms/index.html)。

3. 从存档中解压缩文件并将以下 jar 从存档 qpid-amqp-1-0-client-jms\ < 版本 > \lib 目录复制到您的 Eclipse**发件人**项目。

4. 在 Eclipse 包资源管理器中，右键单击**发件人**项目，然后选择**属性**。在该对话框的左窗格中，单击**Java 生成路径**，然后单击**库**选项卡上，然后**添加 Jar**按钮。选择以前已复制的所有 jar，然后单击**确定**。

	![][8]

5. 创建一个名为文件**servicebus.properties**根目录中的**发件人**项目中的，使用以下内容。请记住要替换的值为您的事件集线器名称和命名空间名称 （通常是后者 `{事件集线器名称}-ns`)。您还必须替换的密钥的 URL 编码版本**SendRule**之前创建。您可以进行 URL 编码它[此处](http://www.w3schools.com/tags/ref_urlencode.asp)。

		# servicebus.properties - sample JNDI configuration

		# Register a ConnectionFactory in JNDI using the form:
		# connectionfactory.[jndi_name] = [ConnectionURL]
		connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.chinacloudapi.cn/?sync-publish=false

		# Register some queues in JNDI using the form
		# queue.[jndi_name] = [physical_name]
		# topic.[jndi_name] = [physical_name]
		queue.EventHub = {event hub name}

5. 创建一个新的类，名为**发件人**。将下面的导入语句添加：

		import java.io.BufferedReader;
		import java.io.IOException;
		import java.io.InputStreamReader;
		import java.io.UnsupportedEncodingException;
		import java.util.Hashtable;
		
		import javax.jms.BytesMessage;
		import javax.jms.Connection;
		import javax.jms.ConnectionFactory;
		import javax.jms.Destination;
		import javax.jms.JMSException;
		import javax.jms.MessageProducer;
		import javax.jms.Session;
		import javax.naming.Context;
		import javax.naming.InitialContext;
		import javax.naming.NamingException; 

8. 然后，向其中添加以下代码：

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// Configure JNDI environment
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);
	
			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
	
			Destination queue = (Destination) context.lookup("EventHub");
	
			// Create Connection
			Connection connection = cf.createConnection();
	
			// Create sender-side Session and MessageProducer
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);
	
			System.out.println("Press Ctrl-C to stop the sender process");
			System.out.println("Press Enter to start now");
			BufferedReader commandLine = new java.io.BufferedReader(
					new InputStreamReader(System.in));
			commandLine.readLine();
	
			while (true) {
				sendBytesMessage(sendSession, sender);
				Thread.sleep(200);
			}
		}
		
		private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
	        BytesMessage message = sendSession.createBytesMessage();
	        message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
	        sender.send(message);
	        System.out.println("Sent message");
	    }



<!-- Links -->
[Azure 管理门户]: https://manage.windowsazure.cn/


<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png
