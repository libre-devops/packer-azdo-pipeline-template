```yam;
---
parameters:

  - name: PACKER_PATH
    type: string
    default: ""
    displayName: "What is the path to your packer HCL file?"

  - name: AZURE_TARGET_CLIENT_ID
    default: ""
    type: string
    displayName: "What is the client id of the service principle you wish to use with Packer?"

  - name: AZURE_TARGET_CLIENT_SECRET
    default: ""
    type: string
    displayName: "What is the client of the service principle you wish to use with Packer?  Note, this value is a secret"

  - name: AZURE_TARGET_SUBSCRIPTION_ID
    default: ""
    type: string
    displayName: "What is the subscription ID of the target subscription you are trying to deploy to?"

  - name: AZURE_TARGET_TENANT_ID
    default: ""
    type: string
    displayName: "What is the tenant ID in which the target subscription resides?"

  - name: SHORTHAND_PROJECT_NAME
    default: ""
    type: string
    displayName: "What is the shorthand name for your project?"

  - name: SHORTHAND_ENVIRONMENT_NAME
    default: ""
    type: string
    displayName: "What is the shorthand (3 character) name for environment you are deploying to?"

  - name: SHORTHAND_LOCATION_NAME
    default: ""
    type: string
    displayName: "What is the shorthand location name? E.g. uks for UK South etc"


steps:

  - pwsh: |
      if ($IsLinux)
      {
      brew install packer ; `
      packer validate ${{ parameters.PACKER_PATH }} ; packer build ${{ parameters.PACKER_PATH }}
      }
      elseif ($IsMacOS)
      {
      brew install packer ; `
      packer validate ${{ parameters.PACKER_PATH }} ; packer build ${{ parameters.PACKER_PATH }}
      }
      elseif ($IsWindows)
      {
        Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; Invoke-Expression ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
        choco install packer -y ; `
        packer validate ${{ parameters.PACKER_PATH }} ; packer build ${{ parameters.PACKER_PATH }}
      }
    displayName: 'Packer install, validate and build'
    workingDirectory: "${{ parameters.PACKER_PATH }}"
    continueOnError: false
    enabled: true
    env:
      PKR_VAR_short: ${{ parameters.SHORTHAND_PROJECT_NAME }}
      PKR_VAR_env: ${{ parameters.SHORTHAND_ENVIRONMENT_NAME }}
      PKR_VAR_loc: ${{ parameters.SHORTHAND_LOCATION_NAME }}

      ARM_CLIENT_ID: ${{ parameters.AZURE_TARGET_CLIENT_ID }}
      ARM_CLIENT_SECRET: ${{ parameters.AZURE_TARGET_CLIENT_SECRET }}
      ARM_SUBSCRIPTION_ID: ${{ parameters.AZURE_TARGET_SUBSCRIPTION_ID }}
      ARM_TENANT_ID: ${{ parameters.AZURE_TARGET_TENANT_ID }}

```