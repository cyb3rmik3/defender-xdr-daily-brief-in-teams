# DefenderXDR Daily Brief in Teams: Deployment Guide

## 0. Prerequisites

- Get the **teamsGroupID** and **teamsChannelId** before deploying the ARM Template
> Note: Teams Reader is the least privilige role for the powershell method

### Powershell method
1. Install Microsoft Teams Powershell module, detailed steps can be found [here](https://learn.microsoft.com/en-us/MicrosoftTeams/teams-powershell-install)
```powershell
Install-Module -Name MicrosoftTeams 
```
2. Connect to Microsoft Teams
```powershell 
Connect-MicrosoftTeams
```
3. Find the Teams ID (you have to know your Teams' name where the AdaptiveCard will be deployed beforhand)
```powershell 
Get-Team -DisplayName "YourTeamsName"
```
The `GroupID` in the first column is your `teamsGroupId`

4. Find the Channel ID, use the Group ID from the previous command
```powershell 
Get-TeamChannel -GroupId "TeamsGroupID"
```
The `Id` in the first column is your `teamsChannelId`

### UI method

1. Open Microsoft Teams
2. Navigate to the Team and Channel you are interested in
3. Click the three dots (…) next to the channel name
4. Select “Get link to channel”
5. Copy the URL provided
6. The URL will look similar to:
`https://teams.microsoft.com/l/channel/<CHANNEL_ID>/<Channel_Name>?groupId=<TEAM_ID>&tenantId=<TENANT_ID>`
7. Extract the values of **<CHANNEL_ID>** and **<TEAM_ID>**. The **CHANNEL_ID** is equals to `teamsChannelId` and the 
**TEAM_ID** is the `teamsGroupId`.

---

## 1. Deploy the ARM Template

1. Go to **Azure Portal** → search for **"Deploy a custom template"**
2. Click **"Build your own template in the editor"** and paste the contents of `DefenderXDR-DailyBrief-Teams.json`
3. Fill in the required parameters:
   - `workflowName` — name for the Logic App (default: `DefenderXDR-DailyBrief-Teams`)
   - `location` — Azure region
   - `teamsGroupId` — Microsoft Teams Group/Team ID
   - `teamsChannelId` — Microsoft Teams Channel ID (use the URL-decoded format)
   - `teamsConnectionName` — name for the Teams API connection (default: `teams-DefenderBriefingConn`)
4. Click **Review + Create** → **Create**

---

## 2. Authorize the Teams API Connection

1. Go to **Azure Portal** → navigate to the deployed **API connection** resource (`teams`)
2. Navigate on the left, under **General** → click **Edit API connection** → **Authorize**
3. Sign in with a user account that is a **member of the Teams channel** and has (at minimum) the role of **Teams Reader** specified in the parameters
4. Click **Save**

---

## 3. Assign Graph API Permission to the Managed Identity

The Logic App uses a **System-Assigned Managed Identity** to call the Microsoft Graph `ThreatHunting.Read.All` application permission.

### Prerequisites

- PowerShell 5.1+ or PowerShell 7+
- Admin account with **Privileged Role Administrator** or **Global Administrator** in Entra ID

### Script

```powershell
$principalId = "<LOGIC APP MANAGED IDENTITY OBJECT ID>"

Connect-AzureAD
$graphSP = Get-AzureADServicePrincipal -Filter "AppId eq '00000003-0000-0000-c000-000000000000'"
$role = $graphSP.AppRoles | Where-Object { $_.Value -eq "ThreatHunting.Read.All" }
New-AzureADServiceAppRoleAssignment -ObjectId $principalId `
  -PrincipalId $principalId -ResourceId $graphSP.ObjectId -Id $role.Id
```

> **Note:** You can run this script from an Azure Cloud Shell. Replace `<LOGIC APP MANAGED IDENTITY OBJECT ID>` with the **Object (Principal) ID** of the Logic App's system-assigned managed identity.  
> Found at: Navigate on the left, under **Settings** → click **Identity** → System assigned → **Object (Principal) ID**
> Also  It may take 30–60 minutes for the permissions to be fully applied and become effective.

---

## 4. Verify

- Go to the Logic App → click **Run Trigger** → **Run**
- Check the run history for a successful execution
- Verify the Adaptive Card appears in the configured Teams channel
