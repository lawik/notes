NAT traversal seems possible but tricky and being in the middle would likely be simpler

Traversing the NAT would be more efficient and cooler. And would scale in an awesome way

See "what if I don't want multiple interfaces?" https://jrs-s.net/2018/08/05/routing-between-wg-interfaces-with-wireguard/

Should mean one interface per " private. network "

Working! Notes jotted down in NervesCloud slack at the moment.

One interface "many clients". I gave them /24 meaning ~255 IPs and one is the "gateway" or server.

Remaining sport. Nice DNS names. Ideally assign DNS lookups per interface/private network. Make domains like "nh-SERIALNUMBER". I assume dnsmasq can do something like this. Or this might work :D
https://hexdocs.pm/dns/DNS.Server.html#c:handle/2
Or more maintained:
https://github.com/dnsimple/erldns/tree/main

Dnsmasq wikl do it. https://linux.die.net/man/8/dnsmasq
Ignore hosts file, no dhcp, bind interface specifically. (A start: https://stackoverflow.com/questions/9326438/dnsmasq-serve-different-ip-addresses-based-on-interface-used)

Web UI for private network can be plain HTTP and on the gateway. Elixir app that manages the interfaces can also run Phoenix and based on source IP show information about network activity and hosts joined to it. Should work. May need to check some extra to protect against spoofing but should not expose sensitive information anyway. Explicitly close port 80 in firewall.


