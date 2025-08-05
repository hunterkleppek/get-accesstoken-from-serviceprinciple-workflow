# Get Azure DevOps Token Action

This GitHub Action obtains an access token for Azure DevOps using a service principal. The token can be used to authenticate with Azure DevOps REST APIs.

## Prerequisites

Before using this action, you need to:

1. Create a service principal in Azure AD
2. Add the service principal to your Azure DevOps organization
3. Grant appropriate permissions to the service principal

## Inputs

| Input | Description | Required |
|-------|-------------|----------|
| `tenant-id` | Azure AD tenant ID where the service principal is registered | Yes |
| `client-id` | Application (client) ID of the service principal | Yes |
| `client-secret` | Client secret of the service principal | Yes |
| `organization` | Azure DevOps organization name (without the URL) | Yes |

## Outputs

| Output | Description |
|--------|-------------|
| `token` | The access token for Azure DevOps |

## Example Usage

```yaml
jobs:
  get-token:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Get Azure DevOps Token
        id: get-token
        uses: ./get-token-action
        with:
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          client-secret: ${{ secrets.AZURE_CLIENT_SECRET }}
          organization: 'your-organization'

      - name: Use the token
        run: |
          # The token is available as an output
          echo "Using token to access Azure DevOps API"
          
          # Example API call (don't print the token in real workflows)
          curl -H "Authorization: Bearer ${{ steps.get-token.outputs.token }}" \
               "https://dev.azure.com/your-organization/_apis/projects?api-version=7.0"
```

## Security Considerations

* Store your tenant ID, client ID, and client secret as GitHub secrets
* The token is automatically masked in logs to prevent accidental exposure
* The token is valid for a limited time (typically 1 hour)

## Troubleshooting

If you encounter a 401 Unauthorized error when using the token, check the following:

1. The service principal has proper permissions to access Azure DevOps
2. The client credentials (ID and secret) are correct
3. The service principal has been added to your Azure DevOps organization
