# Windows Server RDS Learning Path 02 — Create the Isolated RDS Lab Network

**Level:** Beginner · **Phase:** Domain foundation · **Module:** 2/70

## Goal
Create an isolated Hyper-V NAT network for the domain and RDS servers without exposing lab DNS or DHCP to the physical LAN.

## Prerequisites
- Activity 01 plan
- Hyper-V enabled
- Elevated PowerShell

## Setup
1. Verify `10.30.30.0/24` does not overlap LAN, VPN, WSL, Docker or another hypervisor network.
2. Create an Internal switch named `RDS-LAB-NAT`.
3. Assign `10.30.30.1/24` to the host-side virtual adapter.
4. Create a NAT object for `10.30.30.0/24`.
5. Attach all future lab VMs to this switch.

```powershell
$Switch='RDS-LAB-NAT'
New-VMSwitch -Name $Switch -SwitchType Internal
$Nic=Get-NetAdapter -Name "vEthernet ($Switch)"
New-NetIPAddress -InterfaceIndex $Nic.ifIndex -IPAddress 10.30.30.1 -PrefixLength 24
New-NetNat -Name $Switch -InternalIPInterfaceAddressPrefix 10.30.30.0/24

Get-VMSwitch -Name $Switch
Get-NetIPAddress -InterfaceAlias "vEthernet ($Switch)" -AddressFamily IPv4
Get-NetNat -Name $Switch
```

## Evidence
Capture route-overlap checks, switch configuration, host adapter address, NAT state, VM attachment and connectivity tests. Save actual outputs/screenshots under `evidence/` together with `findings.md`, including errors, root cause, remediation and final pass/fail state.

## Troubleshooting
If outbound access fails, verify host connectivity, gateway `10.30.30.1`, VM subnet and NAT prefix. If VMs cannot communicate, verify they use the same switch and test Windows Firewall separately.

## Security
Do not bridge experimental AD DNS, DHCP or RDS systems directly onto a production, customer or home LAN. NAT reduces exposure but is not a malware-containment boundary.

## Rollback
Disconnect dependent VMs, then remove the NAT and switch only when later modules no longer require them.

## References
- https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/virtual-switch
- https://learn.microsoft.com/en-us/powershell/module/netnat/new-netnat

## Next
`Windows-Server-RDS-Learning-Path-03-Install-Windows-Server-for-DC01`
