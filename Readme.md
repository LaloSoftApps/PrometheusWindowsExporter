$PrometheusExporter_Url = "https://github.com/LaloSoftApps/PrometheusWindowsExporter/archive/refs/heads/main.zip"
$PrometheusExporter_Path = "C:\PrometheusExporter"

if (Test-Path -Path $PrometheusExporter_Path) {
Write-Host "El Path ya Existe"
} else {
New-Item -Path $PrometheusExporter_Path -ItemType "directory" -Force
}

Set-Location -LiteralPath $PrometheusExporter_Path
Invoke-WebRequest -Uri $PrometheusExporter_Url -OutFile PrometheusExporter.zip

Expand-Archive -LiteralPath .\PrometheusExporter.zip -DestinationPath .\

Stop-Service PrometheusExporter
.\nssm remove PrometheusExporter confirm

# msiexec /i windows_exporter-0.16.0-amd64.msi ENABLED_COLLECTORS=os,cpu LISTEN_PORT=8080
#.\nssm.exe install PrometheusExporter windows_exporter.exe
.\nssm.exe install PrometheusExporter windows_exporter.exe --config.file=$PrometheusExporter_Path\config.yml
Start-Service PrometheusExporter

netsh advfirewall firewall add rule name="PrometheusExporter 8080" dir=in action=allow protocol=TCP localport=8080

(Invoke-WebRequest -Uri "http://localhost:8080/metrics").StatusCode
 
 


