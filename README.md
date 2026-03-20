# MikrotikTool MCP Server

A Go-based [MCP (Model Context Protocol)](https://modelcontextprotocol.io/) server that enables [Claude](https://claude.ai) to manage MikroTik RouterOS network devices. Works with **Claude Desktop**, **Claude Code**, and any MCP-compatible client. Single static binary, no dependencies.

## What It Does

MikrotikTool gives Claude **40+ tools** to interact with your MikroTik routers:

- **Connect** to multiple RouterOS 7.1+ devices simultaneously
- **Read** any configuration path, system info, logs, routes, DHCP leases, DNS, VLANs, and more
- **Write** configuration changes with safety protection (safe-mode rollback)
- **Monitor** traffic, logs, and resources in real time
- **Analyze** health, security, firewall rules, routing, and user management
- **Apply** configuration templates (firewall baseline, WireGuard, IPsec, VLAN, DHCP)
- **Visualize** via a built-in web UI with dashboards, topology maps, and traffic charts

### Built-in Web UI

The server includes an embedded React web UI that launches in your browser:

- Dark/light/system theme with persistent toggle
- Multi-device selector -- switch between routers on any page
- Network topology map
- Firewall rule flow visualization
- Real-time traffic dashboard
- Health and security dashboards
- Network discovery (MNDP broadcast + router-based ARP/DHCP/neighbor)

## Quick Start

### 1. Download

Download the binary for your platform from the [Releases](https://github.com/Wibjibit/MikrotikTool-releases/releases) page.

| Platform | Binary |
|----------|--------|
| Windows x64 | `mikrotik-mcp-windows-amd64.exe` |
| Windows ARM64 | `mikrotik-mcp-windows-arm64.exe` |
| Linux x64 | `mikrotik-mcp-linux-amd64` |
| Linux ARM64 | `mikrotik-mcp-linux-arm64` |
| macOS Intel | `mikrotik-mcp-darwin-amd64` |
| macOS Apple Silicon | `mikrotik-mcp-darwin-arm64` |

### 2. Create MCP-User on Your Router

The tool uses a dedicated `MCP-User` account for all connections (hardcoded for security). Create it on each RouterOS device:

```
/user add name=MCP-User password=YOUR_PASSWORD group=full
```

Or via WinBox: System > Users > Add, name `MCP-User`, group `full`.

### 3. Configure Your MCP Client

#### Claude Desktop

Open Settings > Developer > Edit Config, and add to `claude_desktop_config.json`:

**macOS / Linux:**
```json
{
  "mcpServers": {
    "mikrotik": {
      "command": "/path/to/mikrotik-mcp"
    }
  }
}
```

**Windows:**
```json
{
  "mcpServers": {
    "mikrotik": {
      "command": "C:\\Tools\\mikrotik-mcp-windows-amd64.exe"
    }
  }
}
```

Restart Claude Desktop after saving.

#### Claude Code

Add to your MCP settings (`~/.claude.json` or project `.mcp.json`):

```json
{
  "mcpServers": {
    "mikrotik": {
      "command": "/path/to/mikrotik-mcp"
    }
  }
}
```

#### Other MCP Clients

MikrotikTool is a standard MCP server using stdio transport. It works with any MCP-compatible client -- just point the client to the binary.

### 4. Use It

In Claude, just ask:

```
Connect to my router at 192.168.88.1 with password MySecurePass
```

Then:
```
Show me the health status of my router
Analyze the firewall rules
Open the web UI dashboard
List all DHCP leases
Check VPN tunnel status
```

## Connection Fallback

The tool automatically tries multiple connection methods:

1. **HTTPS REST API** (port 443)
2. **HTTP REST API** (port 80) -- if HTTPS is unavailable
3. **API Protocol** (port 8728/8729) -- if no REST API is available

This means it works with almost any RouterOS 7.1+ configuration.

## MCP Tools Reference

### Connection Management
| Tool | Description |
|------|-------------|
| `routeros_connect` | Connect to a RouterOS device |
| `routeros_disconnect` | Disconnect from a device |
| `routeros_list_connections` | List active connections |

### Read Operations
| Tool | Description |
|------|-------------|
| `routeros_list` | List records at any path |
| `routeros_get` | Get a single record by ID |
| `routeros_system_info` | System resource info |
| `routeros_export` | Export configuration |
| `routeros_ping` | Ping from the router |

### Write Operations
| Tool | Description |
|------|-------------|
| `routeros_set` | Update a record |
| `routeros_add` | Create a record |
| `routeros_remove` | Delete a record |
| `routeros_enable` | Enable a record |
| `routeros_disable` | Disable a record |

### Monitoring
| Tool | Description |
|------|-------------|
| `routeros_monitor_traffic` | Monitor interface traffic |
| `routeros_monitor_logs` | Watch system logs |
| `routeros_watch` | Watch any path for changes |

### Analysis and Intelligence
| Tool | Description |
|------|-------------|
| `routeros_health_check` | Comprehensive health checks |
| `routeros_security_audit` | Security posture assessment |
| `routeros_firewall_analyze` | Firewall rule analysis |
| `routeros_firewall_move` | Reorder firewall rules |
| `routeros_dhcp_report` | DHCP intelligence |
| `routeros_top_talkers` | Top bandwidth consumers |
| `routeros_discover_network` | Network neighbor discovery |
| `routeros_vpn_status` | VPN tunnel status |
| `routeros_wifi_status` | WiFi/CAPsMAN status |
| `routeros_backup` | Create backup |
| `routeros_drift_check` | Detect config drift |

### DNS Management
| Tool | Description |
|------|-------------|
| `routeros_dns_status` | DNS settings and cache info |
| `routeros_dns_static_list` | List static DNS entries |
| `routeros_dns_static_add` | Add static DNS entry |
| `routeros_dns_static_remove` | Remove static DNS entry |
| `routeros_dns_cache_flush` | Flush DNS cache |

### Log Intelligence
| Tool | Description |
|------|-------------|
| `routeros_log_search` | Search logs with text/topic filtering |
| `routeros_log_stats` | Log statistics by topic and severity |

### User and Route Management
| Tool | Description |
|------|-------------|
| `routeros_list_users` | User report with security findings |
| `routeros_active_sessions` | Active user sessions |
| `routeros_list_routes` | Route listing with classification |
| `routeros_route_analysis` | Detect routing issues |

### Infrastructure
| Tool | Description |
|------|-------------|
| `routeros_vlan_status` | VLAN interface status |
| `routeros_pool_status` | IP pool utilization |
| `routeros_file_list` | Router file listing |

### Diagnostics and Firmware
| Tool | Description |
|------|-------------|
| `routeros_traceroute` | Traceroute from router |
| `routeros_firmware_status` | Firmware version check |
| `routeros_check_updates` | Check for RouterOS updates |
| `routeros_upgrade` | Upgrade firmware/packages |
| `routeros_wireguard_client_config` | Generate WireGuard client config |

### Templates and Bulk Operations
| Tool | Description |
|------|-------------|
| `routeros_list_templates` | List available config templates |
| `routeros_apply_template` | Apply a configuration template |
| `routeros_bulk` | Execute bulk operations |
| `routeros_list_alerts` | List alert rules |
| `routeros_add_alert` | Add threshold-based alert |

### Audit and Web UI
| Tool | Description |
|------|-------------|
| `routeros_audit_log` | View configuration change history |
| `routeros_open_ui` | Launch web UI in browser |

## Built-in Templates

| Template | Description |
|----------|-------------|
| `firewall_baseline` | Comprehensive firewall: input/forward chains, NAT, MSS clamping, SSH brute-force protection |
| `wireguard_peer` | Add a WireGuard peer to an existing interface |
| `wireguard_s2s` | WireGuard site-to-site tunnel setup |
| `ipsec_s2s` | IPsec site-to-site VPN with pre-shared key |
| `vlan_setup` | Create VLAN with IP address assignment |
| `dhcp_server` | Set up DHCP server on an interface |

## Security

- **Hardcoded username**: All connections use the `MCP-User` account to prevent username enumeration and brute-force attacks
- **Safety engine**: Every write operation is protected with safe-mode rollback (RouterOS 7.18+) or scheduler-based rollback (7.1-7.17)
- **Audit log**: All configuration changes are logged with password/secret sanitization
- **No credential storage**: Passwords are never stored or logged in plaintext

## Requirements

- RouterOS 7.1 or later
- REST API service enabled (`/ip/service enable www-ssl` or `www`)
- `MCP-User` account with `full` group permissions
- An MCP-compatible client: [Claude Desktop](https://claude.ai/download), [Claude Code](https://claude.ai/code), or any other MCP client

## License

MIT License. See source repository for details.

---

Built with Go, React, and the [Model Context Protocol](https://modelcontextprotocol.io/).
