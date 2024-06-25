In this project, the Arduino framework is not strictly necessary, but using it can simplify the development process for the ESP32 microcontroller. The Arduino framework provides a simplified programming environment and a vast library ecosystem that can make tasks such as handling ultrasonic sensors and managing WiFi connections easier.

### Using Arduino Framework for ESP32

By using the Arduino framework with the ESP32, you can leverage existing libraries and familiar functions. Below are the steps and code updates needed to integrate the Arduino framework into the project.

#### 1. Setting Up the Arduino Environment for ESP32

1. **Install Arduino IDE:**
   Download and install the Arduino IDE from the [official Arduino website](https://www.arduino.cc/en/software).

2. **Add ESP32 Board Support:**
   - Open the Arduino IDE.
   - Go to `File` > `Preferences`.
   - In the "Additional Board Manager URLs" field, add the following URL: `https://dl.espressif.com/dl/package_esp32_index.json`
   - Go to `Tools` > `Board` > `Boards Manager`.
   - Search for "ESP32" and install the "esp32" package by Espressif Systems.

3. **Select the ESP32 Board:**
   - Go to `Tools` > `Board` and select the appropriate ESP32 board (e.g., "ESP32 Dev Module").

#### 2. Writing the Firmware

The firmware will handle the ultrasonic sensors, process distance data, and communicate with the Electron app via WebSocket.

**Required Libraries:**
- `WiFi.h` for network connectivity
- `WebSocketsServer.h` for WebSocket communication
- `NewPing.h` for ultrasonic sensor handling

**Sample Arduino Code:**

```cpp
#include <WiFi.h>
#include <WebSocketsServer.h>
#include <NewPing.h>

#define TRIGGER_PIN_1  5
#define ECHO_PIN_1     18
#define TRIGGER_PIN_2  19
#define ECHO_PIN_2     21
#define MAX_DISTANCE 200

NewPing sonar1(TRIGGER_PIN_1, ECHO_PIN_1, MAX_DISTANCE);
NewPing sonar2(TRIGGER_PIN_2, ECHO_PIN_2, MAX_DISTANCE);

WebSocketsServer webSocket = WebSocketsServer(81);

const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  
  Serial.println("Connected to WiFi");
  webSocket.begin();
  webSocket.onEvent(webSocketEvent);
}

void loop() {
  webSocket.loop();
  int distance1 = sonar1.ping_cm();
  int distance2 = sonar2.ping_cm();

  String message = "{\"sensor1\":" + String(distance1) + ",\"sensor2\":" + String(distance2) + "}";
  webSocket.broadcastTXT(message);
  
  delay(100); // Adjust delay as necessary
}

void webSocketEvent(uint8_t num, WStype_t type, uint8_t * payload, size_t length) {
  if (type == WStype_TEXT) {
    Serial.printf("[%u] get Text: %s\n", num, payload);
  }
}
```

### 3. Server-Side Development

No changes are needed in the server-side development. The Node.js server using Express and SQLite remains the same as outlined previously.

### 4. Electron App Development

The Electron app code remains the same. It will connect to the WebSocket server running on the ESP32 and display real-time distance data.

**Main Process:**

```javascript
const { app, BrowserWindow } = require('electron');
const path = require('path');

function createWindow () {
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  });

  mainWindow.loadFile('index.html');
}

app.on('ready', createWindow);
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit();
  }
});
app.on('activate', () => {
  if (BrowserWindow.getAllWindows().length === 0) {
    createWindow();
  }
});
```

**Renderer Process:**

```javascript
const WebSocket = require('ws');
const ws = new WebSocket('ws://ESP32_IP_ADDRESS:81');

ws.onmessage = (message) => {
  const data = JSON.parse(message.data);
  document.getElementById('distance1').innerText = `Distance 1: ${data.sensor1} cm`;
  document.getElementById('distance2').innerText = `Distance 2: ${data.sensor2} cm`;

  // Add graphical representation and alerts logic here
};

document.getElementById('loginButton').addEventListener('click', () => {
  const username = document.getElementById('username').value;
  const password = document.getElementById('password').value;

  fetch('http://localhost:3000/login', {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({username, password})
  })
  .then(response => response.json())
  .then(data => {
    if (data.token) {
      // Store token and proceed
    } else {
      alert('Login failed');
    }
  });
});
```

### Conclusion

Using the Arduino framework simplifies the development process for the ESP32 by providing a familiar environment and access to a wide range of libraries. This approach integrates the Arduino environment for the ESP32 with the necessary firmware, server-side components, and Electron app to create a comprehensive parking assistance system.
