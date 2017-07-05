## Stream extended

* An extended SslStream with support for ALPN & SNI
* An extended BufferedStream with support for reading bytes and string

<a href="https://ci.appveyor.com/project/justcoding121/Streamextended">![Build Status](https://ci.appveyor.com/api/projects/status/3vp1pdya9ncmlqwq?svg=true)</a>

## Installation

Install by nuget 

    Install-Package StreamExtended

Supports

 * .Net Standard 1.3 or above
 * .Net Framework 4.5 or above
 
## Usage

### ALPN

When SslStream is used on client side.

```csharp
var stream = new CustomBufferedStream(yourNetworkStreamToServer);
bool alpnEnabled = false;
var alpnStream = alpnEnabled ? (Stream)new ClientHelloAlpnAdderStream(stream) : stream;

//as usual
 await sslStream.AuthenticateAsClientAsync(yourRemoteHostName, null, yourSupportedSslProtocols, false);
 
 //should return h2/http 1.1
 var selectedHttpProtocol = clientSslHelloInfo.Extensions?.FirstOrDefault(x => x.Name == "alpn")?.Data;
```

When SslStream is used on server side.

```csharp
var stream = new CustomBufferedStream(yourNetworkStreamToClient);
bool alpnEnabled = false;
var alpnStream = alpnEnabled ? (Stream)new ServerHelloAlpnAdderStream(stream) : stream;

//as usual
await sslStream.AuthenticateAsServerAsync(yourClientCertificate, false, SupportedSslProtocols, false);
```

### Server Name Indication

```csharp
var clientSslHelloInfo = await SslTools.GetClientHelloInfo(yourClientStream);

if (clientSslHelloInfo != null)
{
    var sslStream = new SslStream(yourClientStream);
    yourClientStream = new CustomBufferedStream(sslStream, BufferSize);

    string sniHostName = clientSslHelloInfo.Extensions?.FirstOrDefault(x => x.Name == "server_name")?.Data;
}
```


## Peek SSL Information

### Peek Client SSL Hello
```csharp
var clientSslHelloInfo = await SslTools.GetClientHelloInfo(yourClientStream);
```

### Peek Server SSL Hello
```csharp
var serverSslHelloInfo = await SslTools.GetServerHelloInfo(yourServerStream);
```

## Contributors

Special thanks to [@honfika](https://github.com/honfika) who contributed this code originally in [Titanium Web Proxy](https://github.com/justcoding121/Titanium-Web-Proxy) project. 
