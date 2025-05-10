#reference #git #commands

Remove a file from syncing without removing the local copy
	`git rm --cached FILENAME`

Clone a repository into the current folder
	`git clone https://github.com/dbc13543/PowerBI
	`git pull origin master`

Force a push
	`git push --all -f`

Show a list of all commits
	`git log`

Revert to a previous commit, roll back the repository
	`git revert <commit id>`
	`git revert c2ab9f9d7deb8f0ae4371cc605e417de96fd22d5`

Revert only my local copy, don't write to the repository
	`git reset <commit id>`

Fetch any changes that exist on the server but not locally
	`git fetch --all`

Pull the latest code from the server
	`git pull origin master`

Show a list of all git repos beneath the current folder
	`Get-ChildItem . -Attributes Directory+Hidden -ErrorAction SilentlyContinue -Filter ".git" -Recurse | % { Write-Host $_.FullName.SubString( 0, $_.FullName.Length - "\.git".Length ) }