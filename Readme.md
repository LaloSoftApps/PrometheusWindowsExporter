@ECHO OFF

nssm.exe install PrometheusExporter windows_exporter.exe --config.file=%cd%\config.yml

net start PrometheusExporter


netsh advfirewall firewall add rule name="PrometheusExporter 8080" dir=in action=allow protocol=TCP localport=8080


###########################################################################


$PrometheusExporter_Url = "https://github.com/LaloSoftApps/PrometheusWindowsExporter/archive/refs/heads/main.zip" 
$PrometheusExporter_Path = "C:\PrometheusExporter"

if (!(Test-Path -Path $PrometheusExporter_Path)) { New-Item -Path $PrometheusExporter_Path -ItemType "directory" -Force }

Set-Location -LiteralPath $PrometheusExporter_Path 
Invoke-WebRequest -Uri $PrometheusExporter_Url -OutFile PrometheusExporter.zip

Expand-Archive -LiteralPath .\PrometheusExporter.zip -DestinationPath .\
Move-Item .\PrometheusWindowsExporter-main\* .\
Remove-Item .\PrometheusWindowsExporter-main\ -Recurse -Force
Remove-Item .\PrometheusExporter.zip -Force


#Stop-Service PrometheusExporter 
#.\nssm remove PrometheusExporter confirm

#.\nssm.exe install PrometheusExporter windows_exporter.exe

.\nssm.exe install PrometheusExporter windows_exporter.exe --config.file=$PrometheusExporter_Path\config.yml 
Start-Service PrometheusExporter

netsh advfirewall firewall add rule name="PrometheusExporter 8080" dir=in action=allow protocol=TCP localport=8080

(Invoke-WebRequest -Uri "http://localhost:8080/metrics").StatusCode




