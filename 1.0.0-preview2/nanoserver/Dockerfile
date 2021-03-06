FROM microsoft/nanoserver:10.0.14300.1030

# Install .NET Core SDK
ENV DOTNET_SDK_VERSION 1.0.0-preview2-003121
ENV DOTNET_SDK_DOWNLOAD_URL https://dotnetcli.blob.core.windows.net/dotnet/preview/Binaries/$DOTNET_SDK_VERSION/dotnet-dev-win-x64.$DOTNET_SDK_VERSION.zip

RUN powershell -NoProfile -Command \
        $ErrorActionPreference = 'Stop'; \
        $httpClient = New-Object System.Net.Http.HttpClient; \
        $stream = $httpClient.GetAsync('%DOTNET_SDK_DOWNLOAD_URL%').Result.Content.ReadAsStreamAsync().Result; \
        $file = [System.IO.File]::Create('dotnet.zip'); \
        $stream.CopyTo($file); \
        $file.Close(); \
        [System.IO.Compression.ZipFile]::ExtractToDirectory('dotnet.zip', '%ProgramFiles%\dotnet'); \
        Remove-Item -Force dotnet.zip

RUN setx /M PATH "%PATH%;%ProgramFiles%\dotnet"

# Copy forwarders assemblies to workaround 
# https://github.com/dotnet/cli/blob/rel/1.0.0-preview2/Documentation/known-issues.md#running-net-core-cli-on-nano-server
RUN copy "%windir%\System32\forwarders\*" "%ProgramFiles%\dotnet\shared\Microsoft.NETCore.App\1.0.0\"

# Trigger the population of the local package cache  
ENV NUGET_XMLDOC_MODE skip  
RUN mkdir warmup \  
    && cd warmup \  
    && dotnet new \  
    && cd .. \  
    && rmdir /q/s warmup
