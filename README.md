# IPv6 from REEF
My ISP (REEF) makes available (officialy) /61 for my IPv6 connectivity
in ISP (REEF) Webpanel we can set 8x Next-Hop for each /64

# WARNING
If you configure a Next Hop for the first subnet, it will no longer be advertised by the REEFbox on your network.
This will prevent IPv6-dependent devices from functioning without a third-party router that manages this subnet.

# REEFbox (Firewall IPv6 :OFF, DHCPv6 :OFF, DNS IPv6 :ON)
Default IPv6 /64 on the LAN side
Using :
-RA + SLAAC
-RDNSS

# WHAT IS DISCOVER
⚠️ VLANs are not required to use the full /56 prefix provided by REEF
Thanks to NDP proxying (ndppd), the REEFbox knows that all addresses within the /56 are located behind the router, even if you use a single unsegmented LAN.
VLANs remain purely an organizational or security choice, not a technical requirement!


# /etc/ndppd.conf
cat /etc/ndppd.conf
proxy enp45s0 {
    router yes
    timeout 500
    rule 2a01:e0a:2eb:8810::/64 {
        static
    }
    rule 2a01:xxx:xxx:8820::/64 {
        static
    }
}

# /etc/radvd/conf
interface br0 {
    AdvSendAdvert on;
#    AdvManagedFlag off; # needed for pick up any /64 of the /56
#    AdvOtherFlag off;   # needed for pick up any /64 of the /56
    prefix 2a01:e0a:2eb:8810::/64 {
        AdvOnLink on;
        AdvAutonomous on;
    };
    prefix 2a01:e0a:2eb:8820::/64 {
        AdvOnLink on;
        AdvAutonomous on;
    };
    # DNS ipv6 from google  |  2a01:e00::1 (freebox itself)
    RDNSS 2001:4860:4860::8844 2001:4860:4860::8888 {};
};


# /etc/sysctl.d/99-ipv6.conf
# OLD working config
net.ipv6.conf.all.forwarding=1
net.ipv6.conf.br0.accept_ra=2

# New config | !!!! in ipv6 LAN is equal to WAN !!!!
#net.ipv6.conf.all.forwarding = 1

# WAN (Freebox)
#net.ipv6.conf.enp45s0.accept_ra = 0
#net.ipv6.conf.enp45s0.autoconf = 0

# LAN
#net.ipv6.conf.br0.accept_ra = 0
#net.ipv6.conf.br0.autoconf = 0




