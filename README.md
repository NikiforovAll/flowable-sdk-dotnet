# flowable-sdk-dotnet

[![Build](https://github.com/NikiforovAll/flowable-sdk-dotnet/actions/workflows/build.yml/badge.svg?branch=main)](https://github.com/NikiforovAll/flowable-sdk-dotnet/actions/workflows/build.yml)
[![CodeQL](https://github.com/NikiforovAll/flowable-sdk-dotnet/actions/workflows/codeql-analysis.yml/badge.svg)](https://github.com/NikiforovAll/flowable-sdk-dotnet/actions/workflows/codeql-analysis.yml)
[![NuGet](https://img.shields.io/nuget/dt/Flowable.Sdk.svg)](https://nuget.org/packages/Flowable.Sdk)
[![contributionswelcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/nikiforovall/flowable-sdk-dotnet)
[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg)](https://conventionalcommits.org)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/nikiforovall/flowable-sdk-dotnet/blob/main/LICENSE.md)

[![GitHub Actions Build History](https://buildstats.info/github/chart/nikiforovall/flowable-sdk-dotnet?branch=main&includeBuildsFromPullRequest=false)](https://github.com/NikiforovAll/flowable-sdk-dotnet/actions)

Flowable HTTP SDK Client for .NET

## Registration in Dependency Injection (DI)

```csharp
public static class ServiceCollectionExtensions
{
    public static IServiceCollection AddFlowableHttpClients(
        this IServiceCollection services, IConfiguration configuration)
    {
        var httpClientOptions = configuration
            .GetSection(FlowableHttpClientOptions.FlowableClientSection)
            .Get<FlowableHttpClientOptions>();

        services.AddOptions<FlowableHttpClientOptions>().Bind(configuration);

        services.AddHttpClient<IFlowableProcessHttpClient, FlowableProcessHttpClient>(options =>
        {
            options.BaseAddress = new Uri(httpClientOptions.BaseUrlProcessApi);
        });

        services.AddHttpClient<IFlowableCaseHttpClient, FlowableCaseHttpClient>(options =>
        {
            options.BaseAddress = new Uri(httpClientOptions.BaseUrlCmmnApi);
        });
        return services;
    }

    public static IServiceCollection AddFlowableExternalWorkerHttpClient(
        this IServiceCollection services, IConfiguration configuration)
    {
        var httpClientOptions = configuration
            .GetSection(FlowableHttpClientOptions.FlowableClientSection)
            .Get<FlowableHttpClientOptions>();

        services.AddOptions<FlowableHttpClientOptions>().Bind(configuration);

        services.AddHttpClient<IFlowableExternalWorkerHttpClient, FlowableExternalWorkerHttpClient>(
            options =>
            {
                options.BaseAddress = new Uri(httpClientOptions.BaseUrlExternalWorker);
            }).AddPolicyHandler(GetCircuitBreakerPolicy());

        static IAsyncPolicy<HttpResponseMessage> GetCircuitBreakerPolicy() =>
            HttpPolicyExtensions
                .HandleTransientHttpError()
                .Or<HttpRequestException>()
                .CircuitBreakerAsync(5, TimeSpan.FromSeconds(60));

        return services;
    }
}

public class FlowableHttpClientOptions
{
    public const string FlowableClientSection = "FlowableHttpClientOptions";

    public string BaseUrlExternalWorker { get; set; }

    public string BaseUrlCmmnApi { get; set; }

    public string BaseUrlProcessApi { get; set; }
}
```
