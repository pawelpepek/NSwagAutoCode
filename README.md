# NSwagAutoCode

NSwag configuration for client code genaration.

## Branches
### main
for the Angular
### react
for the React

## Instruction
### Setup
1. Add into WebUI.csproj:
```c#
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.SpaProxy" Version="6.0.14" />
	 <PackageReference Include="NSwag.AspNetCore" Version="13.20.0" />
	 <PackageReference Include="NSwag.MSBuild" Version="13.20.0">
		 <PrivateAssets>all</PrivateAssets>
		 <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
	 </PackageReference>
  </ItemGroup>

  <ItemGroup>
    <!-- Don't publish the SPA source files, but do show them in the project files list -->
    <Content Remove="$(SpaRoot)**" />
    <None Remove="$(SpaRoot)**" />
    <None Include="$(SpaRoot)**" Exclude="$(SpaRoot)node_modules\**" />
  </ItemGroup>

	<Target Name="NSwag" AfterTargets="PostBuildEvent" Condition=" '$(Configuration)' == 'Debug' ">
		<Exec WorkingDirectory="$(ProjectDir)" EnvironmentVariables="ASPNETCORE_ENVIRONMENT=Development" Command="$(NSwagExe_Net60) run nswag.json /variables:Configuration=$(Configuration)" />
	</Target>
```

2. Add nswag.json into project directory. Pay attention:
 ```
  "project": "WebUI.csproj",
  "typeScriptVersion": 5.1,
  "rxJsVersion": 7.5,
```
   
3. Configure NSwag service in the Program.cs:
```c#
builder.Services.AddControllersWithViews();

builder.Services.AddOpenApiDocument(configure =>
{
    configure.Title = "NSwag Auto Code";
});

var app = builder.Build();
```

4. Configure NSwager in the Program.cs:
```c#
app.UseStaticFiles();

app.UseSwaggerUi3(settings =>
{
    settings.Path = "/api";
    settings.DocumentPath = "/api/specification.json";
});

app.UseRouting();
```

5. Use only lower case routes for controllers:
```c#
    [ApiController]
    [Route("weatherforecast")]
    public class WeatherForecastController : ControllerBase {}
```

### Using

```ts
import { Component } from '@angular/core';
import { WeatherForecast, WeatherForecastClient } from '../web-api-client';

@Component({
  selector: 'app-fetch-data',
  templateUrl: './fetch-data.component.html',
})
export class FetchDataComponent {
  public forecasts: WeatherForecast[] = [];

  constructor(client: WeatherForecastClient) {
    client.get().subscribe((result) => {
      this.forecasts = result;
    });
  }
}
```
