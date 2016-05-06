In all API, communicating with a RougeServer involves two classes, RougeDriver and RougeListener.
RougeDriver handles the connection itself, and provides the methods to send messages.
RougeListener is more of an interface/protocol, defining the necessary methods that are
required to receive messages and events from RougeServer.

At its core, RougeServer is only a communication framework. Messages are a combination of a
command with its corresponding payload. The command is a simple string, while the payload
is a hash-map of object, encapsulated within a RougeObject. Any messages can be sent to
the server, as long as the appropriate server handler are registered within the RougeServer.
If the basic functionalities provided by RougeServer does not fit your needs, you are
encouraged to write your own custom server handler.

# Step by Step Example #

The following example describes a client that will connect to a RougeServer, create a
chat room and then send a message to that room. Although Java was chosen for this
example, the complete source code for all supported API language is provided below.

To learn how the room API, or any other API provided by RougeServer functions, you are
encouraged to check out the protocol documentation at http://code.google.com/p/rouge-server/wiki/Protocol

Imports are sometimes omitted in this example to simplify the code.

## Step 1: Create the class ##

As previously mentioned, RougeListener is used to describe classes that receive data
from the RougeServer. In this case, we create a simple RougeTest class that implements
the RougeListener class. This adds three methods to our class.

```

public class RougeTest extends RougeListener {

	@Override
	public void onMessage(String command, RougeObject payload) { }

	@Override
	public void onConnect() { }

	@Override
	public void onDisconnect() { }

}

```

The onConnect() method is triggered when the driver connects to the Rouge Server. This is
usually a good time to login. As for the onDisconnect() method, it is triggered when the
driver loses connection to the server. The onMessage method is triggered every time a message is
returned from the server. When using commands provided by RougeServer, their output is described
in the protocol described at http://code.google.com/p/rouge-server/wiki/Protocol . In this example, using the messages are printed out to STDOUT. The toJSon method is used to describe the content of the payload.

```
	@Override
	public void onMessage(String command, RougeObject payload) {
		
		System.out.println(command + " " + payload.toJSON().toString());	
	}
```

## Step 2: Login ##

Also mentioned previously, RougeDriver handles the connections with the server. The first step
is to create the object, connect and send a login command with a username and password payload.

```
	this.driver = new RougeDriver("localhost", 6611, this, false);
		
	try {
			this.driver.connect();
	} catch (RougeConnectionFailure e) {
		e.printStackTrcce;
	}
```

Note that the default configuration of RougeServer authenticates users from the MySQL database. You
might want to add a using manually to the user table, or experiment with the user.create command
instead.

```
	@Override
	public void onConnect() {
		
		log.trace("Connected ...");
		
		RougeObject payload = new RougeObject();
		payload.putString("username", "bob");
		payload.putString("password", "password");
				
		this.driver.send("login", payload);
	}
```

## Step 3: Sending a command ##

We want to create the room once we are logged in. This is because anonymous users have very little
rights in RougeServer. Typically, they can login or create new accounts. To send the command
at the appropriate time, we monitor the onReceive method. It's the same situation with waiting
for a room to be created before sending a message.

```
	@Override
	public void onMessage(String command, RougeObject payload) {
		
		if ("login".equals(command)) {
			RougeObject newPayload = new RougeObject();
			
			newPayload.putString("name", "testRoom");
			this.driver.send("room.create", newPayload);
			
		} else if ("room.create".equals(command)) {
			
			RougeObject newPayload = new RougeObject();
			newPayload.putString("name", "testRoom");
			newPayload.putString("message", "hello");
			this.driver.send("room.say", newPayload);
		}
	}
```

## Step 4: Beyond ##

Well, there is no formal step 4. Once you have setup the connection and received responses, you
know most of what goes on in client programming.

# Complete Samples #

Here are the complete samples in Java, Objective-C and C#.

## Java ##

```

public class RougeTest extends RougeListener {

	private RougeDriver driver;
	
	public RougeTest() {
		
		this.driver = new RougeDriver("localhost", 6611, this, false);
		
		try {
			this.driver.connect();
		} catch (RougeConnectionFailure e) {
			System.exit(-1);
		}		
		
		// Keeps the application going
		while(true) {
			try {
				Thread.sleep(500);
			} catch (InterruptedException e) {
				// Nothing to do
			}
		}
	}
	
	@Override
	public void onMessage(String command, RougeObject payload) {
		
		if ("login".equals(command)) {
			RougeObject newPayload = new RougeObject();
			
			newPayload.putString("name", "testRoom");
			this.driver.send("room.create", newPayload);
			
		} else if ("room.create".equals(command)) {
			
			RougeObject newPayload = new RougeObject();
			newPayload.putString("name", "testRoom");
			newPayload.putString("message", "hello");
			this.driver.send("room.say", newPayload);
		}
	}

	@Override
	public void onConnect() {
		
		RougeObject payload = new RougeObject();
		payload.putString("username", "bob");
		payload.putString("password", "password");
				
		this.driver.send("login", payload);
		
		RougeObject newPayload = new RougeObject();
		newPayload.putString("name", "testRoom");
		this.driver.send("room.create", newPayload);
		
		newPayload.putString("message", "hello");
		this.driver.send("room.say", newPayload);
	}

	@Override
	public void onDisconnect() {
		
		log.debug("Disconnected ...");
	}

	public static void main(String[] args) {
		 new RougeTest();
	}
}

```

## Objective-C ##

The Objective-C example is wrapped in a simple Cocoa application that opens a window and then connects to the server.

### RougeDriverAppDelegate.h ###

```
#import <Cocoa/Cocoa.h>
#import "RougeDriver.h"

@interface RougeDriverAppDelegate : NSObject <NSApplicationDelegate, RougeListener> {
@private
    NSWindow *window;
    RougeDriver* driver;
}

@property (assign) IBOutlet NSWindow *window;

@end
```

### RougeDriverAppDelegate.c ###

```
#import "RougeDriverAppDelegate.h"

@implementation RougeDriverAppDelegate

@synthesize window;

- (void)applicationDidFinishLaunching:(NSNotification *)aNotification
{
    driver = [[RougeDriver alloc] initWithHandler:self];
    
    [driver connect:@"localhost" toPort:6612 withBEncoding:true];
    
}

- (void) onConnect {
                    
    NSLog(@"On Connect");
    
    RougeObject *payload = [[RougeObject alloc] init]; 
    [payload putString:@"bob" withKey:@"username"];
    [payload putString:@"password" withKey:@"password"];
    
    [driver send:@"login" withPayLoad:payload];
    
    payload = [[RougeObject alloc] init]; 
    [payload putString:@"main" withKey:@"name"];
    
    [driver send:@"room.create" withPayLoad:payload];
    
    [payload putString:@"message" withKey:@"hello!"];
    
    [driver send:@"room.say" withPayLoad:payload];   
}
                    
- (void) onDisconnect {

    NSLog(@"On Disconnect");
}
                    

- (void) onMessage:(NSString *)command withPayLoad:(RougeObject *)rougeObject {
    
    NSLog(@"Message received! %@ %@", command,  [rougeObject toDictionary]);
}

@end
```

## C# ##

This is the trickiest to setup, given that the driver and the sample programs are provided as two separate projects in a solution. However, the driver could be compiled into a DLL and linked to a new project.

```
class Program : RougeListener
{
		
	private RougeDriver driver;
		
	public Program() {

            driver = new RougeDriver("127.0.0.1", 6611, this, false);
            driver.connect();
			
	}
		
	public void onMessage(string command, RougeObject payload) {
			
		Console.WriteLine("Received {0} {1}", command, payload.toJson());
			
		if (command.Equals("login")) 
		{
			RougeObject rougeObject = new RougeObject();
        	    	rougeObject.putString("name", "csroom2");
       			driver.send("room.create", rougeObject);
		} 
		else if (command.Equals("room.create")) 
		{
			RougeObject rougeObject = new RougeObject();
			rougeObject.putString("name", "csroom2");
            		rougeObject.putString("message", "hello");
       			driver.send("room.say", rougeObject);
		}
	}
		
       public void onConnect() {
			
	    RougeObject rougeObject = new RougeObject();
            rougeObject.putString("username", "bob");
            rougeObject.putString("password", "password");
			
	    driver.send("login", rougeObject);
	}
		
        public void onDisconnect() {
			
		Console.WriteLine("Disconnected");
	}
		
        static void Main(string[] args)
        {
            new Program();

            while (true) ;
        }  
    }
```