# ATLib
![Continuous Integration](https://github.com/hbjorgo/ATLib/workflows/Continuous%20Integration/badge.svg?branch=master) [![NuGet Version](https://img.shields.io/nuget/vpre/HeboTech.ATLib.svg?style=flat)](https://www.nuget.org/packages/HeboTech.ATLib/)

ATLib is a C# AT command library that abstracts away the commands and makes it easy to communicate with modems.

Hayes command set (commonly known as AT commands) is a command set commonly used in modems. Read more about it at [Wikipedia](https://en.wikipedia.org/wiki/Hayes_command_set)

ATLib is just in the beginning. Currently only a few commands are implemented, but more will come.

## Supported commands:
- Send SMS
- Answer incoming call
- Hang up call
- Get SIM status
- Enter SIM PIN
- Get remaining PIN & PUK attempts
- Get product information
- Get battery status
- Get signal strength
- Disable echo

## Supported modems:
- SIMCOM SIM5320
- Adafruit FONA 3G
* Other modems may work using one of the implementations above

## Usage
Install as NuGet package
```shell
dotnet add package HeboTech.ATLib
```

Using a serial port to communicate with a modem is easy:
```csharp
// Set up serial port
using (SerialPort serialPort = new SerialPort(args[0], 9600, Parity.None, 8, StopBits.One))
{
  // Open serial port
  serialPort.Open();
  
  // Create a new communicator based on the serial port
  ICommunicator comm = new SerialPortCommunicator(serialPort);

  // Create a new AT channel based on the communicator
  AtChannel atChannel = new AtChannel(comm);

  // Create the modem
  AdafruitFona3G modem = new AdafruitFona3G(atChannel);

  // The library doesn't support echo, so turn it off
  modem.DisableEcho();

  // Get SIM status
  var simStatus = modem.GetSimStatus();
  Console.WriteLine($"SIM Status: {simStatus}");
  
  // Send SMS to the specified number
  var smsReference = modem.SendSMS(new PhoneNumber("0123456789"), "Hello ATLib!");
  Console.WriteLine($"SMS Reference: {smsReference}");
}
```
For more examples, check out the TestConsole project in the code.

Feedback is welcome :)
