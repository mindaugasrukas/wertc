# Learning Experiment: Limits of Cross-Browser Communication
This is a living document. It will be updated as we learn more. For tracking changes, please follow the [git repository](https://github.com/mindaugasrukas/wertc).

## Introduction
Once I was discussing about the limits of cross-browser communication.
The standard approach is to use a server as a mediator - a.k.a. server-client architecture.
However, we were curious can we create a cross-browser application that works without a server.

Hence this learning-experiment was born.

Where this could go or "what could go wrong"?

## Goal
The goal is to learn the limits of direct communication between browsers without a server.
As an example application we were wondering if it's possible to create a chat application that works on modern browsers, without deployment on a server (like a static HTML file), where communication happens directly between browsers. The chat application should allow users to send messages to each other.

## Initial requirements

* The application should work in modern browsers (Chrome, Firefox, Safari, Edge).
* Application should be distributable as a single HTML file.
* It should have as fewer external dependencies as possible - if possible rely only on the browser's built-in APIs.
* It should work without a server.

## Step 1: Questions to answer
Before we start, we need to answer some questions:

1. What are the existing technologies for applications that works from a single HTML file?
2. What are the existing technologies for direct communication between browsers?

### 1.1: What are the existing technologies for applications that works from a single HTML file?
Browsers are powerful application platforms. They have built-in APIs (like IndexedDB, Web Storage, Service Workers, Web Workers, etc.), they can run JavaScript code, and they can render HTML and CSS. That allows developers to create applications that works locally from a single HTML file.

The core technology here is JavaScript.

JavaScript applications can send network requests, read and write data to local browser storage, and do many other things.

### 1.2: What are the existing technologies for direct communication between browsers?
Browsers have built-in APIs for direct communication between browsers - WebRTC. It allows browsers to communicate directly with each other.

## Step 2: Build a simple prototype
Everything sounds simple: build a JavaScript application that uses WebRTC to communicate directly between browsers.

WebRTC requires a signaling server to exchange connection information between peers. While WebRTC doesn't require a server to send data between peers (ignoring NAT cases), it requires a server for peer discovery.

### Challenges:
* How to discover other peers?
* How to exchange connection information between peers?
* How to handle NAT traversal?

This is a simple prototype ignoring all the challenges. To start somewhere.

```
    // TODO: code here
```
