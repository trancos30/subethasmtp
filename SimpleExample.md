# Introduction #

There are situations in which people need simple working examples and it is not because they are stupid or lazy, but because:

1. They are coming from different programing languages and have the urge of developing a solution using java.

2. They have been programing complex projects which do not allow to see things simply. So, a basic working example can help to have a clean start.

3. They may know how to do it, but want to see how the people who created the code will use it in a simple real server code.

4. They want to see the simple working example available online in order to send its link to other people (some already mentioned) who are already intimidated to ask about it (I wonder why).

# Details #

These are the steps to follow in a Linux and Mac computer:

1. Download the subethasmtp-3.1.zip file and uncompress it.

2. Create a Folder for your project. You could call it "mySMTPServer".

3. In this new folder create the files BasicSMTPServer.java and MyMessageHandlerFactory.java which are shown below.

4. Create a sub-folder called lib. Copy in this folder the following .jar files found in subethasmtp-3.1 folder.
  * activation.jar
  * junit-4.5.jar
  * mail.jar
  * slf4j-api-1.5.6.jar
  * slf4j-simple-1.5.6.jar
  * subethasmtp-3.1.jar

5. To compile the server you need to execute this line:
javac -classpath lib/activation.jar:lib/junit-4.5.jar:lib/mail.jar:lib/slf4j-api-1.5.6.jar:lib/slf4j-simple-1.5.6.jar:lib/subethasmtp-3.1.jar:. BasicSMTPServer.java

6. Run the server:
java -classpath lib/activation.jar:lib/junit-4.5.jar:lib/mail.jar:lib/slf4j-api-1.5.6.jar:lib/slf4j-simple-1.5.6.jar:lib/subethasmtp-3.1.jar:. BasicSMTPServer

7. In order to see the smtp server working do the following.
  * Open your email client.
  * Create a new smtp server connection with hostname "localhost" (if you are testing in your local PC) and port "25000"
  * Send an email using the created smtp connection.
  * You should see your email mime code displayed in the console output.

```
//-------------------------------------------------------------------
//BasicSMTPServer.java
//-------------------------------------------------------------------
import org.subethamail.smtp.server.SMTPServer;

public class BasicSMTPServer {
	public static void main(String[] args) {
		MyMessageHandlerFactory myFactory = new MyMessageHandlerFactory() ;
		SMTPServer smtpServer = new SMTPServer(myFactory);
		smtpServer.setPort(25000);
		smtpServer.start();
	}
}
```

```
//-------------------------------------------------------------------
//MyMessageHandlerFactory.java
//-------------------------------------------------------------------
import org.subethamail.smtp.*;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;

public class MyMessageHandlerFactory implements MessageHandlerFactory {

    public MessageHandler create(MessageContext ctx) {
        return new Handler(ctx);
    }

    class Handler implements MessageHandler {
        MessageContext ctx;

        public Handler(MessageContext ctx) {
        	this.ctx = ctx;
        }
    	
        public void from(String from) throws RejectException {
        	System.out.println("FROM:"+from);
        }

        public void recipient(String recipient) throws RejectException {
        	System.out.println("RECIPIENT:"+recipient);
        }

        public void data(InputStream data) throws IOException {
        	System.out.println("MAIL DATA");
        	System.out.println("= = = = = = = = = = = = = = = = = = = = = = = = = = = = = = =");
        	System.out.println(this.convertStreamToString(data));
        	System.out.println("= = = = = = = = = = = = = = = = = = = = = = = = = = = = = = =");
        }

        public void done() {
        	System.out.println("Finished");
        }

    	public String convertStreamToString(InputStream is) {
    		BufferedReader reader = new BufferedReader(new InputStreamReader(is));
    		StringBuilder sb = new StringBuilder();
    		
    		String line = null;
    		try {
    			while ((line = reader.readLine()) != null) {
    				sb.append(line + "\n");
    			}
    		} catch (IOException e) {
    			e.printStackTrace();
    		}
    		return sb.toString();
    	}

    }
}
```