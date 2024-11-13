# Bank API 🏦

[![CC BY-NC-SA 4.0][cc-by-nc-sa-shield]][cc-by-nc-sa]
![GitHub commit activity](https://img.shields.io/github/commit-activity/m/erwinkramer/bank-api)

![Scalar landing page](.images/scalar-landingpage.png)

![Aspire](.images/aspire.png)

The Bank API is a design reference project suitable to bootstrap development for a compliant and modern API.

## Technology stack

- [ASP.NET Core 9.0 - Minimal API](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis/overview?view=aspnetcore-9.0) for API development, with following base services:

  - [HybridCache](https://learn.microsoft.com/en-us/aspnet/core/performance/caching/hybrid?view=aspnetcore-9.0) for caching

  - [Rate limiting](https://learn.microsoft.com/en-us/aspnet/core/performance/rate-limit?view=aspnetcore-9.0) for rate limiting

  - [API Key, JWT bearer and OpenID Connection-based authentication](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis/security?view=aspnetcore-9.0#configuring-authentication-strategy) for security

  - [OpenApi](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/openapi/aspnetcore-openapi?view=aspnetcore-9.0&tabs=visual-studio) for generating OpenAPI specifications

- [OpenTelemetry with OTLP and the standalone Aspire Dashboard](https://learn.microsoft.com/en-us/dotnet/core/diagnostics/observability-otlp-example) for logging

- [Kiota API client generation](https://learn.microsoft.com/en-us/openapi/kiota/using#client-generation) for calling downstream APIs

- [Gridify](https://alirezanet.github.io/Gridify) for filtering, ordering and paging

- [Scalar](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/openapi/using-openapi-documents?view=aspnetcore-9.0#use-scalar-for-interactive-api-documentation) for interactive API documentation

- [Spectral](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/openapi/using-openapi-documents?view=aspnetcore-9.0#lint-generated-openapi-documents-with-spectral) for linting

- [OpenApiAnyFactory](./bank-api-minimal/Api.Helper.OpenApiAnyFactory.cs) from [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) for parsing JSON to `IOpenApiAny` types

- [xUnit](https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-dotnet-test) for unit tests

- [REST Client extension](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) in Visual Studio Code for quick local tests via `.http` files

## Prerequisites

- [.NET 9 SDK](https://dotnet.microsoft.com/en-us/download/dotnet/9.0)

- [Spectral CLI client](https://meta.stoplight.io/docs/spectral/b8391e051b7d8-installation), via `npm install -g @stoplight/spectral-cli`

- [Kiota CLI tool](https://learn.microsoft.com/en-us/openapi/kiota/overview), via `dotnet tool install --global Microsoft.OpenApi.Kiota`, to generate clients for downstream APIs.

- [Visual Studio Code extensions](.vscode/extensions.json)

## Quick start

- (Optionally) regenerate the GitHub downstream API client:

    ```powershell
    kiota download apisguru::github.com --co -o ./specs-downstream/github-api.json
    kiota generate -l csharp -d ./specs-downstream/github-api.json -c GitHubClient -n DownstreamClients.GitHub -o ./bank-api-minimal/DownstreamClients/GitHub --include-path "/repos/{owner}/{repo}/releases/*" --clean-output
    ```

- Start the standalone Aspire Dashboard for logging:

    ```powershell
    docker run --rm -it `
    -p 18888:18888 `
    -p 4317:18889 `
    --name aspire-dashboard `
    mcr.microsoft.com/dotnet/aspire-dashboard:latest
    ```

    Copy the url shown in the resulting output when running the container, and replace `0.0.0.0` with `localhost`, eg <http://localhost:18888/login?t=123456780abcdef123456780> and open that in your browser, or you can also paste the key after `/login?t=` when the login dialog is shown. The token will change each time you start the container.

- Generate a new JWT-token for secured endpoints:

    ```powershell
    dotnet user-jwts create --scope "bank_api" --role "banker"
    ```

- Run `dotnet build` to output the OpenAPI definition

- Validate the OpenAPI definition:

    ```powershell
    spectral lint ./specs-generated/openapi.json -v -F "hint"
    ```

## License

This work is licensed under a
[Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License][cc-by-nc-sa].

[![CC BY-NC-SA 4.0][cc-by-nc-sa-image]][cc-by-nc-sa]

[cc-by-nc-sa]: http://creativecommons.org/licenses/by-nc-sa/4.0/
[cc-by-nc-sa-image]: https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png
[cc-by-nc-sa-shield]: https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg