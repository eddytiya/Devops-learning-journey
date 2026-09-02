# Linux for DevOps — Day 3: Networking Commands

Day 3 focused on Linux networking and troubleshooting from an AWS EC2 Ubuntu instance.

## Topics Covered

- Connectivity and packet-loss testing
- Active connections and listening ports
- Network interfaces and IP addresses
- Route and hop analysis
- DNS resolution
- TCP port testing
- HTTP requests and downloads
- Firewall inspection
- Continuous monitoring
- Authorized network scanning

> Sensitive EC2 addresses, SSH-key names, and login information have been removed.

---

## 1. Connectivity Testing

### Continuous connectivity test

```bash
ping google.com
```

Stop it with `Ctrl+C`.

### Force IPv4

```bash
ping -4 google.com
```

### Send exactly four packets

```bash
ping -c 4 google.com
```

The output shows:

- Packets transmitted and received
- Packet-loss percentage
- Minimum, average, and maximum round-trip time

---

## 2. Network Connections and Ports

### Install legacy network tools

```bash
sudo apt install net-tools
```

This package provides `netstat`, `ifconfig`, and `route`.

### Display active connections

```bash
netstat
```

### Display listening TCP and UDP ports

```bash
netstat -tuln
```

Options:

- `-t` — TCP
- `-u` — UDP
- `-l` — listening sockets
- `-n` — numeric addresses and ports

### Display socket information with the modern tool

```bash
ss
```

### Display listening TCP and UDP sockets

```bash
ss -tuln
```

### Include process names and PIDs

```bash
sudo ss -tulpn
```

---

## 3. Network Interfaces and IP Addresses

### Display interface information

```bash
ifconfig
```

### Inspect an AWS interface

```bash
ifconfig ens5
```

Interface names can differ between systems.

### Display all interfaces

```bash
ifconfig -a
```

### Display addresses using the modern command

```bash
ip address show
```

Short form:

```bash
ip addr
```

### Display the hostname

```bash
hostname
```

### Display the server’s IP addresses

```bash
hostname -I
```

The `I` is uppercase.

### Display wireless-interface information

```bash
iwconfig
```

Install it when required:

```bash
sudo apt install wireless-tools
```

AWS servers normally have no wireless extensions.

---

## 4. Route and Path Analysis

### Install traceroute

```bash
sudo apt install traceroute
```

### Trace the route to a destination

```bash
traceroute youtube.com
```

### Install the alternative traceroute implementation

```bash
sudo apt install inetutils-traceroute
```

### Trace the path using tracepath

```bash
tracepath google.com
```

### Combine route and latency analysis

```bash
mtr google.com
```

### Display the legacy routing table

```bash
route
```

Numeric output:

```bash
route -n
```

### Display routes using the modern command

```bash
ip route
```

---

## 5. DNS Troubleshooting

### Look up a hostname

```bash
nslookup www.google.com
```

### Install DNS utilities

```bash
sudo apt install dnsutils
```

This provides `nslookup` and `dig`.

### Perform a detailed DNS query

```bash
dig example.com
```

### Display only resolved addresses

```bash
dig +short example.com
```

DNS tools expect a hostname:

```bash
nslookup example.com
```

They should not receive a complete URL such as:

```text
https://example.com/page
```

---

## 6. TCP Port Testing

### Test port 80

```bash
telnet example.com 80
```

To leave Telnet:

1. Press `Ctrl+]`
2. Enter:

```text
quit
```

---

## 7. HTTP Requests with Curl

### Request a web page

```bash
curl https://example.com
```

### Explicit GET request

```bash
curl -X GET https://example.com
```

GET is already Curl’s default HTTP method.

### Request only response headers

```bash
curl -I https://example.com
```

### Save the response to a file

```bash
curl -o page.html https://example.com
```

Verify it:

```bash
ls -l page.html
```

---

## 8. Downloads with Wget

### Download an existing file

```bash
wget https://example.com/path/to/file.zip
```

### Choose the output filename

```bash
wget -O downloaded-file.zip https://example.com/path/to/file.zip
```

An HTTP `404 Not Found` response means the requested resource does not exist.

---

## 9. Firewall Inspection

### Display IPv4 firewall rules

```bash
sudo iptables -L
```

### Display numeric, verbose rules and counters

```bash
sudo iptables -L -n -v
```

The practice server displayed the following chains:

- `INPUT`
- `FORWARD`
- `OUTPUT`

These commands inspect rules; they do not change them.

---

## 10. Continuous Monitoring

### Refresh the date continuously

```bash
watch date
```

### Continuously monitor listening sockets

```bash
watch ss -tuln
```

The default refresh interval is approximately two seconds. Press `Ctrl+C` to stop.

---

## 11. Network Scanning

### Install Nmap

```bash
sudo apt install nmap
```

### Scan an authorized demonstration host

```bash
nmap example.com
```

Nmap reports:

- Whether the host is reachable
- Port numbers
- Port states
- Detected services

> Only scan systems you own or have explicit permission to test.

---

## Errors Corrected During Practice

| Incorrect attempt | Correct command | Lesson |
|---|---|---|
| `ping c -4 google.com` | `ping -c 4 google.com` | Options require the hyphen and correct order |
| `netstat google.com` | `netstat` or `netstat -tuln` | Netstat examines local network state |
| `ifconfig eth0` | `ifconfig ens5` | Interface names differ between systems |
| `raceroute youtube.com` | `traceroute youtube.com` | Command spelling matters |
| `taceroute youtube.com` | `traceroute youtube.com` | Command spelling matters |
| `nslookup https://example.com/page` | `nslookup example.com` | DNS tools expect a hostname |
| `id addr` | `ip addr` | `id` and `ip` are different commands |
| `sudo apt install iwconfig` | `sudo apt install wireless-tools` | `iwconfig` belongs to wireless-tools |
| `curl -X Get URL` | `curl -X GET URL` | HTTP method should be uppercase |
| `wget` with a nonexistent URL | Use a valid file URL | HTTP 404 means the file was not found |

---

## What I Learned

- Packet loss measures connectivity reliability.
- Round-trip time measures network latency.
- `LISTEN` means a service is waiting for connections.
- `ESTABLISHED` means a connection is active.
- DNS converts hostnames into IP addresses.
- A full URL and a hostname are not the same thing.
- Interface names depend on the operating system and environment.
- `ss` and `ip` are modern alternatives to `netstat` and `ifconfig`.
- HTTP status codes help explain request results.
- Firewall inspection and port scanning should be performed carefully.
- Nmap must only be used against authorized targets.

## Day 3 Progress

- [x] Tested connectivity and latency
- [x] Examined active connections
- [x] Inspected listening ports
- [x] Reviewed network interfaces
- [x] Traced network paths
- [x] Queried DNS records
- [x] Tested a TCP port
- [x] Made HTTP requests
- [x] Downloaded web content
- [x] Reviewed firewall rules
- [x] Monitored commands continuously
- [x] Performed an authorized Nmap scan
- [x] Diagnosed and corrected command errors
