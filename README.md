# Node SerialPort-GSM
[![NPM](https://nodei.co/npm/serialport-gsm.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/serialport-gsm/)

## Intro
SerialPort-GSM is a simplified plugin for communicating with gsm modems. (Primarily for sms) (Focused in `PDU` mode)

***
## Table of Contents

* [Installation](#installation-instructions)
* [Usage](#usage)
    * [Methods](#methods)
        * [List Ports](#list-available-ports)
        * [Open](#opening-a-port)
        * [Initalize Modem](#initialize-modem)
        * [Close](#close-modem)
        * [Set Modem Mode](#set-modem-mode)
        * [Send SMS](#send-message)
        * [Get Sim Inbox](#get-sim-inbox)
        * [Delete Message](#delete-sim-message)
        * [Delete All SIm MEssages](#delete-all-sim-messages)
        * [Get Modem Serial](#get-modem-serial)
        * [Get Network Signal](#get-network-signal)
        * [Get Own Number](#get-own-number)
        * [Set Own Number](#set-own-number)
        * [Execute AT Command](#execute-at-command)
    * [Events](#events)
        * [open](#open)
        * [close](#close)
        * [error](#error)
        * [onSendingMessage](#onsendingmessage)
        * [onNewMessage](#onnewmessage)
        * [onNewMessageIndicator](#onnewmessage-indicator)
        * [onNewIncomingCall](#onnewincomingcall)
        * [onMemoryFull](#onmemoryfull)
    * [SerialPort](#serialport)
* [License](#license)

***
## Installation Instructions

```terminal
npm install serialport-gsm
```

## Usage

### Methods

#### List Available Ports
```js
let serialportgsm = require('serialport-gsm')

serialportgsm.list((err, result) => {
    console.log(result)
})
```

#### Opening a Port
Call other functions after the port has been opened.
`open(path, options, callback)`
When opening a serial port, specify (in this order)
1. Path to Serial Port - required.
2. Options - optional `(see sample options on code)`.
    * `autoDeleteOnReceive` - Set to `true` to delete from sim after receiving | Default is `false`
    * `enableConcatenation` - Set to `true` to receive concatenated messages as one | Default is `false`
    * `incomingCallIndication` - Set to `true` to fire the `onNewIncomingCall` event when receiving calls | Default is `false`
    * `incomingSMSIndication` - Set to `false` to not fire the `onNewIncomingCall` event when receiving calls | Default is `true`
    * `pin` - If your SIM card is protected by a IN provide the PIN as String and it will be used to unlock the SIM card during initialization | Default is `` (empty, means "no PIN existing on the SIM card")
    * `customInitCommand` - If your device needs a custom initialization command it can be provided and will be used after PIN check. The command is expected to return "OK" | Default is `` (empty, means "no custom command for init")
    * `logger` - provide a logger instance, currently "debug" is used only to output written and received serial data. Use "console" for debugging purposes | Default is no logging
```js
let serialportgsm = require('serialport-gsm')
let modem = serialportgsm.Modem()
let options = {
    baudRate: 115200,
    dataBits: 8,
    parity: 'none',
    stopBits: 1,
    flowControl: false,
    xon: false,
    rtscts: false,
    xoff: false,
    xany: false,
    autoDeleteOnReceive: true,
    enableConcatenation: true,
    incomingCallIndication: true,
    incomingSMSIndication: true,
    pin: '',
    customInitCommand: '',
    logger: console
}

modem.open('COM', options, callback[Optional])
```

#### Initialize Modem
This function starts the modem. (If your port fails to work or does not respond to commands, don't forget to call `initializeModem` after opening the port.)
```js
modem.on('open', data => {
    modem.initializeModem(callback[optional])
})
```

#### Close Modem
Closes an open connection
`close(callback[optional])`
```	js
modem.close()
```

#### Set Modem Mode	
`setModemMode(callback, type)`	
* type can be `'PDU'` or `'SMS'`
* Note: This module is focused on PDU mode as it is more supported in most GSMs.
```	js
modem.on('open', data => {	
    modem.setModemMode(callback, 'PDU')	
})	
```

#### Send Message
Sends sms.
`sendSMS(recipient, message, alert, callback)`
 * `recipient` - the recipient number should start with the location code or `+` then the location code `(Ex. '63999XXXXX19', '+63999XXXXX19' )`
 * `message` - the text message to send
 * `alert` - parameter is boolean
    * `true` - send as class 0 message(flash message)
    * `false` - send as a normal sms
 * callback
```js
modem.sendSMS('63999XXXXX19', 'Hello there Zab!', true, callback)
```

#### Get Sim Inbox
Shows messages of sim inbox
```js
modem.getSimInbox(callback)
```

#### Delete Sim Message
Delete a sim message by message object `(Use Sim Inbox data)`
```js
modem.deleteMessage(messageObj, callback)
```

#### Delete All Sim Messages
```js
modem.deleteAllSimMessages(callback)
```

#### Get Modem Serial
```js
modem.getModemSerial(callback)
```

#### Get Network Signal
```js
modem.getNetworkSignal(callback)
```

#### Get Own Number
```js
modem.getOwnNumber(callback)
```

#### Set Own Number
`setOwnNumber('number', callback, name[optional || default 'OwnNumber'])`
```js
modem.setOwnNumber(number, callback)
```

#### Execute AT Command
```js
modem.executeCommand(command, callback, priority, timeout)
```

## Other Usage 
### Events
#### open
```js
modem.on('open', result => { /*do something*/ })
```

#### close
```js
modem.on('close', result => { /*do something*/ })
```

#### error
```js
modem.on('error', result => { /*do something*/ })
```

#### onSendingMessage
```js
modem.on('onSendingMessage', result => { status, request, data })
```

#### onNewMessage
```js
modem.on('onNewMessage', messageDetails)
```

#### onNewMessage Indicator
```js
modem.on('onNewMessageIndicator', result => { sender, timeSent })
```

#### onNewIncomingCall
```js
modem.on('onNewIncomingCall', result => { number, numberScheme })
```

#### onMemoryFull
```js
modem.on('onMemoryFull', result => { status, data })
```

## SerialPort
Access base serialport. Please refer to [***SerialPort Docs***](https://serialport.io/docs/en/api-serialport) for documentation
```js
let serialportgsm = require('serialport-gsm')
let serialport = serialportgsm.serialport
```

Access modem serialport.
```js
modem.port.SERIAL_PORT_PROTOTYPES
```

## License
SerialPort-GSM is [MIT licensed](LICENSE) and all it's dependencies are MIT or BSD licensed.