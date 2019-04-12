FROM mcr.microsoft.com/windows/servercore

# Install chocolatey
RUN @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
RUN choco feature enable -n allowGlobalConfirmation
RUN choco install server-jre8
RUN powershell Invoke-WebRequest -Uri "https://selenium-release.storage.googleapis.com/3.141/selenium-server-standalone-3.141.59.jar" -OutFile "C:\\tools\\selenium-server-standalone.jar"

WORKDIR C:\\tools
EXPOSE 4444

ENTRYPOINT java -jar selenium-server-standalone.jar -role hub