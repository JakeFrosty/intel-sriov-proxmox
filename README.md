# What is this for?
## The script/s in this repository is just to help with setting up SR-IOV Virtual Functions on boot (only tested in a Proxmox Virtual Environment 9.0 Environment)

## Scripts

### sriov_vfs.service
#### credits to original thread, the source of this script: [Proxmox Forum Thread](<https://forum.proxmox.com/threads/enabling-sr-iov-for-intel-nic-x550-t2-on-proxmox-6.56677/>)
#### before executing this script, I highly recommend you use pve-network-interface-pinning to prevent having to redo everything when your ethernet interfaces change names due to PCIe configuration alteration.
#### Functions:
- All **repetitive** commands are run in a for loop.
- Spawn VFs on boot using `/sys/class/net/{devicename}/device/sriov_numvfs`
- Set static MAC addresses for each VFs
- Set trust mode on all VFs
- (Optional) Detach VFs from the host, I recommend turning this feature on when possible in Proxmox to avoid cluttering your /etc/network/interfaces with VF devices
- Destroy VFs on stop by setting `/sys/class/net/{devicename}/device/sriov_numvfs` to 0
- Start the script  `require` and `after` `network.target` and before `pve-firewall.service`

#### Usage
Make sure you have drivers and kernel modules, they should come with PVE by default
- Intel EN82599 chipsets = `ixgbe` and `ixgbevf`
- Intel X710 family =  `i40e` and `iavf`
1. First, make sure the path `/sys/class/net/{devicename}/device/sriov_numvfs` is valid/exists
2. Use your favorite text editor and create this file `/etc/systemd/system/sriov_vfs.service`, copy the contents in [here](<https://github.com/JakeFrosty/intel-sriov-proxmox/blob/main/etc/systemd/system/sriov_vfs.service>)
3. Replace values in the service according to your needs
4. Enable and start the service `systemctl daemon-reload && systemctl enable --now`
5. check the status with `systemctl status sriov_vfs.service`, `lspci -d 8086:*` or `ip link list`

