Wiser is a simple SMTP server that you can use for unit testing applications that send mail.  It accepts all messages and stores them in an internal ArrayList.  Your unit test code can then examine the messages (or lack thereof) and verify their validity.

WISER IS NOT A MAIL SERVER.  If you want to receive email and then "do something" with the mail, simply implement the MessageHandler or MessageListener interfaces.  Look at the (very short) Wiser code as an example.

## How To Use It ##
Include the following jars on your classpath:
  * subethasmtp.jar
  * slf4j-api-X.X.X.jar
  * One of the slf4j connectors such as slf4j-simple-X.X.X.jar
  * JavaMail (mail.jar and activation.jar)

Using Wiser is trivial:
```
Wiser wiser = new Wiser();
wiser.setPort(2500); // Default is 25
wiser.start();
```

Now, use a mail client to send email to your Wiser server. To get your JavaMail messages out of the Wiser server, just use this code:

```
for (WiserMessage message : wiser.getMessages())
{
	String envelopeSender = message.getEnvelopeSender();
	String envelopeReceiver = message.getEnvelopeReceiver();

	MimeMessage mess = message.getMimeMessage();

	// now do something fun!
}
```

Note that Wiser is not thread-safe; start it, process your unit tests, then check for delivered messages.