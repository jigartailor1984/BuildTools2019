# escape=`

ARG REPO=mcr.microsoft.com/windows/servercore
FROM $REPO:ltsc2019 as netfx35

# Install .NET Fx 3.5
RUN curl -fSLo microsoft-windows-netfx3.zip https://dotnetbinaries.blob.core.windows.net/dockerassets/microsoft-windows-netfx3-1809.zip `
    && tar -zxf microsoft-windows-netfx3.zip `
    && del /F /Q microsoft-windows-netfx3.zip `
    && DISM /Online /Quiet /Add-Package /PackagePath:.\microsoft-windows-netfx3-ondemand-package~31bf3856ad364e35~amd64~~.cab `
    && del microsoft-windows-netfx3-ondemand-package~31bf3856ad364e35~amd64~~.cab `
    && powershell Remove-Item -Force -Recurse ${Env:TEMP}\*

FROM netfx35 as netfx48runtime
# Install .NET Fx 4.8 runtime
ENV `
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true `
    # Ngen workaround: https://github.com/microsoft/dotnet-framework-docker/issues/231
    COMPLUS_NGenProtectedProcess_FeatureEnabled=0

RUN `
    # Installing .Net Frameworklist 4.8
    curl -fSLo ndp48-x86-x64-allos-enu.exe https://download.visualstudio.microsoft.com/download/pr/014120d7-d689-4305-befd-3cb711108212/0fd66638cde16859462a6243a4629a50/ndp48-x86-x64-allos-enu.exe `
    && ndp48-x86-x64-allos-enu.exe /q `
    && del /F /Q ndp48-x86-x64-allos-enu.exe
    
# Install NuGet CLI
ENV NUGET_VERSION=5.3.1
RUN mkdir "%ProgramFiles%\NuGet\latest" `
    && curl -fSLo "%ProgramFiles%\NuGet\nuget.exe" https://dist.nuget.org/win-x86-commandline/v%NUGET_VERSION%/nuget.exe `
    && curl -fSLo "%ProgramFiles%\NuGet\latest\nuget.exe" https://dist.nuget.org/win-x86-commandline/v5.8.0/nuget.exe

FROM netfx48runtime as mstest
RUN `
    # Install VS Test Agent
    curl -fSLo vs_TestAgent.exe https://download.visualstudio.microsoft.com/download/pr/5f914955-f6c7-4add-8e47-2e090bdc02fa/8fd51571a9aff13c4381037ccb7559a3bb3e6527cbacf4816068e711dfe824f4/vs_TestAgent.exe `
    && start /w vs_TestAgent.exe --quiet --norestart --nocache --wait `
    && powershell -Command "if ($err = dir $Env:TEMP -Filter dd_setup_*_errors.log | where Length -gt 0 | Get-Content) { throw $err }" `
    && del vs_TestAgent.exe

FROM mstest as buildtools
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

RUN ` 
    # Install VS Build Tools
    Invoke-WebRequest -UseBasicParsing https://aka.ms/vs/16/release/vs_buildtools.exe -OutFile vs_BuildTools.exe; `
    setx /M DOTNET_SKIP_FIRST_TIME_EXPERIENCE 1; `
    Start-Process vs_BuildTools.exe `
        -ArgumentList `
            "--add","Microsoft.VisualStudio.Workload.MSBuildTools", `
            "--add","Microsoft.VisualStudio.Workload.VCTools", `
            "--add","Microsoft.VisualStudio.Workload.ManagedDesktopBuildTools", `
            "--add","Microsoft.VisualStudio.Workload.DataBuildTools", `
            "--add","Microsoft.VisualStudio.Workload.NetCoreBuildTools", `
            "--add","Microsoft.Component.ClickOnce.MSBuild", `
            "--add","Microsoft.VisualStudio.Workload.WebBuildTools", `
            "--add","Microsoft.VisualStudio.Component.Roslyn.Compiler", `
            "--add","Microsoft.Component.MSBuild", `
            "--add","Microsoft.VisualStudio.Component.CoreBuildTools", `
            "--add","Microsoft.VisualStudio.Component.Windows10SDK", `
            "--add","Microsoft.VisualStudio.Component.Windows10SDK.18362", `
            "--add","Microsoft.VisualStudio.Component.Windows10SDK.17763", `
            "--add","Microsoft.VisualStudio.Component.VC.CoreBuildTools", `
            "--add","Microsoft.VisualStudio.Component.VC.Tools.x86.x64",  `
            "--add","Microsoft.VisualStudio.Component.VC.Redist.14.Latest", `
            "--add","Microsoft.VisualStudio.Component.VC.CMake.Project", ` 
            "--add","Microsoft.VisualStudio.Component.TestTools.BuildTools", `
            "--add","Microsoft.VisualStudio.Component.VC.ATL", `
            "--add","Microsoft.VisualStudio.Component.VC.ATLMFC", `
            "--add","Microsoft.VisualStudio.Component.VC.14.27.MFC", `
            "--add","Microsoft.VisualStudio.Component.VC.14.27.ATL", `
            "--add","Microsoft.VisualStudio.Component.VC.14.16.MFC", `
            "--add","Microsoft.VisualStudio.Component.VC.14.16.ATL", `
            "--add","Microsoft.Net.Component.4.8.SDK", `
            "--add","Microsoft.Net.Component.4.6.1.TargetingPack", `
            "--add","Microsoft.VisualStudio.Component.VC.CLI.Support", `
            "--add","Microsoft.VisualStudio.Component.TextTemplating", `
            "--add","Microsoft.VisualStudio.Component.VC.CoreIde", `
            "--add","Microsoft.VisualStudio.ComponentGroup.NativeDesktop.Core", `
            "--add","Microsoft.VisualStudio.Component.VC.Llvm.ClangToolset", `
            "--add","Microsoft.VisualStudio.Component.VC.Llvm.Clang", `
            "--add","Microsoft.VisualStudio.ComponentGroup.NativeDesktop.Llvm.Clang", `
            "--add","Microsoft.VisualStudio.Component.NuGet.BuildTools", `
            "--add","Microsoft.Net.Component.4.TargetingPack", `
            "--add","Microsoft.Net.Component.4.5.TargetingPack", `
            "--add","Microsoft.Net.Component.4.5.1.TargetingPack", `
            "--add","Microsoft.Net.Component.4.5.2.TargetingPack", `
            "--add","Microsoft.Net.Component.4.6.TargetingPack", `
            "--add","Microsoft.Net.ComponentGroup.TargetingPacks.Common", `
            "--add","Microsoft.NetCore.Component.Runtime.5.0", `
            "--add","Microsoft.NetCore.Component.Runtime.3.1", `
            "--add","Microsoft.NetCore.Component.SDK", `
            "--add","Microsoft.Net.Core.Component.SDK.2.1", `
            "--add","Microsoft.VisualStudio.Wcf.BuildTools.ComponentGroup", `
            "--add","Microsoft.Net.Component.3.5.DeveloperTools", `
            "--add","Microsoft.Net.ComponentGroup.4.6.1.DeveloperTools", `
            "--add","Microsoft.Net.Component.4.6.2.TargetingPack", `
            "--add","Microsoft.Net.ComponentGroup.4.6.2.DeveloperTools", `
            "--add","Microsoft.Net.Component.4.7.TargetingPack", `
            "--add","Microsoft.Net.ComponentGroup.4.7.DeveloperTools", `
            "--add","Microsoft.Net.Component.4.7.1.TargetingPack", `
            "--add","Microsoft.Net.ComponentGroup.4.7.1.DeveloperTools", `
            "--add","Microsoft.Net.Component.4.8.TargetingPack", `
            "--add","Microsoft.Net.ComponentGroup.4.8.DeveloperTools", `
            "--add","Microsoft.VisualStudio.Component.FSharp.MSBuild", `
            "--add","Microsoft.VisualStudio.Component.NuGet", `
            "--add","Microsoft.VisualStudio.Component.SQL.SSDTBuildSku", `
            "--add","Microsoft.VisualStudio.Component.Roslyn.LanguageServices", `
            "--quiet", "--wait", "--norestart", "--nocache" `
        -NoNewWindow -Wait; `
    `
    #Install Windows 8.1 SDK from https://go.microsoft.com/fwlink/p/?LinkId=323507
    Invoke-WebRequest -UseBasicParsing https://go.microsoft.com/fwlink/p/?LinkId=323507 -OutFile Windows8.1sdksetup.exe; `
    Start-Process Windows8.1sdksetup.exe `
        -ArgumentList `
            "/features", "+", "/q" `
        -NoNewWindow -Wait; 

ENV DOTNET_USE_POLLING_FILE_WATCHER=true `
    ROSLYN_COMPILER_LOCATION="C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\MSBuild\Current\Bin\Roslyn"

FROM buildtools as final

#Reset Shell to Command Prompt.
SHELL ["cmd", "/S", "/C"]

# Set PATH in one layer to keep image size down.
RUN setx /M PATH "%PATH%;%ProgramFiles(x86)%\Microsoft Visual Studio\2019\TestAgent\Common7\IDE;%programfiles(x86)\Microsoft Visual Studio\2019\TestAgent\Common7\IDE\CommonExtensions\Microsoft\TestWindow\;%ProgramFiles(x86)%\Microsoft Visual Studio\2019\BuildTools\MSBuild\Current\Bin\;%ProgramFiles(x86)%\Microsoft SDKs\ClickOnce\SignTool\;%ProgramFiles%\NuGet\latest" `
    && setx /M DevEnvDir "%ProgramFiles(x86)%\Microsoft Visual Studio\2019\BuildTools\Common7\IDE\"

RUN `
    #Cleanup
    del vs_BuildTools.exe `
    && del Windows8.1sdksetup.exe `
    `
    && rmdir /S /Q "%programfiles(x86)%\Microsoft Visual Studio\Installer" `
    && rmdir /S /Q "%ProgramData%\Package Cache" `
    && powershell Remove-Item -Force -Recurse "$env:Temp\*"
