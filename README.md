# websocket_basic

a simple WebSocket tutorial using React for the frontend and Node.js with the ws library for the backend. This will guide junior developers through setting up real-time communication between a client and a server.

----
## 1. Setting Up the Backend with Node.js
We'll create a WebSocket server using the ws library.

### Step 1: Initialize the Node.js project
```
mkdir websocket-server
cd websocket-server
npm init -y
npm install ws
```

### Step 2: Create the WebSocket server
Create a file named `server.js`:

```js
const WebSocket = require('ws');

// Create a WebSocket server on port 8080
const wss = new WebSocket.Server({ port: 8080 });

console.log('WebSocket server started on ws://localhost:8080');

// Listen for connection events
wss.on('connection', (ws) => {
  console.log('New client connected');

  // Send a welcome message to the client
  ws.send('Welcome to the WebSocket server!');

  // Listen for messages from the client
  ws.on('message', (message) => {
    console.log(`Received message => ${message}`);

    // Broadcast the message to all connected clients
    wss.clients.forEach((client) => {
      if (client.readyState === WebSocket.OPEN) {
        client.send(`Server received: ${message}`);
      }
    });
  });

  // Handle client disconnection
  ws.on('close', () => {
    console.log('Client disconnected');
  });
});
```

#### Run the server:
````
node server.js
````

----

## 2. Setting Up the Frontend with React
We'll create a simple React app that connects to the WebSocket server.

### Step 1: Create a new React project

````bash

npx create-react-app websocket-client
cd websocket-client
npm start

````
### Step 2: Implement WebSocket in React
Open src/App.js and replace the content with:

````javascript
import React, { useState, useEffect, useRef } from 'react';

function App() {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState('');
  const ws = useRef(null);

  useEffect(() => {
    // Connect to WebSocket server
    ws.current = new WebSocket('ws://localhost:8080');

    ws.current.onopen = () => {
      console.log('Connected to WebSocket server');
    };

    ws.current.onmessage = (event) => {
      setMessages((prevMessages) => [...prevMessages, event.data]);
    };

    ws.current.onclose = () => {
      console.log('Disconnected from WebSocket server');
    };

    // Cleanup on component unmount
    return () => {
      ws.current.close();
    };
  }, []);

  const sendMessage = () => {
    if (ws.current.readyState === WebSocket.OPEN && input.trim() !== '') {
      ws.current.send(input);
      setInput('');
    }
  };

  return (
    <div className="App" style={{ padding: '20px', fontFamily: 'Arial' }}>
      <h1>WebSocket Chat</h1>

      <div style={{ border: '1px solid #ccc', padding: '10px', height: '200px', overflowY: 'scroll', marginBottom: '10px' }}>
        {messages.map((msg, index) => (
          <div key={index}>{msg}</div>
        ))}
      </div>

      <input
        type="text"
        value={input}
        onChange={(e) => setInput(e.target.value)}
        onKeyPress={(e) => e.key === 'Enter' && sendMessage()}
        placeholder="Type a message..."
        style={{ padding: '10px', width: '70%', marginRight: '10px' }}
      />
      <button onClick={sendMessage} style={{ padding: '10px' }}>
        Send
      </button>
    </div>
  );
}

export default App;
````
----
## 3. Running the Application
### 1. Start the WebSocket Server:
```` bash
node server.js
````

### 2.Run the React App:
````bash
npm start
````

### 3. Open your browser at http://localhost:3000, and you should see a simple chat interface. Open multiple tabs to see real-time messaging!

----

## Key Concepts Explained:
- **WebSocket Connection:** In React, we use useRef to hold the WebSocket instance and manage its lifecycle with useEffect.
- **Message Handling:** The server listens for messages and broadcasts them to all connected clients.
- **Real-time Updates:** The frontend updates the chat in real-time when new messages are received.
