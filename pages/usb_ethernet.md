# Ethernet-over-USB network

Specifically to v2. When combined with configuring a DNS server, FTP, or SMB (for example), this is a powerful way to extend the capabilities of Pi-KVM.

:exclamation: The USB-Ethernet features are experimental, so some of the default settings may be changed in future releases.

1. Edit `/etc/kvmd/override.yaml` (remove `{}` if this your first configuration entry) and add these lines:
    ``` yaml
    otg:
        devices:
            ethernet:
                enabled: true
                driver: ecm
                host_mac: 48:6f:73:74:50:43
                kvm_mac: 42:61:64:55:53:42
    ```
    The `host_mac` address will be used on the server's network interface. The `kvm_mac` means the address that will be assigned to the local interface on the Pi-KVM. The KVM interface will be called `usb0`.r's network interface. If the `host_mac` or `kvm_mac` is not specified, a random value will be used. The `driver` parameter means the protocol that will be used for the USB network. The default value is `ecm` so it can be passed it this example. Other possible values are `eem`, `ncm` and `rndis`.
2. To automatically configure the USB network on the server recommended using the service `kvmd-otgnet`. It configures the firewall, assigns an address to the local Pi-KVM interface `usb0` and starts DHCP so the managed server can get the IPv4 address. By default, the address `169.254.0.1/28` to interface `usb0` will be assigned. One of the other addresses from the network `169.254.0.0./28` will be assigned to the server when it requests it via DHCP. For security reasons, all incoming connections from the server to the Pi-KVM side are blocked (except for ICMP and UDP port 67 which is used for DHCP). If you want to allow access from the server to the Pi-KVM interface, then you need to add ports 80 and 443 to the whitelist using `/etc/kvmd/override.yaml` file like this:
    ```yaml
    otgnet:
        firewall:
            allow_tcp: [80, 443]
    ```
    To view other available configuration parameters, use the command `kvmd -m`.
3. To enable the service, use the command `systemctl enable kvmd-otgnet`.
4. Perform `reboot`.
