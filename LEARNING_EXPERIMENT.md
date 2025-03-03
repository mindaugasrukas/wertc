---
title: Learning Experiment
permalink: /learning-experiment/
---

# Learning Experiment: Limits of Cross-Browser Communication

This is a living document. It will be updated as we learn more. For tracking changes, please follow the [git repository](https://github.com/mindaugasrukas/wertc).

## Introduction

Once, I was discussing the limits of cross-browser communication.
The standard approach is to use a server as a mediator—commonly known as the server-client architecture.
However, we started wondering: can we create a cross-browser application that works without a server?

Hence this learning experiment was born.

Where could this lead? Or more intriguingly — what could go wrong?

## Goal

The primary goal of this experiment is to explore the limits of direct browser-to-browser communication without relying on a server.

As a practical example, we considered building a chat application that:
* Works on modern browsers.
* Runs as a standalone HTML file.
* Does not require deployment on a server.
* Enables direct peer-to-peer (P2P) communication between users.
* Does not rely on external dependencies (if possible).
* Allows users to send messages to each other.

## Initial requirements

To keep the experiment focused, we defined the following requirements:

* The application should work in modern browsers (Chrome, Firefox, Safari, Edge).
* The application should be distributable as a single HTML file.
* It should have minimal external dependencies, relying only on built-in browser APIs.
* It should work without a dedicated server.

## Step 1: Questions to answer

Before we start, we need to answer some questions:

1. What existing technologies allow applications to run from a single HTML file?
2. What existing technologies enable direct communication between browsers?

### 1.1: What Technologies Enable Single-File Applications?

Modern browsers are powerful application platforms. They have built-in APIs and support:

* JavaScript (the core language for browser-based applications).
* IndexedDB, Local Storage, and Service Workers (for local data storage and offline capabilities).
* Web Workers (for multi-threading within the browser).
* Web Components (for modular UI design).

This allows developers to build fully functional applications that run directly from a single HTML file.

### 1.2: What Technologies Enable Direct Browser-to-Browser Communication?

WebRTC (Web Real-Time Communication) is a built-in browser API designed for direct P2P communication. It enables:
* Audio & video streaming
* Data transfer (files, messages, etc.)
* Low-latency networking

Unlike traditional web communication (which requires a server), WebRTC allows browsers to communicate directly with each other.

## Step 2: Build a simple prototype

Everything sounds simple:

* Build a JavaScript application that uses WebRTC.
* Allow browsers to communicate directly with each other.

However, WebRTC has a major limitation (in this project context): it requires a signaling server to exchange connection information between peers. While WebRTC does not require a server for actual data transfer, it needs one for peer discovery.

### Challenges to Solve

* How do peers discover each other?
* How do they exchange connection information?
* How do we handle NAT traversal?

At this stage, we will ignore all these challenges and focus on getting something simple to work first.

## Prototype: WebRTC-Based P2P Chat (Simplified Version)

Share your Peer ID with a friend to connect and start chatting!

```
<!DOCTYPE html>
<html lang="en">
<head>
    <title>P2P Chat Experiment</title>
    <script src="https://unpkg.com/peerjs@1.5.4/dist/peerjs.min.js"></script>
</head>
<body>
    <h2>P2P Chat</h2>
    <p>Your Peer ID: <span id="peerId">Loading...</span></p>
    <input type="text" id="remoteId" placeholder="Enter Peer ID">
    <button onclick="connectToPeer()">Connect</button>

    <div id="chat"></div>
    <input type="text" id="message" placeholder="Type a message...">
    <button onclick="sendMessage()">Send</button>

    <script>
        var peer = new Peer();
        let conn;

        peer.on("open", id => {
            document.getElementById("peerId").innerText = id;
        });

        peer.on("connection", connection => {
            conn = connection;
            setupConnection();
        });

        function connectToPeer() {
            const remoteId = document.getElementById("remoteId").value;
            conn = peer.connect(remoteId);
            setupConnection();
        }

        function setupConnection() {
            conn.on("open", () => {
                displayMessage("Connected to friend: " + conn.peer);
                conn.on("data", (data) => displayMessage("Friend: " + data));
            });
        }

        function sendMessage() {
            const message = document.getElementById("message").value;
            if (conn) {
                conn.send(message);
                displayMessage("You: " + message);
                document.getElementById("message").value = "";
            }
        }

        function displayMessage(msg) {
            const chat = document.getElementById("chat");
            chat.innerHTML += "<p>" + msg + "</p>";
        }
    </script>
</body>
</html>
```

### How does it work?

We are using the PeerJS library to simplify the WebRTC connection process, but we also could use the native WebRTC API.
This library provides a signaling server that helps peers discover each other.

Here's an architectural diagram of the application:

```
+-----------------+        +-----------------+
|                 |   (3)  |                 |
|   Browser A     |<------>|   Browser B     |
|                 |        |                 |
+--------+--------+        +--------+--------+
        |                          |
        |                          |
    (1) |   +-----------------+    | (2)
        |   |                 |    |
        --->|   STUN Server   |<----
            |                 |
            +--------+--------+
```

1) Browser A connects to a STUN server to get its public IP address and port.
2) Browser B connects to the same STUN server to get its public IP address and port.
3) Browser A and Browser B exchange connection information via the signaling server and establish a direct P2P connection.

### Limitations

We imeediately observed some limitations:

* WebRTC requires a signaling server for peer discovery. Can we avoid this?
* After testing it out it doesn't work on Chrome out of the box, because of security restrictions. Can we work around this?
* The chat is not persistent. If you refresh the page, you lose the chat history. How and where can we store messages?
* Both peers must be online at the same time to chat. Can we implement offline messaging?
* Requires a public IP and port for direct connections. It doens't work behind hard firewalls or NATs. Can we implement NAT traversal?

## Step 3: Addressing Limitations

###  Avoiding the Signaling Server

WebRTC allows peers to exchange connection information directly without a signaling server.
However, this requires a way for peers to discover each other and exchange initial connection details.
For example, peers could share their connection details via a third-party service (like email, SMS, or QR codes).

See the [prototype](chat_copy_paste.html) for a simple example of how this could work.
