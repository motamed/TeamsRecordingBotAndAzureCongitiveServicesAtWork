FROM mcr.microsoft.com/dotnet/framework/sdk:4.8 AS build

ARG CallSignalingPort=9441
ARG CallSignalingPort2=9442
ARG InstanceInternalPort=8445

COPY /src /src

WORKDIR /src
RUN nuget restore

WORKDIR /src/RecordingBot.Console
RUN msbuild /p:Configuration=Release /p:OutputPath=C:\app /p:Platform=x64

FROM mcr.microsoft.com/windows:1809
SHELL ["powershell", "-Command"]

# Install OpenSSL using Chocolatey
RUN Invoke-WebRequest -Uri "https://slproweb.com/download/Win32OpenSSL_Light-3_3_1.exe" -OutFile "Win32OpenSSL_Light-3_3_1.exe" ; \
    Start-Process "Win32OpenSSL_Light-3_3_1.exe" -ArgumentList "/silent", "/verysilent", "/sp-", "/suppressmsgboxes" -NoNewWindow -Wait ; \
    Remove-Item -Force "Win32OpenSSL_Light-3_3_1.exe"

ADD https://aka.ms/vs/16/release/vc_redist.x64.exe /bot/VC_redist.x64.exe

COPY /scripts/entrypoint.cmd /bot
COPY /scripts/halt_termination.ps1 /bot
COPY --from=build /app /bot

WORKDIR /bot

EXPOSE $InstanceInternalPort
EXPOSE $CallSignalingPort
EXPOSE $CallSignalingPort2

ENTRYPOINT [ "entrypoint.cmd" ]