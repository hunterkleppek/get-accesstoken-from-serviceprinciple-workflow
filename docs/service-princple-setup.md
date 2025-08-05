# Service Principal Setup Guide

This guide explains how to set up a service principal for use with Azure DevOps. The service principal can be used to authenticate with Azure DevOps REST APIs.

## 1. Create a Service Principal in Azure AD

### Using Azure Portal

1. Sign in to the [Azure Portal](https://portal.azure.com)
2. Navigate to **Azure Active Directory** > **App registrations**
3. Click **New registration**
4. Enter a name for your application (e.g., "ADO-API-Access")
5. Select supported account types (usually "Accounts in this organizational directory only")
6. Click **Register**
7. Note the **Application (client) ID** and **Directory (tenant) ID**
8. Navigate to **Certificates & secrets**
9. Click **New client secret**
10. Enter a description and select an expiration period
11. Click **Add**
12. **IMPORTANT**: Copy the client secret value immediately, as you won't be able to see it again

### Using PowerShell

```powershell
# Install the Az module if not already installed
# Install-Module -Name Az -AllowClobber -Scope CurrentUser

# Login to Azure
Connect-AzAccount

# Create a new service principal with a password
$sp = New-AzADServicePrincipal -DisplayName "ADO-API-Access"

# Get the service principal information
$spId = $sp.ApplicationId
$tenantId = (Get-AzContext).Tenant.Id

# Output the information
Write-Host "Tenant ID: $tenantId"
Write-Host "Client ID: $spId"
Write-Host "Client Secret: $($sp.PasswordCredentials.SecretText)"
```

## 2. Add the Service Principal to Azure DevOps

1. Sign in to your Azure DevOps organization: `https://dev.azure.com/your-organization`
2. Go to **Organization Settings** (bottom left)
3. Select **Users**
4. Click **Add users**
5. In the "Add new users" dialog:
   - Enter the service principal's Application ID (Client ID) in this format: `<Client ID>@<Tenant ID>`
   - Select an access level (e.g., "Basic")
   - Click **Add**

## 3. Grant Permissions to the Service Principal

For organization-level access:

1. Go to **Organization Settings**
2. Select **Permissions**
3. Select the appropriate group (e.g., "Project Collection Administrators")
4. Click **Members**
5. Click **Add**
6. Search for and add the service principal by its display name

For project-level access:

1. Navigate to your project
2. Go to **Project Settings** (bottom left)
3. Select **Permissions**
4. Select the appropriate group (e.g., "Project Administrators")
5. Click **Members**
6. Click **Add**
7. Search for and add the service principal by its display name

## 4. Configure GitHub Secrets

Add the following secrets to your GitHub repository:

1. `AZURE_TENANT_ID`: Your Azure AD tenant ID
2. `AZURE_CLIENT_ID`: Your service principal's application (client) ID
3. `AZURE_CLIENT_SECRET`: Your service principal's client secret

These secrets will be used by the GitHub Action to obtain a token.

## Testing the Service Principal

You can test the service principal by running the included PowerShell script:

```powershell
.\Get-AzureDevOpsToken.ps1 -TenantId "your-tenant-id" -ClientId "your-client-id" -ClientSecret "your-client-secret" -Organization "your-organization"
```

If successful, the script will output an access token that can be used to authenticate with Azure DevOps REST APIs.
