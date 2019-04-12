FROM mcr.microsoft.com/windows/servercore:1809

# Install chocolatey
RUN @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
RUN choco feature enable -n allowGlobalConfirmation

WORKDIR C:\\tools

# Set driver/browser versions
ARG Selenium_Major_Version="3.141"
ARG Selenium_Version="3.141.59"

ARG GeckoDriver_Version="0.24.0"
ARG ChromeDriver_Version="73.0.3683.68"

ARG Chrome_Version="73.0.3683.103"
ARG Firefox_Version="66.0.3"

# Install browsers
RUN choco install googlechrome --version %Chrome_Version%

# Install Java
RUN choco install jdk8

# Download Selenium
RUN powershell Invoke-WebRequest \
    -Uri "https://selenium-release.storage.googleapis.com/$env:Selenium_Major_Version/selenium-server-standalone-$env:Selenium_Version.jar" \
    -OutFile ".\\selenium-server-standalone.jar"

# Download & unpack ChromeDriver
RUN powershell Invoke-WebRequest \
    -Uri "https://chromedriver.storage.googleapis.com/$env:ChromeDriver_Version/chromedriver_win32.zip" \
    -OutFile ".\\chromedriver_win32.zip"
RUN powershell Add-Type -A 'System.IO.Compression.FileSystem'; \
    [IO.Compression.ZipFile]::ExtractToDirectory('chromedriver_win32.zip', '.')

# Download & unpack GeckoDriver
RUN powershell [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; \
    Invoke-WebRequest \
        -Uri "https://github.com/mozilla/geckodriver/releases/download/v$env:GeckoDriver_Version/geckodriver-v$env:GeckoDriver_Version-win64.zip" \
        -OutFile ".\\geckodriver_win64.zip"
RUN powershell Add-Type -A 'System.IO.Compression.FileSystem'; \
    [IO.Compression.ZipFile]::ExtractToDirectory('geckodriver_win64.zip', '.')

# Install Firefox

RUN choco install firefox --version %Firefox_Version%

#RUN powershell Install-WindowsFeature Server-Media-Foundation -IncludeAllSubFeature
#RUN powershell Add-WindowsCapability -Name "Tools.Graphics.DirectX~~~~0.0.1.0" -Online -LimitAccess -Source "C:\Windows\winsxs"

COPY config.json .

RUN powershell [Environment]::SetEnvironmentVariable('MOZ_HEADLESS', '1', [EnvironmentVariableTarget]::Machine)

ENTRYPOINT java \
    -Dwebdriver.gecko.driver=C:\\tools\\geckodriver.exe \
    -Dwebdriver.chrome.driver=C:\\tools\\chromedriver.exe \
    -jar selenium-server-standalone.jar \
    -role node \
    -nodeConfig config.json