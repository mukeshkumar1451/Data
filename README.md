$clientId = "YOUR_CLIENT_ID"
$tenant = "YOUR_TENANT_ID.onmicrosoft.com"
$secret = "YOUR_CLIENT_SECRET"

Connect-PnPOnline `
 -Url "https://corpofficeapps.sharepoint.com/sites/Ops_Home/nationalops" `
 -ClientId $clientId `
 -ClientSecret $secret `
 -Tenant $tenant
