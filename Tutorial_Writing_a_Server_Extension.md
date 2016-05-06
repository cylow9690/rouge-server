An Expansion in RougeServer allows the server to send and handle new types of commands. This allows a developer to customize RougeServer to be game specific. Writing an expansion is done in two steps, designing the communication protocol and implementing the expansion.

In this tutorial, we will implement an expansion which records messages send by the client and echo them back, a kind of super echo. The communication would me as follows:

Msg sent to Server (-->) Msg sent from Server (<--)

  * --> This is a test
  * <-- {This is a test}
  * --> Does this work
  * <-- {This is a test, Does this work}
  * --> I guess it does
  * <-- {This is a test, Does this work, I guess it does}

# Protocol Design #

In Rouge, there are two components to a command, the command keyword and the command payload. In the case of the super echo protocol, we only need one command : "super.echo". This command would have one parameter in the payload, a string called "message". When the command returns, it will still be called "super.echo". However, it will also include a new parameter, "messages", which would be an RougeArray of strings.

If we were to send the commands using the Java client, the commands would look as such:

```
 RougeObject = new RougeObject();
 payload.putString("message", "This is a test"); 
 rougeDriver.send("super.echo", payload);
```

The code to handle the output would be as such.

```
public void onMessage(String command, RougeObject payload) {
		
	if (command.equals("super.echo")) {
	
		RougeArray messages = payload.getRougeArray("messages");
			
		for(Object o : messages.toList()) {
			String message = (String)o;
			System.out.println("Msg: " + message);
		}
	}
}
```

# Setting up a Development Environment #

There are two ways to setup a development environment with RougeServer, one is easier, and the other  is a lot more flexible for development, but harder to setup. Most developer will prefer the second setup.

## Scenario 1: Easier ##

First, install RougeServer on your computer.  Then, create a project in your favorite IDE program and add the rouge-server-xx.jar jar file to your class path. You can start developing the extension right  away.

When you want to test out your extension, compile and jar your extension's class file. Place that jar, along with any needed configuration or jar file, in the extension directory of Rouge Server. Start or restart the server. The extension should be automatically be loaded.

If you are having problem loading the extension, check out the section below on packaging and distribution.

## Scenario 2: Better, but more difficult ##

Again, install RougeServer on your computer. Then create a project in your favorite IDE using the RougeServer (either the download or the source) as your project's base directory. Create an additional source directory to hold the source of you extension. Add all the jars found in the lib directory to your project.

You'll need to create the configuration file for the extension, but you don't need to specify a jar file to load. You'll load those class files directly from your source directory.

To start Rouge Server, simply use the main method of the ca.qc.adinfo.rouge.RougeServer class. This will make development easier, but you'll still need to package your extension for non-development purposes (i.e. the real server).

# Writting the extension #

An extension is composed of three types of components, a configuration file, one
or several commands and one or several modules.


# Optional Page #

# Preparing for distribution #

Package the compiled class files of your extension in a jar file. Place that jar file, along with the configuration file and any other jar files required by your library in the extension folder.

RougeServer is built using Apache Ant. We highly recommend using it to automate the compilation and packaging of your extension.