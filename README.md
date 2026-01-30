mkdir mcp-ado-sharepoint
cd mcp-ado-sharepoint
npm init -y

npm install express axios dotenv xlsx @modelcontextprotocol/sdk

.env
TENANT_ID=xxxx
CLIENT_ID=xxxx
CLIENT_SECRET=xxxx
ADO_PAT=xxxx
ADO_ORG=yourorg
ADO_PROJECT=yourproject
SITE_URL=https://yourcompany.sharepoint.com/sites/QA

