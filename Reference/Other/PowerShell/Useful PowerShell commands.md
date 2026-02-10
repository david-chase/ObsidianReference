#powershell 

Show list of installed modules
	`Get-Module -ListAvailable

Grep equivalent - find a string
	`Get-Module -ListAvailable | Select-String "yaml"

Read from a file / cat
	`Get-Content -Path "mswcName.json"

Parse a YAML file
	`$sYaml = Get-Content -Path '.\mswcName Query.yaml' | ConvertFrom-Yaml

Encode a URL
	`$URL = [System.Web.HttpUtility]::UrlEncode( $sString )

Block a program through the firewall
	`New-NetFirewallRule -Program "C:\Program Files\Microsoft Office\root\Office16\POWERPNT.EXE" -Action Block -Profile Domain,Private,Public -DisplayName "Block PowerPoint" -Description "PowerPoint blocked" -Direction Outbound`

Pipe output to the clipboard
	`<command> | Set-Clipboard`
	`minikube ip | Set-Clipboard`