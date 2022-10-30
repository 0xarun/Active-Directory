# Get all users in the current domain
Get-DomainUser | select -ExpandProperty cn

# Get all computers in the current domain
Get-DomainComputer

# Get all domains in current forest
Get-ForestDomain

# Get domain/forest trusts
Get-DomainTrust
Get-ForestTrust

# Get information for the DA group
Get-DomainGroup "Domain Admins"

# Find members of the DA group
Get-DomainGroupMember "Domain Admins" | select -ExpandProperty membername

# Find interesting shares in the domain, ignore default shares, and check access
Find-DomainShare -ExcludeStandard -ExcludePrint -ExcludeIPC -CheckShareAccess

# Get OUs for current domain
Get-DomainOU -FullData

# Get computers in an OU
# %{} is a looping statement
Get-DomainOU -name Servers | %{ Get-DomainComputer -SearchBase $_.distinguishedname } | select dnshostname

# Get GPOs applied to a specific OU
Get-DomainOU *WS* | select gplink
Get-DomainGPO -Name "{3E04167E-C2B6-4A9A-8FB7-C811158DC97C}"

# Get Restricted Groups set via GPOs, look for interesting group memberships forced via domain
Get-DomainGPOLocalGroup -ResolveMembersToSIDs | select GPODisplayName, GroupName, GroupMemberOf, GroupMembers

# Get the computers where users are part of a local group through a GPO restricted group
Get-DomainGPOUserLocalGroupMapping -LocalGroup Administrators | select ObjectName, GPODisplayName, ContainerName, ComputerName

# Find principals that can create new GPOs in the domain
Get-DomainObjectAcl -SearchBase "CN=Policies,CN=System,DC=targetdomain,DC=com" -ResolveGUIDs | ?{ $_.ObjectAceType -eq "Group-Policy-Container" } | select ObjectDN, ActiveDirectoryRights, SecurityIdentifier

# Find principals that can link GPOs to OUs
Get-DomainOU | Get-DomainObjectAcl -ResolveGUIDs | ? { $_.ObjectAceType -eq "GP-Link" -and $_.ActiveDirectoryRights -match "WriteProperty" } | select ObjectDN, SecurityIdentifier

# Get incoming ACL for a specific object
Get-DomainObjectAcl -SamAccountName "Domain Admins" -ResolveGUIDs | Select IdentityReference,ActiveDirectoryRights

# Find interesting ACLs for the entire domain, show in a readable (left-to-right) format
Find-InterestingDomainAcl | select identityreferencename,activedirectoryrights,acetype,objectdn | ?{$_.IdentityReferenceName -NotContains "DnsAdmins"} | ft

# Get interesting outgoing ACLs for a specific user or group
# ?{} is a filter statement
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReference -match "Domain Admins"} | select ObjectDN,ActiveDirectoryRights