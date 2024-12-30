# A-S
A application for chting and sharing images,and need camera access only for two persons 
// App.js
import React, { useState, useEffect } from 'react';

function App() {
  const [messages, setMessages] = useState([]);
  const [message, setMessage] = useState('');
  const [image, setImage] = useState(null);
  const [cameraOpen, setCameraOpen] = useState(false);
  const [stream, setStream] = useState(null);

  useEffect(() => {
    // Connect to WebSocket server and handle messages
    const socket = new WebSocket('ws://localhost:3000');
    socket.onmessage = (event) => {
      const newMessage = JSON.parse(event.data);
      setMessages((prevMessages) => [...prevMessages, newMessage]);
    };
  }, []);

  const sendMessage = () => {
    // Send message to WebSocket server
    const socket = new WebSocket('ws://localhost:3000');
    socket.send(JSON.stringify({ message, image }));
    setMessage('');
    setImage(null);
  };

  const handleImageUpload = (e) => {
    setImage(e.target.files[0]);
  };

  const openCamera = async () => {
    const stream = await navigator.mediaDevices.getUserMedia({ video: true });
    setStream(stream);
    setCameraOpen(true);
  };

  const closeCamera = () => {
    stream.getTracks().forEach((track) => track.stop());
    setStream(null);
    setCameraOpen(false);
  };

  return (
    <div>
      <h1>Chat Application</h1>
      <div>
        {messages.map((msg, index) => (
          <div key={index}>{msg.message}</div>
        ))}
      </div>
      <input
        type="text"
        value={message}
        onChange={(e) => setMessage(e.target.value)}
      />
      <button onClick={sendMessage}>Send</button>
      <input type="file" onChange={handleImageUpload} />
      <button onClick={openCamera}>Open Camera</button>
      {cameraOpen && <video autoPlay srcObject={stream}></video>}
      <button onClick={closeCamera}>Close Camera</button>
    </div>
  );
}

export default App;
