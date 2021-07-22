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

.\nssm.exe install PrometheusExporter windows_exporter.exe
Start-Service PrometheusExporter

(Invoke-WebRequest -Uri "http://localhost:9182/metrics").StatusCode
 
 
