# Comprehensive Documentation for Awesome Shodan Queries

## Table of Contents

1. [Introduction to Shodan](#introduction-to-shodan)
2. [Getting Started](#getting-started)
3. [Understanding Search Queries](#understanding-search-queries)
4. [Advanced Search Techniques](#advanced-search-techniques)
5. [Interpreting Results](#interpreting-results)
6. [Security and Ethical Guidelines](#security-and-ethical-guidelines)
7. [Common Use Cases](#common-use-cases)
8. [Troubleshooting](#troubleshooting)
9. [Resources and Further Reading](#resources-and-further-reading)

---

## Introduction to Shodan

### What is Shodan?

Shodan is a search engine for internet-connected devices, often referred to as "the search engine for the Internet of Things (IoT)." Unlike traditional search engines like Google that index web content, Shodan indexes information about servers, webcams, routers, industrial control systems, and virtually any device connected to the internet.

### How Shodan Works

Shodan continuously scans the entire IPv4 address space, attempting to connect to common ports and services. When a connection is successful, Shodan captures the banner information - the metadata that devices broadcast about themselves. This includes:

- Server type and version
- Operating system
- Open ports
- SSL certificate information
- HTML titles and content
- Geographic location
- Organization/ISP information
- Default credentials status (for some devices)

### Why This Repository Exists

This repository serves multiple purposes:

1. **Security Research**: Helps security professionals identify vulnerable systems for responsible disclosure
2. **Awareness**: Demonstrates the risks of exposing devices to the internet without proper security
3. **Education**: Teaches about network security, IoT vulnerabilities, and proper system hardening
4. **Threat Intelligence**: Allows organizations to identify exposed assets in their infrastructure

---

## Getting Started

### Creating a Shodan Account

1. Visit [Shodan.io](https://www.shodan.io/)
2. Click "Create Account" or sign up using GitHub/Google
3. Verify your email address
4. Consider upgrading to a paid account for:
   - Unlimited search result pages
   - Access to advanced filters
   - Historical data
   - API access
   - Download capabilities

### Account Types

- **Free Account**: Limited to first page of results, basic filters
- **Membership ($59/lifetime)**: Most filters, more results, data downloads
- **Enterprise**: API access, historical data, custom scans

### Basic Navigation

1. **Search Bar**: Enter your query
2. **Filters**: Narrow results by country, city, organization, port, etc.
3. **Facets**: Left sidebar shows distribution of results by various attributes
4. **Map View**: Visualize geographic distribution of results
5. **Images**: Browse screenshots of web interfaces (when available)

---

## Understanding Search Queries

### Query Syntax

Shodan queries use a combination of search terms and filters:

```
[search term] [filter1:value1] [filter2:value2]
```

### Common Filters

| Filter | Description | Example |
|--------|-------------|---------|
| `city:` | Filter by city name | `city:"New York"` |
| `country:` | Filter by country code | `country:US` |
| `geo:` | Filter by coordinates | `geo:40.7,-74.0,10` |
| `hostname:` | Filter by hostname | `hostname:google.com` |
| `net:` | Filter by IP range (CIDR) | `net:216.0.0.0/8` |
| `org:` | Filter by organization | `org:"Harvard University"` |
| `os:` | Filter by operating system | `os:Windows` |
| `port:` | Filter by port number | `port:22` |
| `before:` | Results before date | `before:01/01/2020` |
| `after:` | Results after date | `after:01/01/2020` |
| `has_screenshot:` | Only results with screenshots | `has_screenshot:true` |
| `http.title:` | Filter by HTTP page title | `http.title:"Dashboard"` |
| `http.html:` | Search within HTML content | `http.html:"admin"` |
| `http.favicon.hash:` | Filter by favicon hash | `http.favicon.hash:123456` |
| `ssl:` | Filter by SSL certificate data | `ssl:google` |
| `product:` | Filter by product name | `product:Apache` |

### Search Operators

- **Quotes**: Exact phrase matching - `"authentication disabled"`
- **Minus**: Exclude results - `-authentication`
- **OR**: Logical OR (must be uppercase) - `"SSH" OR "Telnet"`
- **AND**: Logical AND (implied, can be explicit) - `apache AND version`
- **Parentheses**: Group operations - `(ssh OR telnet) port:22`

### Examples from This Repository

#### Simple Query
```
"Server: Prismview Player"
```
Searches for devices with this exact server header.

#### Query with Filter
```
"in-tank inventory" port:10001
```
Searches for gas station pump controllers on a specific port.

#### Complex Query
```
http.title:"Nordex Control" "Windows 2000 5.0 x86" "Jetty/3.1 (JSP 1.1; Servlet 2.2; java 1.6.0_14)"
```
Combines multiple criteria to find specific wind turbine control systems.

#### Negative Filter Query
```
HP-ILO-4 !"HP-ILO-4/2.53" !"HP-ILO-4/2.54" port:1900
```
Finds HP iLO devices while excluding patched versions.

---

## Advanced Search Techniques

### Using HTTP Headers

HTTP headers provide rich information:

```
# Find servers by specific header
"Server: nginx/1.10.3"

# Search by custom headers
"X-Powered-By: PHP/5.3.0"

# Content-Type filtering
http.component:"wordpress"
```

### SSL Certificate Hunting

```
# Find certificates by organization
ssl.cert.subject.cn:"*.company.com"

# Expired certificates
ssl.cert.expired:true

# Self-signed certificates
ssl.cert.issuer.cn:localhost
```

### Favicon Hashing

Unique favicons help identify specific applications:

```
http.favicon.hash:1307375944
```

To generate your own favicon hash:
```python
import mmh3
import requests
import codecs

response = requests.get('http://example.com/favicon.ico')
favicon = codecs.encode(response.content, 'base64')
hash = mmh3.hash(favicon)
print(hash)
```

### Combining Filters Effectively

```
# Find vulnerable Elasticsearch instances in a specific region
product:"Elastic" port:9200 country:US -authentication

# Locate webcams in educational institutions
webcam org:university has_screenshot:true

# Industrial systems in specific IP range
scada net:10.0.0.0/8
```

### Using Wildcards and Regex

While Shodan doesn't support full regex, you can use wildcards in some cases:

```
# Partial matching often works without explicit wildcards
hostname:*.google.com
```

### Time-Based Searches

```
# Recent discoveries
after:01/01/2024

# Historical comparison
"Apache" before:01/01/2020

# Specific time range
"mongodb" after:01/06/2024 before:30/06/2024
```

---

## Interpreting Results

### Understanding Result Pages

Each search result typically shows:

1. **IP Address**: The device's public IP
2. **Ports**: Open ports discovered
3. **Services**: Identified services running
4. **Organization**: ISP or hosting provider
5. **Location**: Geographic location (approximate)
6. **Banner**: Raw banner data captured
7. **Timestamp**: When Shodan last scanned this device

### Reading Banners

Banners contain the actual response from the device:

```
HTTP/1.1 200 OK
Server: nginx/1.14.0
Date: Wed, 16 Nov 2025 12:00:00 GMT
Content-Type: text/html
```

This reveals:
- Protocol version (HTTP/1.1)
- Server software (nginx 1.14.0)
- Response code (200 OK)
- Content type (text/html)

### Vulnerability Indicators

Look for:

- **Outdated software versions**: May contain known vulnerabilities
- **Default credentials**: "default password" in banner
- **Missing authentication**: "authentication disabled"
- **Verbose error messages**: Revealing internal paths or versions
- **Unnecessary services**: FTP, Telnet on production systems

### False Positives

Not all results are vulnerable or publicly accessible:

- Some systems may have IP-based restrictions not visible to Shodan
- Authentication prompts don't mean the system is accessible
- Banner information may be intentionally misleading (honeypots)
- Some results may be sandboxed research environments

---

## Security and Ethical Guidelines

### Legal Considerations

**IMPORTANT**: Unauthorized access to computer systems is illegal in most jurisdictions under laws like:

- **USA**: Computer Fraud and Abuse Act (CFAA)
- **UK**: Computer Misuse Act 1990
- **EU**: Convention on Cybercrime (Budapest Convention)
- **Others**: Similar laws exist globally

### Ethical Usage

#### ✅ DO:

- Use for authorized security assessments
- Search for your own organization's exposed assets
- Conduct academic research with proper authorization
- Report vulnerabilities through responsible disclosure
- Use for security awareness and education
- Respect privacy and data protection laws

#### ❌ DON'T:

- Attempt to access systems you don't own or have authorization for
- Use default credentials to "test" access
- Download or exfiltrate data from discovered systems
- Share specific IP addresses or credentials publicly
- Use findings for malicious purposes
- Ignore responsible disclosure protocols
- Harass or intimidate system owners

### Responsible Disclosure

If you discover a vulnerability:

1. **Document**: Screenshot and note the finding (without accessing the system)
2. **Identify Owner**: Use WHOIS or contact information to find responsible party
3. **Private Contact**: Reach out privately, not publicly
4. **Provide Details**: Clear description, location, potential impact
5. **Allow Time**: Give 90 days for remediation before public disclosure
6. **Follow Up**: Work cooperatively with the organization
7. **No Demands**: Don't demand payment or recognition as condition

### Protecting Your Own Systems

If you're a system administrator:

1. **Regular Shodan Checks**: Search for your organization regularly
2. **Use Shodan Alerts**: Set up monitoring for your IP ranges
3. **Minimize Exposure**: Only expose necessary services
4. **Strong Authentication**: Disable default credentials
5. **Update Regularly**: Keep software patched
6. **Firewall Rules**: Implement strict ingress/egress rules
7. **VPNs**: Use VPNs for administrative access
8. **Security Headers**: Configure proper security headers
9. **Disable Banners**: Minimize information disclosure

---

## Common Use Cases

### 1. Asset Discovery (Authorized)

Organizations can use Shodan to:

- Identify all internet-facing assets
- Discover shadow IT
- Find forgotten or legacy systems
- Audit cloud deployments
- Monitor acquisitions' infrastructure

**Example Workflow**:
```
# Step 1: Search by organization
org:"Your Company Name"

# Step 2: Search by owned IP ranges
net:203.0.113.0/24

# Step 3: Search by domains
hostname:yourcompany.com

# Step 4: Export and analyze results
```

### 2. Vulnerability Research

Security researchers use Shodan to:

- Assess vulnerability prevalence
- Track patch deployment rates
- Identify vulnerable IoT devices
- Study attack surfaces

**Example**:
```
# Find systems vulnerable to specific CVE
"OpenSSL 1.0.1" before:01/04/2014

# Track patching progress
product:"Apache Struts" "2.3.5"
```

### 3. Threat Intelligence

Security teams can:

- Identify command and control servers
- Track malware infrastructure
- Monitor botnet activity
- Find compromised systems

### 4. IoT Security Research

Researchers studying IoT security:

- Catalog device types and vulnerabilities
- Study default configuration issues
- Assess manufacturer security practices
- Track IoT malware spread

### 5. Competitive Intelligence (Legal)

Understanding industry technology usage:

- Common platforms in your sector
- Technology adoption rates
- Infrastructure patterns
- Security posture benchmarking

---

## Troubleshooting

### No Results Found

**Possible Causes**:
- Query too specific - try broader terms
- Shodan hasn't scanned that service recently
- Service is behind a firewall
- Free account limitations

**Solutions**:
- Simplify your query
- Remove some filters
- Try related search terms
- Check if paid features are needed

### Too Many Results

**Solutions**:
- Add more specific filters
- Use exact phrase matching with quotes
- Combine multiple filters
- Use negative filters to exclude irrelevant results

### Unexpected Results

**Issues**:
- Results don't match query intent
- Getting unrelated devices
- Country filter not working as expected

**Solutions**:
- Review query syntax
- Use quotes for exact matching
- Check for typos in filter names
- Verify filter values are correct

### Results Are Outdated

Shodan's database is updated continuously but not in real-time:

- Most popular services: scanned monthly
- Less common services: scanned quarterly
- New IP ranges: may take time to appear
- Use `after:` filter to see recent scans

### Can't Access Results

**Free Account Limitations**:
- Only first page of results
- Limited filter access
- No data export
- No API access

**Solution**: Consider upgrading to a paid membership

---

## Resources and Further Reading

### Official Shodan Resources

- **Main Site**: [https://www.shodan.io/](https://www.shodan.io/)
- **Help Documentation**: [https://help.shodan.io/](https://help.shodan.io/)
- **Blog**: [https://blog.shodan.io/](https://blog.shodan.io/)
- **API Documentation**: [https://developer.shodan.io/](https://developer.shodan.io/)
- **Shodan Images**: [https://images.shodan.io/](https://images.shodan.io/)
- **Shodan Maps**: [https://maps.shodan.io/](https://maps.shodan.io/)

### Shodan CLI and API

**Python Library**:
```bash
pip install shodan
```

**Basic Usage**:
```python
import shodan

api = shodan.Shodan('YOUR_API_KEY')
result = api.search('apache')

print(f'Results found: {result["total"]}')
for service in result['matches']:
    print(service['ip_str'])
```

### Books and Guides

- **"Complete Guide to Shodan"** by SHODAN LLC
- **"The Security Researcher's Handbook"** - Shodan chapter
- **SANS Reading Room** - Papers on internet scanning

### Related Tools

- **Censys**: Alternative internet scanning platform
- **ZoomEye**: Chinese search engine for cyberspace
- **BinaryEdge**: Internet scanning and data platform
- **GreyNoise**: Internet background noise analysis
- **URLScan.io**: URL and website scanner
- **SecurityTrails**: Historical DNS and WHOIS data

### Security Communities

- **r/netsec**: Reddit network security community
- **r/AskNetsec**: Security Q&A community
- **OWASP**: Open Web Application Security Project
- **SANS ISC**: Internet Storm Center
- **Krebs on Security**: Security news and investigations

### CVE Databases

- **CVE.org**: Common Vulnerabilities and Exposures
- **NVD**: National Vulnerability Database
- **ExploitDB**: Exploit database
- **VulnDB**: Commercial vulnerability database

### Responsible Disclosure Programs

- **HackerOne**: Vulnerability coordination platform
- **Bugcrowd**: Crowdsourced security platform
- **CERT/CC**: Coordinates vulnerability disclosure
- **GitHub Security Advisories**: For open source projects

### Training and Certification

- **Shodan Fundamentals**: Free certification from Shodan
- **CEH**: Certified Ethical Hacker
- **OSCP**: Offensive Security Certified Professional
- **GPEN**: GIAC Penetration Tester
- **eWPT**: eLearnSecurity Web Penetration Tester

---

## Appendix: Query Cheat Sheet

### Quick Reference

```
# Basic search
search_term

# Exact phrase
"exact phrase"

# Exclude term
-excluded_term

# Location filters
city:"New York"
country:US
geo:40.7,-74.0,10

# Network filters
hostname:example.com
net:192.168.1.0/24
org:"Company Name"
port:80,443,8080

# Service filters
product:Apache
os:Windows
http.title:"Dashboard"
http.component:"PHP"

# Time filters
before:01/01/2020
after:01/01/2024

# Boolean
term1 OR term2
term1 term2  (implicit AND)
-term1

# Advanced
has_screenshot:true
has_ssl:true
http.favicon.hash:123456
ssl.cert.expired:true
vuln:CVE-2014-0160
```

---

## Contributing to This Documentation

Found an error or want to improve this documentation? Please:

1. Check the [contributing guidelines](contributing.md)
2. Open an issue describing the improvement
3. Submit a pull request with your changes

---

## Disclaimer

This documentation is for educational and authorized security testing purposes only. The authors and contributors assume no liability for misuse of this information. Always obtain proper authorization before testing systems you don't own or have permission to test.

**Remember**: With great power comes great responsibility. Use these tools ethically and legally.

---

**Last Updated**: November 2025
**Version**: 1.0
**Maintained by**: awesome-shodan-queries contributors
