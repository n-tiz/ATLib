# ATLib
[![CI](https://github.com/hbjorgo/ATLib/workflows/CI/badge.svg)](https://github.com/hbjorgo/ATLib)
[![Nuget](https://img.shields.io/nuget/v/hebotech.atlib)](https://www.nuget.org/packages/HeboTech.ATLib)
[![Nuget](https://img.shields.io/nuget/dt/HeboTech.ATLib)](https://www.nuget.org/packages/HeboTech.ATLib)

ATLib is a C# AT command library that abstracts away the commands and makes it easy to communicate with modems.

Hayes command set (commonly known as AT commands) is a command set frequently used in modems. Read more about it at [Wikipedia](https://en.wikipedia.org/wiki/Hayes_command_set).

Feedback is very much welcome and please request features 🙂

[HeboTech.GsmApi](https://github.com/hbjorgo/GsmApi) is a REST API wrapping this library.

## Supported commands:
- Send SMS (text and PDU (GSM7bit, UCS2, ANSI))
- List SMSs
- Read SMS (text and PDU)
- Delete SMS
- Set SMS message format (text and PDU (GSM7bit, UCS2, ANSI))
- Dial number
- Answer incoming call
- Hang up call
- Get SIM status
- Enter SIM PIN
- Get remaining PIN & PUK attempts
- Get product information
- Get battery status
- Get signal strength
- Get / set date and time
- Disable echo
- Send USSD code
- Get / set character set
- Get IMSI
- Some modems may also support modem specific commands

## Events
- Incoming call
- Missed call
- Call started
- Call ended
- SMS received
- Error received
- USSD response received
- Generic event

## Supported modems:
- Adafruit FONA 3G (based on SIMCOM SIM5320 chipset)
- D-Link DWM-222 (based on Qualcomm MDM9225 chipset)
- TP-LINK MA260 (based on a Qualcomm chipset)
- Other modems may work using one of the implementations above

## Other
- Debug functionality that lets you intercept incoming and outgoing data

## Usage
Install as NuGet package
```shell
dotnet add package HeboTech.ATLib
```

Using a serial port to communicate with a modem is easy:
```csharp
// Set up serial port
using SerialPort serialPort = new SerialPort(args[0], 9600, Parity.None, 8, StopBits.One)
{
    Handshake = Handshake.RequestToSend
};
serialPort.Open();

// Create AT channel
using AtChannel atChannel = AtChannel.Create(serialPort.BaseStream);

// Create the modem
using IModem modem = new Fona3G(atChannel);

// The library doesn't support echo, so turn it off
await modem.DisableEchoAsync();

// Get SIM status
var simStatus = await modem.GetSimStatusAsync();
Console.WriteLine($"SIM Status: {simStatus}");

if (simStatus == SimStatus.SIM_PIN)
{
    var simPinStatus = await modem.EnterSimPinAsync(new PersonalIdentificationNumber("<PIN>"));
    Console.WriteLine($"SIM PIN Status: {simPinStatus}");
}

// Set SMS text format
var smsTextFormatResult = await modem.SetSmsMessageFormatAsync(SmsTextFormat.Text);

// Send SMS to the specified number
var smsReference = await modem.SendSMSAsync(new PhoneNumber("0123456789"), "Hello ATLib!");
Console.WriteLine($"SMS Reference: {smsReference}");
```
Because it relies on a stream, you can even control a modem over a network! Either use a network attached modem, or forward a modem serial port to a network port.

For more examples, check out the TestConsole project in the code.
