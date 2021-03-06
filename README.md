# Office 365 CLI runscript GitHub action
GitHub action to run a script using the Office 365 CLI

![Office 365 CLI Runscript](./images/cli-runscript.png)

This GitHub Action (created using typescript) uses [Office 365 CLI](https://pnp.github.io/office365-cli/), to run a line of script supplied to it or run code in a script file supplied to it.

## Usage
### Pre-requisites
Create a workflow `.yml` file in `.github/workflows` directory of your repo. An [example workflow](#example-workflow---office-365-cli-runscript) is available below. For more information, reference the GitHub Help Documentation for [Creating a workflow file](https://help.github.com/en/articles/configuring-a-workflow#creating-a-workflow-file).

## Dependencies on other GitHub Actions

- [Office 365 CLI Login](https://github.com/pnp/action-cli-login) – **Required** . This action is dependant on `action-cli-login`. So in the workflow we need to run  `action-cli-login` before using this action.

#### Optional requirement
Since `action-cli-login` requires user name and password which are sensitive pieces of information, it would be ideal to store them securely. We can achieve this in a GitHub repo by using [secrets](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets). So, click on `settings` tab in your repo and add 2 new secrets:
- `adminUsername` - store the admin user name in this (e.g. user@contoso.onmicrosoft.com)
- `adminPassword` - store the password of that user in this.
These secrets are encrypted and can only be used by GitHub actions.

### Inputs
- `O365_CLI_SCRIPT_PATH` : Relative path of the script in your repo.
- `O365_CLI_SCRIPT` : The script to run
- `IS_POWERSHELL` : `true|false` Used only with O365_CLI_SCRIPT. If true the assumption is the script passed in O365_CLI_SCRIPT will be a PowerShell script, otherwise the assumption is bash script. Default is false

One of `O365_CLI_SCRIPT_PATH` / `O365_CLI_SCRIPT` is mandatory, in case both are defined `O365_CLI_SCRIPT_PATH` gets preference.

### Example workflow - Office 365 CLI Runscript
On every `push` build the code, then deploy and then send an email using Office 365 CLI Runscript action.

```yaml
name: SPFx CICD with O365 CLI

on: [push]

jobs:
  
  runscript:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [10.x]
    
    steps:
    
    # Office 365 cli login action
    - name: Login to tenant
      uses: pnp/action-cli-login@v1.0.0
      with:
        ADMIN_USERNAME:  ${{ secrets.adminUsername }}
        ADMIN_PASSWORD:  ${{ secrets.adminPassword }}
    
    # Office 365 CLI runscript action option 1 (single line of script as input)
    - name: Send email
      uses: pnp/action-cli-runscript@v1.0.0
      with:
        O365_CLI_SCRIPT: o365 spo mail send --webUrl https://contoso.sharepoint.com/sites/teamsite --to 'user@contoso.onmicrosoft.com' --subject 'Deployment done' --body '<h2>Office 365 CLI</h2> <p>The deployment is complete.</p> <br/> Email sent via Office 365 CLI GitHub Action.'
    
    # Office 365 CLI runscript action option 2 (script file as input)
    - name: Create lists
      uses: pnp/action-cli-runscript@v1.0.0
      with:
        O365_CLI_SCRIPT_PATH: ./script/lists.ps1 
        #lists.ps1 will have all the required Office 365 CLI commands
```

#### Self-hosted runners
If self-hosted runners are used for running the workflow, then please make sure that they have `PowerShell` or `bash` installed on them. 

## Release notes

### v1.0.0
- Added inital 'Office 365 CLI runscript' GitHub action solving #2