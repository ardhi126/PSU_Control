# Web Serial Power Supply Controller

A zero-dependency, browser-based user interface for communicating with Modbus RTU power supplies (like the Wuzhi SK150Pro) over the Web Serial API.

## Features
* **Zero Dependencies**: Pure Vanilla HTML, CSS, and JS. No React, no NPM, no build steps. 
* **Browser Native Serial**: Communicates directly from your browser to USB/Bluetooth COM ports via the `navigator.serial` API.
* **Auto-Reconnect Loop**: Handles spontaneous physical disconnections gracefully without requiring page reloads.
* **Live Oscilloscope Graphing**: Hardware-accelerated HTML5 `<canvas>` graphing with auto-scaling capabilities.
* **Data Export**: Export your recorded graph sessions directly to CSV.
* **Fully Responsive**: Collapses sidebars and rearranges the grid for mobile usage.

## How it Works (Communication Protocol)
The application acts as a Modbus Master, formatting binary request frames and verifying CRC-16 checksums before dispatching them over the Web Serial stream. 

It heavily utilizes two Modbus function codes:
* `0x03` (Read Holding Registers): Sent in batches to minimize serial bus congestion.
* `0x06` (Write Single Register): Dispatched asynchronously using an internal command queue to ensure write commands don't collide with the aggressive background polling loop.

## Architecture: How to Support Other Devices
To comply with specific deployment restrictions, this entire application runs from a single `index.html` file. However, the JavaScript internally uses a strict Object-Oriented paradigm. 

The DOM manipulation (`UIController`), the Serial/Modbus queueing (`ModbusSerial`), and the canvas (`GraphEngine`) are completely decoupled from the specific device register maps.

If you wish to use this UI for a completely different serial device, you do **not** need to rewrite the UI. 
1. Open `index.html`.
2. Locate the `SK150Device` class.
3. Write a new class (e.g., `class MyNewDevice`) that implements `.startPolling()`, `.parseResponse()`, and your specific write functions.
4. Swap the class instantiation at the bottom of the file:
   ```javascript
   // Change this:
   window.app.device = new SK150Device(window.app.comm, window.app.ui, window.app.graph);
   // To this:
   window.app.device = new MyNewDevice(window.app.comm, window.app.ui, window.app.graph);
   ```

## Disclaimer
*This code was generated with the assistance of an AI large language model. While the application is functional and the Modbus implementation strictly adheres to general standards, you should exercise caution when using automated tools to control high-power electrical hardware. Always supervise the equipment during operation.*
