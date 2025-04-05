#powershell #share

`$password = ConvertTo-SecureString "yM73txhrUpRJ" -AsPlainText -Force`
`$Cred = New-Object System.Management.Automation.PSCredential ("dchase@hotmail.com", $password)`
`New-PSDrive -Name F -Persist -Credential $Cred -PSProvider FileSystem -Root "[\\roswell\data](file://roswell/data)"`