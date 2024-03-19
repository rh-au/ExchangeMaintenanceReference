## ExchangeMaintenanceReference

$S = "Server"

#Drain hub transport service 
Set-ServerComponentState -Identity $S -Component HubTransport -State Draining -Requester Maintenance

#Redirect queued messages -> Use FQDN
Redirect-Message -Server $S -Target "target.domain.com"

#Suspend from DAG
Suspend-ClusterNode $S

#Disable database copy automatic activeion -> DB copies to DAG members
Set-MailboxServer $S -DatabaseCopyActivationDisabledAndMoveNow $true

#Get DB copy automatic activation policy -> Note
Get-MailboxServer $S | Select-Object DatabaseCopyAutoActivationPolicy

#Block DB copy automatic activation policy
Set-MailboxServer $S -DatabaseCopyAutoActivationPolicy Blocked

#Get mounted DB copies -> Perform switchover if not auto
Get-MailboxDatabaseCopyStatus -Server $S | Where-Object {$_.Status -eq "Mounted"} | Format-Table -AutoSize
Move-ActiveMailboxDatabase -Server $S -ActivateOnServer "target" -SkipMoveSuppressionChecks -Confirm:$false

#Verify transport queue empty
Get-Queue

#Set maintenance mode -> 
Set-ServerComponentState $S -Component ServerWideOffline -State Inactive -Requester Maintenance
