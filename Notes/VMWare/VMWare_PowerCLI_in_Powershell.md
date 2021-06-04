---
tags: [VMWare]
title: VMWare_PowerCLI_in_Powershell
created: '2020-01-30T20:16:16.547Z'
modified: '2020-02-17T21:05:18.011Z'
---

# VMWare PowerCLI in Powershell
Created Friday 12 April 2019

## PowerCLI Install instructions for Unbuntu: 
<https://jjasghar.github.io/blog/2018/03/22/powercli-10+-on-linux/>
Notes: <https://www.petri.com/use-powercli-create-snapshot>

## Open Powershell
`pwsh`

## Connect to VCenter Server
`Connect-VIServer -Server <connect_IP>`

## Ignore Cert Errors
`Set-PowerCLIConfiguration -InvalidCertificateAction Ignore -Confirm:$false`

## Disconnect from VCenter
`Disconnect-VIServer`

## To show VMs
`Get-VM`

## Show snapshots
`Get-VM | Get-Snapshot | select vm,name,description,created, @{n="SizeGB"; e={[math]::Round($_.sizegb,2)}}  | ft -autosize`

## Remove Snapshots
`Get-Snapshot VMname | Remove-Snapshot`

## Remove Snapshot without asking
`Get-Snapshot AMTPPercona01 | Remove-Snapshot -confirm:$false`

## Make Snapshot with Memory
`get-vm petri-1 | new-snapshot -Name "Before Updates" -Quiesce -Memory`

## Make Snapshot without Memory
`get-vm gAPI20Percona01 | new-snapshot -Name "Before Updates"`

## Show available tags
`get-tag`

## Get a list of VMs with a certain tag
`get-vm -tag "Development"`

## Assign a tag to a VM
`New-TagAssignment -Tag "Development" -Entity "DevgAPI20Percona03"`

## Get list of tags assigned to a VM. 
`Get-TagAssignment -Entity "DevgAPI20Percona03"`

## Get List of VMs in folder 
`Get-Folder "Corp" |Get-VM`

## Snapshot all VMs with the tag Development
`get-vm -tag "Development" | foreach {get-vm $_ | new-snapshot -Name "Before Updates"}`

## Clear snapshots on all VMs with the tag Development
`get-vm -tag "Development" | foreach {Get-Snapshot $_ | Remove-Snapshot -confirm:$false}`

## Show Powered off VMs
`Get-VM | where-object {$_.PowerState –eq “PoweredOff”}`

## Get tags for all VMs
`Get-VM | foreach {Get-TagAssignment -Entity $_}`

## Show VMs without tags
`get-vm | ?{ (get-tagassignment $_) -eq $null}`

## Show VMs in Perham Cluster without tags
`get-cluster -Location Perham |get-vm | ?{ (get-tagassignment $_) -eq $null}`

## Show VMs in Perham Cluster without the tag category "Class"
`get-cluster -Location Perham |Get-VM | ?{(Get-TagAssignment -Entity $_ -Category Class) -eq $null} | Where{Get-TagAssignment -Entity $_ -Category Class} | Select Name,@{N="Tag";E={(Get-TagAssignment $_.name).tag}}`


## Get IP of all VMs
`get-datacenter | where-object {$_.Name -notmatch "MobiTV"} |get-vm | Select Name, @{N="IP";[E={@($_.Guest.IPAddress](mailto:E={@($_.Guest.IPAddress))}}`

## Get Configured OS and Running OS
`get-datacenter | where-object {$_.Name -notmatch "MobiTV"} |get-vm  | Get-View -Property @("Name", "Config.GuestFullName", "Guest.GuestFullName") | Select -Property Name, @{N="Configured OS";E={$_.Config.GuestFullName}},  @{N="Running OS";E={$_.Guest.GuestFullName}} | Format-Table -AutoSize`

## Get Running OS for all VMs
`get-datacenter | where-object {$_.Name -notmatch "MobiTV"} |get-vm  | Get-View -Property @("Name", "Guest.GuestFullName") | Select Name, @{N="GuestOS";[E={@($_.Guest.GuestFullName](mailto:E={@($_.Guest.GuestFullName))}}`

## Get Running OS and exlude all non Linux
`get-datacenter | where-object {$_.Name -notmatch "MobiTV"} |get-vm  | Get-View -Property @("Name", "Guest.GuestFullName") |  where-object {$_.Guest.GuestFullName -notmatch "Windows |Other"} | Select Name, @{N="GuestOS";[E={@($_.Guest.GuestFullName](mailto:E={@($_.Guest.GuestFullName))}}`

`get-datacenter | where-object {$_.Name -notmatch "MobiTV"} |get-vm  | Get-View -Property @("Name", "Guest.GuestFullName","[Guest.IPAddress](mailto:E={@($_.Guest.IPAddress)") |  where-object {$_.Guest.GuestFullName -notmatch "Windows |Other"} | Select -Property Name, @{N="GuestOS";E={$_.Guest.GuestFullName}},  @{N="GuestIP";E={$_.Guest.IPAddress}} | Format-Table -AutoSize`

`get-datacenter | where-object {$_.Name -notmatch "MobiTV"} | Get-VM | Select Name, @{N="IP";[E={@($_.guest.IPaddress](mailto:E={@($_.guest.IPaddress))}} | foreach { write-host $_.Name $_.IP}`

## Template to VM for updates procedure
```
Set-Template -Template "CentOS-7.6.1810-CIS-STIG-July19" -ToVM
Start-VM -VM "CentOS-7.6.1810-CIS-STIG-July19" -Confirm:$false -RunAsync
#VM Update happens here
Get-VM "CentOS-7.6.1810-CIS-STIG-July19" | Shutdown-VMGuest -Confirm:$false
Get-VM -Name "CentOS-7.6.1810-CIS-STIG-July19" | Set-VM -ToTemplate -Confirm:$false
Set-Template -Template "CentOS-7.6.1810-CIS-STIG-July19" -Name "CentOS-7.6.1810-CIS-STIG-Aug19"
```

```
Set-Template -Template "Ubuntu-16.04-CIS-STIG-Aug19" -ToVM
Start-VM -VM "Ubuntu-16.04-CIS-STIG-Aug19" -Confirm:$false -RunAsync 

Start-Sleep -s 15

ansible-playbook --ask-vault-pass ./templateubuntu_update.yml

Get-VM "Ubuntu-16.04-CIS-STIG-Aug19" | Shutdown-VMGuest -Confirm:$false
Start-Sleep -s 15
Get-VM -Name "Ubuntu-16.04-CIS-STIG-Aug19" | Set-VM -ToTemplate -Confirm:$false
Set-Template -Template "Ubuntu-16.04-CIS-STIG-Aug19" -Name "Ubuntu-16.04-CIS-STIG-Sept19"
```







