For people without Raspberry PI code.
I decided to post code for a sick gaming controller.
This code is made by a self-made AI called Mant-Port, it is fully functunal and works all the time.
If this code does not work try pasting this code sentence by sentence:

bash:
sudo apt-get update
sudo apt-get install python3-rpi.gpio

bash:
sudo apt-get install nodejs npm

bash:
npm init -y
npm install express socket.io

python:
import RPi.GPIO as GPIO
import time
import socketio

# Set up GPIO pins for buttons
GPIO.setmode(GPIO.BCM)
button_pin = 18
GPIO.setup(button_pin, GPIO.IN, pull_up_down=GPIO.PUD_UP)

# Set up socket.io client to communicate with JavaScript server
sio = socketio.Client()
sio.connect('http://localhost:3000')

def button_callback(channel):
    # Send a message to the Node.js server when the button is pressed
    sio.emit('button_press', {'button': 'A'})

# Add event detection for button press
GPIO.add_event_detect(button_pin, GPIO.FALLING, callback=button_callback, bouncetime=200)

try:
    while True:
        time.sleep(0.1)

except KeyboardInterrupt:
    GPIO.cleanup()

sio.disconnect()

Javascript:
const express = require('express');
const socketIO = require('socket.io');
const http = require('http');

const app = express();
const server = http.createServer(app);
const io = socketIO(server);

io.on('connection', (socket) => {
    console.log('A new client connected');

    socket.on('button_press', (data) => {
        console.log(`Button pressed: ${data.button}`);
        // You can extend this logic to control a game or send commands
    });

    socket.on('disconnect', () => {
        console.log('Client disconnected');
    });
});

server.listen(3000, () => {
    console.log('Server is running on port 3000');
});

bash:
node server.js

bash:
sudo python3 gpio_control.py

<all code>>
