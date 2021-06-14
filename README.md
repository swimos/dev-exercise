Swim Interview Exercise
=======================

Introduction
------------
For this exercise, you will be writing a very simple client/server network application. Preferably, your solution will be written in Java (or some other JVM language) but any language that allows you to work directly with TCP sockets is fine.

It should not be necessary for you to use any third-party libraries, but you are welcome to do so, if you wish.

The application is deliberately trivial, and the exercise is intended to show how you think and work. Your code does not need to be "production quality" and does not need to be configurable, have logging etc. However, it should have automated test cases, handle common error cases gracefully, and more complex parts should be commented.


Protocol
--------

The application consists of two components, a client and a server. There will be one instance of the server but there may be multiple instances of the client connected to the same server. The clients and server communicate over TCP. The clients are stateless, and the state of the server is a single signed, 64-bit integer. The initial value of the state is 0.

The messages that can be sent from the client to the server are as follows:

- Set the state: `{ "command": "set", "value": 2 }`.
- Get the state: `{ "command": "get" }`.
- Subscribe to the state: `{ "command": "subscribe" }`.

The server can reply with the following:

- Response: `{ "value", 2 }`.

In both cases the value can be any, decimal 64-bit integer.

All of these messages are JSON documents, but you can simply treat them as strings. Messages should be delimited by new-line characters.

Handling Messages
-----------------

Upon receipt of a message, the server will do the following:
- When a "set" message is received, the server will update the value of the state and broadcast the value to all clients that are subscribed.
- When a "get" message is received, the server will send the current value to the client that requested it.
- When a "subscribe" message is received, the server will add that client to the list of subscriptions.

Server Application
------------------

The server should be a command line application that has no user interaction after it has started. The port on which it listens may be hard-coded. It should be possible to stop the application by pressing `Ctrl-C`.

Client Application
------------------

The client should be a command line application that takes a parameter to indicate the server/port to connect to. It should read commands as lines from STDIN to be sent to the server and responses from the server should be written to STDOUT.

You should decide on how invalid messages are handled by both the client and the server.

Example Sessions
----------------

Single Client:
```
> { "command": "get" }
{ "value": 0 }
> { "command": "set", "value": 5 }
> { "command": "get" }
{ "value": 5 }
```

Multiple Clients:

Client 1

(Here the 'subscribe' command is executed before clients 2 and 3 are started.)

```
> { "command": "subscribe" }
{ "value": 1 }
{ "value": 2 }
{ "value": 3 }
{ "value": 4 }
{ "value": 5 }
{ "value": 6 }
```

Client 2
```
> { "command": "set", "value": 1 }
> { "command": "set", "value": 3 }
> { "command": "set", "value": 5 }
```

Client 3
```
> { "command": "set", "value": 2 }
> { "command": "set", "value": 4 }
> { "command": "set", "value": 6 }
```

(Note that the exact message and order received would vary depending on the order the commands are entered on the clients).

Sharing the Solution
--------------------
Please place your solution under git source control somewhere it can be shared with us (for example Github). You should have instructions on who should be given access in the e-mail that directed you to these instructions.

Please give instructions on how your application should be executed, in a README file in the repository.

Priority should be given to correctness, robustness and clarity.
