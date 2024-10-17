# ACT-SENDEMAIL-ATM
# Set your Azure AD app credentials and other configurations
$tenantId = "42e5ac94-8790-4cc0-88ce-b104d4f8e15c"
$clientId = "d5254b98-39f2-4c0f-846b-a59ac430cc92"
$clientSecret = "V188Q~fpOVd7du_HNPBqWmZc2tmEgn.QtgXVGc6X"
$scope = "https://graph.microsoft.com/.default"
$tokenUrl = "https://login.microsoftonline.com/$tenantId/oauth2/v2.0/token"
$emailAddress = "info@acloudtraining.com" # Email recipient address

# Get access token from Azure AD
$tokenBody = @{
    client_id     = $clientId
    client_secret = $clientSecret
    scope         = $scope
    grant_type    = "client_credentials"
}

try {
    # Request the token
    $tokenResponse = Invoke-RestMethod -Method Post -Uri $tokenUrl -ContentType "application/x-www-form-urlencoded" -Body $tokenBody
    $accessToken = $tokenResponse.access_token
    Write-Host "Access Token: $accessToken" -ForegroundColor Green
} catch {
    Write-Host "Failed to retrieve access token" -ForegroundColor Red
    Write-Host $_.Exception.Message
    exit
}

# Prepare email content
$emailBody = @{
    message = @{
        subject = "Test email from Microsoft Graph API"
        body    = @{
            contentType = "Text"
            content     = "This is a test email sent using Microsoft Graph API."
        }
        toRecipients = @(
            @{
                emailAddress = @{
                    address = $emailAddress
                }
            }
        )
    }
    saveToSentItems = $false
}

# Set API request headers
$headers = @{
    "Authorization" = "Bearer $accessToken"
    "Content-Type"  = "application/json"
}

# Microsoft Graph API endpoint for sending mail
$graphApiUrl = "https://graph.microsoft.com/v1.0/users/act@acloudtraining.com/sendMail"

try {
    # Send the email via Microsoft Graph API
    $response = Invoke-RestMethod -Method Post -Uri $graphApiUrl -Headers $headers -Body ($emailBody | ConvertTo-Json -Depth 10)
    Write-Host "Email sent successfully." -ForegroundColor Green
} catch {
    Write-Host "Failed to send email" -ForegroundColor Red
    Write-Host $_.Exception.Message
}
