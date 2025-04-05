#powershell #test-path

`# Sync Obsidian git repos if they exist`
`if( Test-Path $env:DevFolder ) {`
    `Push-Location $env:DevFolder`
    `foreach ( $sFolder in "WorkNotes", "ReferenceNotes" ) {`

        `if( ( Test-Path ( $env:DevFolder + "\Git" ) ) -and ( Test-Path ( $env:DevFolder + $sFolder ) ) ) {`
            `Add-Log -Tags "#hourly#obsidian" -Text ( "Syncing Obsidian folder " + $sFolder )`
            `Push-Location $env:DevFolder`
            `. Git\Git-Sync ( ".\" + $sFolder )`
        `} # END if`

    `} # END foreach ( $sFolder in "WorkNotes", "ReferenceNotes" )`
    `Pop-Location`
`} # END if( Test-Path $env:DevFolder )`