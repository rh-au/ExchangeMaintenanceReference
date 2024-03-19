### ExchangeMaintenanceReference

https://setup.cloud.microsoft/exchange/exchange-update

##### Empty transport queue
`Set-ServerComponentState <ServerName> -Component HubTransport -State Draining -Requester Maintenance`

##### Drain transport queue
`Restart-Service MSExchangeTransport`

##### Drain UM [2016]
`Set-ServerComponentState <ServerName> -Component UMCallRouter -State Draining -Requester Maintenance`

##### Maintenance script
`CD $ExScripts`

`.\StartDagServerMaintenance.ps1 -ServerName <ServerName> -MoveComment Maintenance -PauseClusterNode`

##### Redirect pending messages
`Redirect-Message -Server <ServerName> -Target <Server FQDN>`

##### Maintenance mode
`Set-ServerComponentState <ServerName> -Component ServerWideOffline -State Inactive -Requester Maintenance`

##### Verify maintenance mode [Monitoring+RecoveryActionsEnabled = Active]
`Get-ServerComponentState <ServerName> | Format-Table Component,State -Autosize`

##### Verify no hosted copies
`Get-MailboxServer <ServerName> | Format-List DatabaseCopyAutoActivationPolicy`

##### Verify paused cluster
`Get-ClusterNode <ServerName> | Format-List`

##### Verify queue empty
`Get-Queue`

### Reboot + Update

##### Revert maintenance mode
`Set-ServerComponentState <ServerName> -Component ServerWideOffline -State Active -Requester Maintenance`

##### Accept UM
`Set-ServerComponentState <ServerName> -Component UMCallRouter -State Active -Requester Maintenance`

##### Maintenance script
`CD $ExScripts`
`.\StopDagServerMaintenance.ps1 -serverName <ServerName>`

##### Enable transport queue
`Set-ServerComponentState <ServerName> -Component HubTransport -State Active -Requester Maintenance`

##### Resume transport activity
`Restart-Service MSExchangeTransport`

##### Verify reverted maintenance mode
`Get-ServerComponentState <ServerName> | Format-Table Component,State -Autosize`


