# FluxDNS

A DNS routing solution that manages and filters DNS traffic using Docker Compose and multiple specialized containers.

## Overview

FluxDNS intelligently routes DNS queries based on domain location:

- Chinese domains and IPs are resolved using AliDNS and DNSPod
- Non-Chinese domains and IPs are resolved using Cloudflare DNS (via proxy)

This setup allows for faster DNS resolution based on geographic relevance and improves access to global resources.

## Architecture

The system consists of the following components:

- **[AdGuardHome](https://github.com/AdguardTeam/AdGuardHome)**: Acts as the main DNS server with ad-blocking capabilities
- **[MosDNS](https://github.com/IrineSistiana/mosdns)**: Handles DNS traffic routing based on domain and IP lists
- **ClashMeta**: Provides the SOCKS proxy for accessing Cloudflare DNS
- **MetaCubeXD**: Web UI for managing the ClashMeta proxy

### How It Works

1. DNS queries first arrive at **AdGuardHome** which provides ad-blocking and security filtering
2. Queries are forwarded to **MosDNS** which decides where to route them based on domain/IP lists
3. For Chinese domains, MosDNS forwards requests to AliDNS (223.5.5.5) and DNSPod (1.12.12.12)
4. For non-Chinese domains, requests are forwarded to Cloudflare DNS (1.1.1.2) via the **ClashMeta** proxy
5. Results are returned to the client through AdGuardHome

This approach offers both clean DNS resolution and optimal query speed by using geographically appropriate DNS servers.

## Prerequisites

- Docker and Docker Compose
- Access to port 53 (DNS) if you want to use it as your system DNS server
- Open ports as specified in the compose file

## Quick Start

1. Clone this repository:

   ```bash
   git clone https://github.com/jianxx/fluxdns.git
   cd fluxdns
   ```

2. Prepare directories for configuration.

   ```bash
   mkdir -p adguardhome/config adguardhome/work mihomo mosdns
   ```

3. Set up your proxy configuration:

   - Drop your crafted ClashMeta `config.yaml` into the `mihomo` directory
   - This powers the proxy that ensures your DNS queries sail smoothly to Cloudflare

4. Configure mosdns:

   - Place your mosdns configuration files in the `mosdns` directory
   - These ruleset files determine which DNS paths your queries will travel

5. Start the services:

   ```bash
   docker compose up -d
   ```

6. Access the various management interfaces:

   - AdGuardHome: http://localhost:3000 or http://10.89.2.2:3000
   - MetaCubeXD (ClashMeta management): http://localhost:28000 or http://10.89.2.4:80

7. Configure your device to use FluxDNS:
   - Set your DNS server to the IP address of the machine running FluxDNS

## License

See the [LICENSE](LICENSE) file for details.
