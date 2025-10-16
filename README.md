# Compusoft API client

## How to generate an C# API client

First, install NSwag:

```bash
dotnet tool install --global Nswag.ConsoleCore
```

Then generate the client like this:

```bash
nswag openapi2csclient /input:https://api.compusoft.com/swagger/v1/swagger.json?apikey=ey... /output:CompuSoftApiClient.cs /namespace:CompuSoft.ApiClient /ClassName:CompuSoftApiClient /GenerateClientInterfaces:true /GenerateNullableReferenceTypes:true /InjectHttpClient:true /UseBaseUrl:true /jsonLibrary:SystemTextJson /DateTimeType:DateTimeOffset
```

Replace `ey...` with the correct apikey.

### Example code

```csharp
using System.Net.Http.Headers;

var http = new HttpClient();
var csPassword = "...";
var apiKey = "...";

http.DefaultRequestHeaders.Add("CS-ApiKey", apiKey);
http.DefaultRequestHeaders.Add("CS-Password", csPassword);

var api = new CompuSoft.ApiClient.CompuSoftApiClient("https://api.compusoft.com/", http);
var c = await api.CustomersAllAsync(0, 10, null, null, null, null, null, null, null, null, csPassword);
c.ToList().ForEach(x => Console.WriteLine($"{x.Id} {x.Name}"));
```

## How to generate a TS API client

- Create a new project

```powershell
mkdir compusoft-ts-min
cd compusoft-ts-min
npm init -y
npm i -D typescript
npx tsc --init
```

- Edit **tsconfig.json** to contain only this:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "CommonJS",
    "moduleResolution": "Node",
    "rootDir": "src",
    "outDir": "dist",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "lib": ["ES2020", "DOM"]
  },
  "include": ["src"]
}
```

- Update **package.json** scripts:

```json
{
  "scripts": {
    "start": "tsc && node dist/demo.js"
  }
}
```


- Generate the NSwag client

Run this in PowerShell (single line):

```powershell
nswag openapi2tsclient /input:https://api.compusoft.com/swagger/v1/swagger.json?apikey=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzaXRlaWQiOiJNQ1JESVYiLCJ1c2VyaWQiOiI5OSIsImRhdGUiOiI0NTg3Niw1NjgyMDQ5MzA1NTQifQ.q5xm_D0PuogQDil9capzBcJEI1BVoHdpxGkJzoBEDe4 /output:src/compusoft-api-client.ts /Template:Fetch /ClassName:CompuSoftApiClient /GenerateClientInterfaces:true
```

This creates the file **src/compusoft-api-client.ts**.


- Minimal demo (single file)

Create **src/demo.ts**:

```ts
import { CompuSoftApiClient } from "./compusoft-api-client";

const API_KEY = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzaXRlaWQiOiJNQ1JESVYiLCJ1c2VyaWQiOiI5OSIsImRhdGUiOiI0NTg3Niw1NjgyMDQ5MzA1NTQifQ.q5xm_D0PuogQDil9capzBcJEI1BVoHdpxGkJzoBEDe4";
const CS_PASSWORD = "0497987770";
const baseUrl = "https://api.compusoft.com";

async function fetchWithHeaders(input: RequestInfo, init?: RequestInit): Promise<Response> {
  const merged: RequestInit = {
    ...init,
    headers: {
      ...(init?.headers ?? {}),
      "CS-ApiKey": API_KEY,
      "CS-Password": CS_PASSWORD
    }
  };
  return fetch(input as any, merged);
}

const api = new CompuSoftApiClient(baseUrl, { fetch: fetchWithHeaders });

async function main() {
  const res = await api.customersAll(0, 10, undefined, undefined, undefined, undefined, undefined, undefined, undefined, undefined, CS_PASSWORD);
  for (const c of res) {
    console.log(`${c.id} ${c.name}`);
  }
}

main().catch(err => {
  console.error("Error:", err);
});
```
- Build and run

```powershell
npm start
```

