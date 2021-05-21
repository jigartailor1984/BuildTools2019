# escape=`
ARG REPO=buildtools2019
ARG TAG=latest
FROM $REPO:$TAG

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]


RUN `
    # Download Wix Toolset from https://github.com/wixtoolset/wix3/releases/download/wix3112rtm/wix311.exe
    Invoke-WebRequest -UseBasicParsing https://github.com/wixtoolset/wix3/releases/download/wix3112rtm/wix311.exe -OutFile wix311.exe; `
    `
    # Install Wix Toolset.
    Start-Process wix311.exe -ArgumentList "-q" -NoNewWindow -Wait;`
    `
    # Download Wix extension for VS 2019
    Invoke-WebRequest -UseBasicParsing https://wixtoolset.gallerycdn.vsassets.io/extensions/wixtoolset/wixtoolsetvisualstudio2019extension/1.0.0.4/1563296438961/Votive2019.vsix -OutFile Votive2019.vsix; `
    `
    #Install Wix extension for VS 2019
    Start-Process VSIXInstaller.exe -ArgumentList "/q", "Votive2019.vsix" -NoNewWindow -Wait;`
    ` 
    #Remove downloaded installers.
    cmd /S /C del wix311.exe; `
    cmd /S /C del Votive2019.vsix;

CMD [ "cmd","/S"]

#docker build --pull --rm -f "Wix\DockerFile" -t wix:311 "Wix"
#docker build --rm -f "Wix\DockerFile" -t wix:311 "Wix"