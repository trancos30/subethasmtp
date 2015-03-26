SubEthaSMTP depends on the [SLF4J](http://www.slf4j.org/) logging library and [JavaMail](http://www.oracle.com/technetwork/java/javamail/index.html). You will need  slf4j-api-X.X.X.jar and one of the SLF4J binding jars such as  slf4j-simple-X.X.X.jar, both of which are included in our distribution. Other binding jars (Log4J, java.util.logging, LogKit, etc) are available in the SLF4J distribution which you can download from their website.

Note that [Wiser](Wiser.md) has additional dependencies.

SubEthaSMTP implements a powerful, low-level API that lets you handle incoming mail and (optionally) authenticate clients.  It also provides several helper classes that make life easier, if perhaps less flexible.

## The Basic API ##
```
SMTPServer smtpServer = new SMTPServer(myFactory);
smtpServer.start();
```

```
public interface MessageHandlerFactory
{
	/**
	 * Called for the exchange of a single message during an SMTP conversation.
	 */
	public MessageHandler create(MessageContext ctx);
}

public interface MessageHandler
{
	/**
	 * Called first, after the MAIL FROM during a SMTP exchange.
	 */
	public void from(String from) throws RejectException;
	
	/**
	 * Called once for every RCPT TO during a SMTP exchange.
	 * This will occur after a from() call.
	 */
	public void recipient(String recipient) throws RejectException;
	
	/**
	 * Called when the DATA part of the SMTP exchange begins.
	 */
	public void data(InputStream data) throws RejectException, TooMuchDataException, IOException;
}
```

For every message delivery, SubEthaSMTP will call your Factory to create a MessageHandler.  The from(), recipient(), and data() methods will be called during each stage of the SMTP transaction. If multiple messages are delivered within a single SMTP session (via the RSET command), multiple MessageHandlers will be created.

## Higher-Level Help ##

If you simply wish to receive one copy of each message/recipient combination, the SimpleMessageListener will help you.

```
SMTPServer smtpServer = new SMTPServer(new SimpleMessageListenerAdapter(myListener));
smtpServer.start();
```

The SimpleMessageListener is easy to implement:

```
public interface SimpleMessageListener
{
	/**
	 * Called once for every RCPT TO during a SMTP exchange.  Each accepted recipient
	 * will result in a separate deliver() call later.
	 */
	public boolean accept(String from, String recipient);

	/**
	 * When message data arrives, this method will be called for every recipient
	 * this listener accepted.
	 */
	public void deliver(String from, String recipient, InputStream data)
			throws TooMuchDataException, IOException;
}
```

Your listener simply accept()s any recipients it cares about, and the SMTP server will deliver() an input stream of the message data. Any recipients which are not accepted by a listener will be rejected.

Beware, however, that delivering mail to multiple recipients requires that the entire message be buffered as it is read by the first consumer.  The buffer will start to memory, then switch to disk if the size exceeds a threshold.

Look at the source code for [Wiser](Wiser.md) to see a simple example of how to implement SimpleMessageListener.deliver() so that it receives a message and converts it into a JavaMail MimeMessage object.

## Authentication ##

SubEtha SMTP supports the SMTP AUTH commands through a factory interface similar to the MessageHandlerFactory.  For convenience, a pluggable authenticator implementation that supports PLAIN and LOGIN is provided with the distribution.

```
SMTPServer smtpServer = new SMTPServer(myMessageHandlerFactory);
smtpServer.setAuthenticationHandlerFactory(new EasyAuthenticationHandlerFactory(myUsernamePasswordValidator));
smtpServer.start();
```

If you do not explicitly set an AuthenticationHandlerFactory, SMTP AUTH will not be advertised or supported.  See the javadocs for AuthenticationHandlerFactory for more information.

## Threading ##

SubEthaSMTP is a multithreaded server.  You may change settings on a stopped server (ie the # of simultaneous connections allowed) but not after calling start().  Each SMTP connection will be serviced by a new thread.

## Using Port 25 on Unix ##

On unix, it is possible to run SubEthatSMTP on port 25 without running the JVM as root. Please see the iptables/ipfw instructions here: [UsingPort25](http://code.google.com/p/subetha/wiki/UsingPort25)

## SSL Configuration ##

Erik van Oosten has written an excellent blog posting about [how to configure this](http://blog.jteam.nl/2009/11/10/securing-connections-with-tls/).