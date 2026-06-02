# ai-chat-app
Personal AI Chat Assistant - Installable PWA with OpenAI API
Personal AI Chat Assistant - Installable PWA with OpenAI API<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
    <meta name="theme-color" content="#007bff">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="AI Chat">
    <link rel="manifest" href="manifest.json">
    <link rel="apple-touch-icon" href="icon-192.png">
    <title>AI Chat Assistant</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            height: 100vh;
            overflow: hidden;
        }

        .app-container {
            max-width: 800px;
            margin: 0 auto;
            height: 100vh;
            display: flex;
            flex-direction: column;
            background: #f5f5f5;
            box-shadow: 0 0 20px rgba(0,0,0,0.1);
        }

        /* Header */
        .header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 20px;
            text-align: center;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }

        .header h1 {
            font-size: 1.5rem;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
        }

        .status {
            font-size: 0.75rem;
            opacity: 0.9;
            margin-top: 5px;
        }

        /* Chat Area */
        .chat-area {
            flex: 1;
            overflow-y: auto;
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        /* Message Bubbles */
        .message {
            max-width: 80%;
            padding: 12px 16px;
            border-radius: 18px;
            animation: fadeIn 0.3s ease;
        }

        @keyframes fadeIn {
            from {
                opacity: 0;
                transform: translateY(10px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .user-message {
            background: #007bff;
            color: white;
            align-self: flex-end;
            border-bottom-right-radius: 4px;
        }

        .ai-message {
            background: white;
            color: #333;
            align-self: flex-start;
            border-bottom-left-radius: 4px;
            box-shadow: 0 1px 2px rgba(0,0,0,0.1);
        }

        .typing-indicator {
            background: white;
            padding: 12px 16px;
            border-radius: 18px;
            align-self: flex-start;
            display: none;
        }

        .typing-dot {
            display: inline-block;
            width: 8px;
            height: 8px;
            border-radius: 50%;
            background: #999;
            margin: 0 2px;
            animation: typing 1.4s infinite;
        }

        .typing-dot:nth-child(2) { animation-delay: 0.2s; }
        .typing-dot:nth-child(3) { animation-delay: 0.4s; }

        @keyframes typing {
            0%, 60%, 100% { transform: translateY(0); opacity: 0.4; }
            30% { transform: translateY(-10px); opacity: 1; }
        }

        /* Input Area */
        .input-area {
            background: white;
            padding: 15px 20px;
            border-top: 1px solid #e0e0e0;
            display: flex;
            gap: 10px;
            align-items: center;
        }

        .input-wrapper {
            flex: 1;
            display: flex;
            gap: 10px;
        }

        input {
            flex: 1;
            padding: 12px 16px;
            border: 1px solid #e0e0e0;
            border-radius: 25px;
            font-size: 16px;
            outline: none;
            transition: border-color 0.3s;
        }

        input:focus {
            border-color: #667eea;
        }

        button {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 12px 20px;
            border-radius: 25px;
            font-size: 16px;
            cursor: pointer;
            transition: transform 0.2s, opacity 0.2s;
        }

        button:active {
            transform: scale(0.95);
        }

        button:disabled {
            opacity: 0.6;
            cursor: not-allowed;
        }

        /* Controls */
        .controls {
            display: flex;
            gap: 10px;
            margin-left: 10px;
        }

        .icon-btn {
            background: #f0f0f0;
            color: #666;
            padding: 10px;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        /* Scrollbar */
        .chat-area::-webkit-scrollbar {
            width: 6px;
        }

        .chat-area::-webkit-scrollbar-track {
            background: #f1f1f1;
        }

        .chat-area::-webkit-scrollbar-thumb {
            background: #888;
            border-radius: 3px;
        }

        /* Install Prompt */
        .install-prompt {
            position: fixed;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: white;
            padding: 15px 20px;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15);
            display: none;
            align-items: center;
            gap: 15px;
            z-index: 1000;
            animation: slideUp 0.3s ease;
        }

        @keyframes slideUp {
            from {
                transform: translateX(-50%) translateY(100px);
                opacity: 0;
            }
            to {
                transform: translateX(-50%) translateY(0);
                opacity: 1;
            }
        }

        .install-prompt button {
            padding: 8px 16px;
            font-size: 14px;
        }

        @media (max-width: 600px) {
            .message {
                max-width: 90%;
            }
            
            .controls {
                display: none;
            }
        }
    </style>
</head>
<body>
    <div class="app-container">
        <div class="header">
            <h1>
                🤖 AI Chat Assistant
            </h1>
            <div class="status" id="status">● Ready to chat</div>
        </div>

        <div class="chat-area" id="chatArea">
            <div class="message ai-message">
                Hello! I'm your AI assistant. Ask me anything! 🚀
            </div>
        </div>

        <div class="typing-indicator" id="typingIndicator">
            <span class="typing-dot"></span>
            <span class="typing-dot"></span>
            <span class="typing-dot"></span>
        </div>

        <div class="input-area">
            <div class="input-wrapper">
                <input type="text" id="userInput" placeholder="Type your message..." autocomplete="off">
                <button id="sendBtn">Send</button>
            </div>
            <div class="controls">
                <button class="icon-btn" id="clearBtn" title="Clear chat">🗑️</button>
                <button class="icon-btn" id="exportBtn" title="Export chat">📥</button>
            </div>
        </div>
    </div>

    <div class="install-prompt" id="installPrompt">
        <span>📱 Install this app on your phone!</span>
        <button id="installBtn">Install</button>
        <button id="closeInstallBtn">✕</button>
    </div>

    <script src="app.js"></script>
</body>
</html>// AI Chat App - Complete JavaScript

// Configuration
const API_KEY = 'YOUR_OPENAI_API_KEY_HERE'; // Replace with your actual API key
let conversationHistory = [];
let isProcessing = false;
let deferredPrompt;

// DOM Elements
const chatArea = document.getElementById('chatArea');
const userInput = document.getElementById('userInput');
const sendBtn = document.getElementById('sendBtn');
const clearBtn = document.getElementById('clearBtn');
const exportBtn = document.getElementById('exportBtn');
const typingIndicator = document.getElementById('typingIndicator');
const statusDiv = document.getElementById('status');

// Load saved conversation from localStorage
function loadConversation() {
    const saved = localStorage.getItem('chatHistory');
    if (saved) {
        conversationHistory = JSON.parse(saved);
        conversationHistory.forEach(msg => {
            displayMessage(msg.role, msg.content);
        });
    }
}

// Save conversation to localStorage
function saveConversation() {
    localStorage.setItem('chatHistory', JSON.stringify(conversationHistory));
}

// Display message in chat
function displayMessage(role, content) {
    const messageDiv = document.createElement('div');
    messageDiv.className = `message ${role}-message`;
    messageDiv.innerHTML = content.replace(/\n/g, '<br>');
    chatArea.appendChild(messageDiv);
    chatArea.scrollTop = chatArea.scrollHeight;
}

// Show typing indicator
function showTyping() {
    typingIndicator.style.display = 'flex';
    chatArea.scrollTop = chatArea.scrollHeight;
}

// Hide typing indicator
function hideTyping() {
    typingIndicator.style.display = 'none';
}

// Update status
function updateStatus(text, isError = false) {
    statusDiv.innerHTML = isError ? `⚠️ ${text}` : `● ${text}`;
    statusDiv.style.color = isError ? '#ff6b6b' : '';
    setTimeout(() => {
        if (!isError) statusDiv.style.color = '';
    }, 3000);
}

// Send message to AI (using OpenRouter for free tier or OpenAI)
async function sendToAI(message) {
    // Option 1: Use OpenRouter (has free tier, no credit card needed)
    // Sign up at https://openrouter.ai/ to get free API key
    
    const useOpenRouter = true; // Set to false if using OpenAI
    
    if (useOpenRouter) {
        // OpenRouter API (Free tier available)
        const response = await fetch('https://openrouter.ai/api/v1/chat/completions', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${API_KEY}`,
                'HTTP-Referer': window.location.origin,
                'X-Title': 'AI Chat App'
            },
            body: JSON.stringify({
                model: 'mistralai/mistral-7b-instruct:free', // Free model
                messages: [
                    { role: 'system', content: 'You are a helpful AI assistant. Be concise and friendly.' },
                    ...conversationHistory
                ],
                temperature: 0.7,
                max_tokens: 500
            })
        });
        
        if (!response.ok) throw new Error('API request failed');
        const data = await response.json();
        return data.choices[0].message.content;
    } else {
        // Option 2: OpenAI API (requires paid account)
        const response = await fetch('https://api.openai.com/v1/chat/completions', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${API_KEY}`
            },
            body: JSON.stringify({
                model: 'gpt-3.5-turbo',
                messages: [
                    { role: 'system', content: 'You are a helpful AI assistant.' },
                    ...conversationHistory
                ],
                temperature: 0.7,
                max_tokens: 500
            })
        });
        
        if (!response.ok) throw new Error('API request failed');
        const data = await response.json();
        return data.choices[0].message.content;
    }
}

// Handle sending message
async function sendMessage() {
    const message = userInput.value.trim();
    if (!message || isProcessing) return;
    
    // Add user message
    conversationHistory.push({ role: 'user', content: message });
    displayMessage('user', message);
    saveConversation();
    
    // Clear input
    userInput.value = '';
    isProcessing = true;
    sendBtn.disabled = true;
    showTyping();
    updateStatus('AI is thinking...');
    
    try {
        const aiResponse = await sendToAI(message);
        
        // Add AI response
        conversationHistory.push({ role: 'assistant', content: aiResponse });
        displayMessage('ai', aiResponse);
        saveConversation();
        
        updateStatus('Ready');
    } catch (error) {
        console.error('Error:', error);
        updateStatus('Connection error. Check API key.', true);
        displayMessage('ai', '⚠️ Sorry, I encountered an error. Please check your API key and try again.');
    } finally {
        isProcessing = false;
        sendBtn.disabled = false;
        hideTyping();
        userInput.focus();
    }
}

// Clear chat history
function clearChat() {
    if (confirm('Clear all messages?')) {
        conversationHistory = [];
        chatArea.innerHTML = '';
        displayMessage('ai', 'Chat cleared! How can I help you? 🚀');
        saveConversation();
        updateStatus('Chat cleared');
    }
}

// Export chat
function exportChat() {
    if (conversationHistory.length === 0) {
        alert('No messages to export');
        return;
    }
    
    let exportText = 'AI Chat Conversation Export\n';
    exportText += '=' .repeat(50) + '\n\n';
    exportText += `Date: ${new Date().toLocaleString()}\n\n`;
    
    conversationHistory.forEach(msg => {
        const role = msg.role === 'user' ? 'YOU' : 'AI';
        exportText += `${role}:\n${msg.content}\n\n${'-'.repeat(40)}\n\n`;
    });
    
    // Download as text file
    const blob = new Blob([exportText], { type: 'text/plain' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = `chat_export_${Date.now()}.txt`;
    a.click();
    URL.revokeObjectURL(url);
    
    updateStatus('Chat exported');
}

// Handle Enter key
userInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter' && !e.shiftKey) {
        e.preventDefault();
        sendMessage();
    }
});

sendBtn.addEventListener('click', sendMessage);
clearBtn.addEventListener('click', clearChat);
exportBtn.addEventListener('click', exportChat);

// PWA Installation
window.addEventListener('beforeinstallprompt', (e) => {
    e.preventDefault();
    deferredPrompt = e;
    const installPrompt = document.getElementById('installPrompt');
    installPrompt.style.display = 'flex';
});

document.getElementById('installBtn').addEventListener('click', async () => {
    if (deferredPrompt) {
        deferredPrompt.prompt();
        const { outcome } = await deferredPrompt.userChoice;
        if (outcome === 'accepted') {
            console.log('App installed');
        }
        deferredPrompt = null;
        document.getElementById('installPrompt').style.display = 'none';
    }
});

document.getElementById('closeInstallBtn').addEventListener('click', () => {
    document.getElementById('installPrompt').style.display = 'none';
});

// Load saved conversation on startup
loadConversation();

// Focus input
userInput.focus();

// Service Worker registration for offline capability
if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('/sw.js').then(reg => {
        console.log('Service Worker registered');
    }).catch(err => {
        console.log('Service Worker registration failed:', err);
    });
}{
    "name": "AI Chat Assistant",
    "short_name": "AIChat",
    "description": "Personal AI Chat Assistant",
    "start_url": "/",
    "display": "standalone",
    "theme_color": "#667eea",
    "background_color": "#ffffff",
    "orientation": "portrait",
    "icons": [
        {
            "src": "icon-72.png",
            "sizes": "72x72",
            "type": "image/png",
            "purpose": "any maskable"
        },
        {
            "src": "icon-96.png",
            "sizes": "96x96",
            "type": "image/png"
        },
        {
            "src": "icon-128.png",
            "sizes": "128x128",
            "type": "image/png"
        },
        {
            "src": "icon-144.png",
            "sizes": "144x144",
            "type": "image/png"
        },
        {
            "src": "icon-152.png",
            "sizes": "152x152",
            "type": "image/png"
        },
        {
            "src": "icon-192.png",
            "sizes": "192x192",
            "type": "image/png"
        },
        {
            "src": "icon-384.png",
            "sizes": "384x384",
            "type": "image/png"
        },
        {
            "src": "icon-512.png",
            "sizes": "512x512",
            "type": "image/png"
        }
    ],
    "screenshots": [
        {
            "src": "screenshot1.png",
            "sizes": "1080x1920",
            "type": "image/png"
        }
    ],
    "shortcuts": [
        {
            "name": "New Chat",
            "short_name": "New",
            "description": "Start a new conversation",
            "url": "/?new=true",
            "icons": [{ "src": "icon-96.png", "sizes": "96x96" }]
        }
    ]
}
const CACHE_NAME = 'ai-chat-v1';
const urlsToCache = [
    '/',
    '/index.html',
    '/app.js',
    '/manifest.json'
];

// Install service worker
self.addEventListener('install', event => {
    event.waitUntil(
        caches.open(CACHE_NAME)
            .then(cache => {
                console.log('Opened cache');
                return cache.addAll(urlsToCache);
            })
    );
});

// Fetch and cache
self.addEventListener('fetch', event => {
    event.respondWith(
        caches.match(event.request)
            .then(response => {
                // Cache hit - return response
                if (response) {
                    return response;
                }
                
                // Clone request
                const fetchRequest = event.request.clone();
                
                return fetch(fetchRequest).then(
                    response => {
                        // Check if valid response
                        if (!response || response.status !== 200 || response.type !== 'basic') {
                            return response;
                        }
                        
                        // Clone response
                        const responseToCache = response.clone();
                        
                        caches.open(CACHE_NAME)
                            .then(cache => {
                                cache.put(event.request, responseToCache);
                            });
                            
                        return response;
                    }
                );
            })
    );
});

// Clean up old caches
self.addEventListener('activate', event => {
    const cacheWhitelist = [CACHE_NAME];
    event.waitUntil(
        caches.keys().then(cacheNames => {
            return Promise.all(
                cacheNames.map(cacheName => {
                    if (cacheWhitelist.indexOf(cacheName) === -1) {
                        return caches.delete(cacheName);
                    }
                })
            );
        })
    );
});
