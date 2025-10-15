# NswagClient

## How to generate an API client

First, install NSwag:

```bash
dotnet tool install --global Nswag.ConsoleCore
```

Then generate the client like this:

```bash
nswag openapi2csclient /input:https://api.compusoft.no/swagger/v1/swagger.json?apikey=ey... /output:CompuSoftApiClient.cs /namespace:CompuSoft.ApiClient /ClassName:CompuSoftApiClient /GenerateClientInterfaces:true /GenerateNullableReferenceTypes:true /InjectHttpClient:true /UseBaseUrl:true /jsonLibrary:SystemTextJson /DateTimeType:DateTimeOffset
```

Replace `ey...` with the correct apikey.

## Example code

```csharp
using System.Net.Http.Headers;

var http = new HttpClient();
var csPassword = "7928005321";

http.DefaultRequestHeaders.Add("CS-ApiKey", "...");
http.DefaultRequestHeaders.Add("CS-Password", csPassword);

var api = new CompuSoft.ApiClient.CompuSoftApiClient("https://api.compusoft.com/", http);
var c = await api.CustomersGETAsync(113, null, null, null, null, csPassword);
Console.WriteLine(c.Email);
```
