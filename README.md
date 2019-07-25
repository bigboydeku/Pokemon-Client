# Pokemon-Client - Created by Oliver Drowzdowski and Denis Nadarevic
We decided to recreate the core of Pokémon battles utilizing TCP sockets in Java. Players will connect to a server and take turns attacking until a Pokémon has fainted. Players can also choose from a selection of Pokémon. By doing this we hope to get a greater understanding of how TCP sockets, threads, Java, states, and client-server-client communication functions on a fundamental level. Note that we are just implementing the battling portion, as the rest of the game would be a massive undertaking. 

A substantial amount of planning was needed to understand what type of battle and network systems we needed, how we would organize the database of Pokémon and more. This project idea was decided to challenge not only our understanding of TCP sockets but our ability to program a modern game that is popular to this day. The Java programming language was chosen since both members understand the language. This report will explain the main features, tools, and techniques implemented to perform properly.

To execute the code, simply run this command, in the src file, in your terminal: ``` java Client ```

The application was originally created using the IntelliJ IDE, but it was switched over to the University of Windsor server for the presentation. A problem that we faced, when switching over, involved the database. The server does not allow us to upload files such as our database as it requires installation of SQLite in the server. 



## Implementation
Players handle their own states using a stateHandler. States used include NOT_CONNECTED, SUBMITTED_NAME, PLAYER_TURN, and WAITING. Clients also decodes information in the same way as the server by splitting the message from the server into a message and option. The player begins by being in a NOT_CONNECTED state, connects to the network, and sends the player’s name to the server.

```java
public boolean doConnection(int port){
	try {
		this.serverConnection = new ServerConnection(port);
	} catch (Exception e){
		e.printStackTrace();
		System.exit(1);
	}
	if (this.serverConnection != null){
		this.myState = PLAYER_STATE.SUBMITTED_NAME;
		sendMessageToServer("NAME_REG " + this.playerName);
		return true;
	}
	return false;
}
```

The client’s connection code is always wrapped around a try and catch to make sure we understand why a certain error is happening. If it successfully connects, it will set the state of the player to SUBMITTED_NAME and send the name to the server along with the command to understand what type of message it is.

```java
public void onReceiveServerMessage(String serverMessage){
String message = serverMessage.split(" ")[0];	
String option = serverMessage.split(" ")[1];
this.stateHandler(message, option);
}
```

Upon receiving the message, the player will decode it by splitting the message into two sections, message and option. It is then sent into the stateHandler to understand what the server is trying to say.

```java
public void sendMessageToServer(String msg){
	try {
		System.out.println("Sending msg to server: " + msg);
		this.serverConnection.getOutputStream().write(msg.getBytes());
		this.serverConnection.getOutputStream().flush();
	} catch (IOException e){
		e.printStackTrace();
		System.exit(1);
  }
}
```
