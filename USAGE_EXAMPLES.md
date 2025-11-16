# Shodan Usage Examples and Command Reference

## Table of Contents

1. [Web Interface Examples](#web-interface-examples)
2. [Command Line Interface (CLI)](#command-line-interface-cli)
3. [Python API Examples](#python-api-examples)
4. [Step-by-Step Tutorials](#step-by-step-tutorials)
5. [Real-World Scenarios](#real-world-scenarios)
6. [Advanced Techniques](#advanced-techniques)
7. [Automation Scripts](#automation-scripts)
8. [Quick Reference](#quick-reference)

---

## Web Interface Examples

### Example 1: Finding Unprotected Databases

**Objective**: Find MongoDB databases without authentication

**Step-by-Step**:

1. Go to https://www.shodan.io/
2. Enter the following query:
   ```
   "MongoDB Server Information" port:27017 -authentication
   ```
3. Press Enter or click Search
4. Review results - you'll see:
   - IP addresses of exposed databases
   - Port numbers (typically 27017)
   - Version information
   - Geographic location
5. Click on any result to see full details

**Understanding Results**:
```
IP: 203.0.113.45
Port: 27017
Organization: Example Hosting Inc.
Location: New York, USA

Banner Information:
MongoDB Server Information
  version: 3.4.2
  authentication: disabled
  databases: 12
```

**What This Means**:
- This MongoDB instance is publicly accessible
- No authentication is required
- Contains 12 databases
- Running older version 3.4.2

**Ethical Action**: If this is your organization, secure it immediately. If not, do NOT access it.

---

### Example 2: Finding Webcams by Location

**Objective**: Find webcams in a specific city (for authorized testing only)

**Query**:
```
"Server: yawcam" city:"London"
```

**Alternative with Multiple Filters**:
```
"Server: IP Webcam Server" country:GB city:"London" has_screenshot:true
```

**Refining Results**:
```
# Exclude password-protected cameras
"Server: yawcam" city:"London" -401 -authentication

# Only show recent scans
"Server: yawcam" city:"London" after:01/01/2024
```

---

### Example 3: Identifying Industrial Control Systems

**Objective**: Find SCADA/ICS systems in your organization's network

**Query for Siemens Systems**:
```
"Siemens, SIMATIC" org:"Your Company Name"
```

**Query for Multiple ICS Types**:
```
(scada OR "industrial control" OR SIMATIC) org:"Your Company Name"
```

**With Network Range** (if you know your IP space):
```
"Siemens, SIMATIC" net:203.0.113.0/24
```

---

### Example 4: Finding Specific Vulnerabilities

**Objective**: Identify systems affected by CVE-2017-5689 (Intel AMT)

**Query**:
```
"Intel(R) Active Management Technology" port:623,664,16992,16993,16994,16995
```

**Narrowing to Your Organization**:
```
"Intel(R) Active Management Technology" port:623,664 org:"Your Company"
```

**Excluding Honeypots** (systems that appear to be traps):
```
"Intel(R) Active Management Technology" port:623,664 -honeypot
```

---

### Example 5: Complex Boolean Queries

**Objective**: Find multiple types of remote access systems

**Query**:
```
("authentication disabled" "RFB") OR ("root@" port:23) OR ("Android Debug Bridge" port:5555)
```

**Breaking it Down**:
- `("authentication disabled" "RFB")` - Unprotected VNC
- `OR` - Boolean operator
- `("root@" port:23)` - Telnet with root access
- `OR` - Another Boolean operator
- `("Android Debug Bridge" port:5555)` - ADB over network

---

## Command Line Interface (CLI)

### Installing Shodan CLI

```bash
# Install using pip
pip install shodan

# Or using pip3
pip3 install shodan

# Verify installation
shodan --version
```

### Setting Up API Key

```bash
# Initialize with your API key (get from https://account.shodan.io/)
shodan init YOUR_API_KEY_HERE

# Verify it works
shodan info
```

**Expected Output**:
```
Query Credits: 100
Scan Credits: 100
Plan: Dev
```

---

### Basic CLI Commands

#### 1. Search Command

```bash
# Basic search
shodan search "apache"

# Search with count
shodan count "nginx"

# Save results to file
shodan search --fields ip_str,port,org "mongodb" > results.txt

# Limit number of results
shodan search --limit 10 "docker"
```

**Example Output**:
```
$ shodan search --limit 3 "mongodb"
198.51.100.23:27017
203.0.113.45:27017
192.0.2.67:27017
```

#### 2. Host Information

```bash
# Get info about specific IP
shodan host 203.0.113.45

# Get specific fields only
shodan host 203.0.113.45 --fields ip_str,port,org,os
```

**Example Output**:
```
$ shodan host 203.0.113.45
IP: 203.0.113.45
Organization: Example Hosting
Operating System: Linux
Ports: 22, 80, 443, 27017

Services:
  22/tcp - OpenSSH 7.4
  80/tcp - nginx 1.14.0
  443/tcp - nginx 1.14.0
  27017/tcp - MongoDB 3.4.2
```

#### 3. Download Full Results

```bash
# Download complete data (requires API subscription)
shodan download mongodb-results 'product:mongodb -authentication'

# Parse downloaded data
shodan parse --fields ip_str,port,org mongodb-results.json.gz
```

#### 4. Statistics

```bash
# Get statistics on search results
shodan stats "apache" --facets country,org

# Get top ports for a search
shodan stats "nginx" --facets port
```

**Example Output**:
```
$ shodan stats "nginx" --facets country
Top 10 Results for facet "country":
1. United States    45,234
2. China           32,123
3. Germany         21,456
4. France          18,900
5. Russia          15,678
```

---

### Advanced CLI Examples

#### Example 1: Finding Your Organization's Exposed Assets

```bash
# Search by organization name
shodan search 'org:"Your Company Name"' --fields ip_str,port,product

# Search by hostname
shodan search 'hostname:yourcompany.com' --fields ip_str,port,product

# Search by IP range
shodan search 'net:203.0.113.0/24' --fields ip_str,port,product
```

#### Example 2: Monitoring for New Exposures

```bash
# Create an alert for your IP range (requires membership)
shodan alert create "Company Network" 203.0.113.0/24

# List your alerts
shodan alert list

# Get triggered alerts
shodan alert info ALERT_ID
```

#### Example 3: Streaming Real-Time Data

```bash
# Stream real-time data (requires subscription)
shodan stream

# Filter stream by specific criteria
shodan stream --ports 22,23,3389

# Filter by country
shodan stream --countries US,GB,DE
```

#### Example 4: Combining with Other Tools

```bash
# Extract IPs and scan with nmap
shodan search "apache" --fields ip_str --limit 100 | nmap -iL - -sV

# Extract IPs for further investigation
shodan search 'org:"Your Company"' --fields ip_str > company_ips.txt

# Check if specific IPs are in Shodan
cat ip_list.txt | while read ip; do shodan host $ip; done
```

---

## Python API Examples

### Setup

```python
# Install library
# pip install shodan

import shodan

# Initialize API
api_key = "YOUR_API_KEY_HERE"
api = shodan.Shodan(api_key)
```

---

### Example 1: Basic Search

```python
import shodan

api = shodan.Shodan('YOUR_API_KEY')

try:
    # Search Shodan
    results = api.search('apache')

    # Show results
    print(f'Results found: {results["total"]}')

    for result in results['matches']:
        print(f'IP: {result["ip_str"]}')
        print(f'Port: {result["port"]}')
        print(f'Organization: {result.get("org", "N/A")}')
        print(f'Operating System: {result.get("os", "N/A")}')
        print('-' * 40)

except shodan.APIError as e:
    print(f'Error: {e}')
```

**Output**:
```
Results found: 45234567
IP: 203.0.113.45
Port: 80
Organization: Example Hosting
Operating System: Linux
----------------------------------------
IP: 198.51.100.23
Port: 443
Organization: Cloud Provider Inc.
Operating System: Ubuntu
----------------------------------------
```

---

### Example 2: Getting Host Information

```python
import shodan

api = shodan.Shodan('YOUR_API_KEY')

try:
    # Lookup host
    host = api.host('203.0.113.45')

    # Print general info
    print(f"""
    IP: {host['ip_str']}
    Organization: {host.get('org', 'N/A')}
    Operating System: {host.get('os', 'N/A')}
    """)

    # Print all services
    for item in host['data']:
        print(f"""
        Port: {item['port']}
        Banner: {item['data']}
        """)

except shodan.APIError as e:
    print(f'Error: {e}')
```

---

### Example 3: Searching with Filters

```python
import shodan

api = shodan.Shodan('YOUR_API_KEY')

# Define search query with filters
query = 'product:mongodb -authentication country:US'

try:
    results = api.search(query, page=1)

    print(f'Results found: {results["total"]}')

    for result in results['matches']:
        print(f'IP: {result["ip_str"]}:{result["port"]}')

        # Extract MongoDB version if available
        if 'version' in result:
            print(f'Version: {result["version"]}')

except shodan.APIError as e:
    print(f'Error: {e}')
```

---

### Example 4: Automated Monitoring Script

```python
import shodan
import time
from datetime import datetime

api = shodan.Shodan('YOUR_API_KEY')

def monitor_organization(org_name):
    """Monitor an organization for exposed services"""

    query = f'org:"{org_name}"'

    try:
        results = api.search(query)

        print(f'[{datetime.now()}] Scan Results for {org_name}')
        print(f'Total exposed services: {results["total"]}')

        # Group by port
        ports = {}
        for result in results['matches']:
            port = result['port']
            ports[port] = ports.get(port, 0) + 1

        print('\nServices by Port:')
        for port, count in sorted(ports.items(), key=lambda x: x[1], reverse=True):
            print(f'  Port {port}: {count} instances')

        # Check for concerning services
        concerning_ports = {
            23: 'Telnet',
            3389: 'RDP',
            27017: 'MongoDB',
            5900: 'VNC',
            6379: 'Redis'
        }

        print('\nPotentially Concerning Exposures:')
        for port, service in concerning_ports.items():
            if port in ports:
                print(f'  ⚠️  {service} (port {port}): {ports[port]} instances')

    except shodan.APIError as e:
        print(f'Error: {e}')

# Run monitor
monitor_organization('Your Company Name')
```

**Output**:
```
[2025-11-16 12:34:56] Scan Results for Your Company Name
Total exposed services: 127

Services by Port:
  Port 443: 45 instances
  Port 80: 38 instances
  Port 22: 25 instances
  Port 27017: 8 instances
  Port 3389: 6 instances
  Port 23: 3 instances
  Port 5900: 2 instances

Potentially Concerning Exposures:
  ⚠️  Telnet (port 23): 3 instances
  ⚠️  RDP (port 3389): 6 instances
  ⚠️  MongoDB (port 27017): 8 instances
  ⚠️  VNC (port 5900): 2 instances
```

---

### Example 5: Checking for Specific Vulnerabilities

```python
import shodan
import json

api = shodan.Shodan('YOUR_API_KEY')

def check_vulnerability(cve_id, org_name=None):
    """Check for systems vulnerable to specific CVE"""

    # Build query
    if org_name:
        query = f'vuln:{cve_id} org:"{org_name}"'
    else:
        query = f'vuln:{cve_id}'

    try:
        results = api.search(query)

        print(f'Checking for {cve_id}')
        print(f'Vulnerable systems found: {results["total"]}')

        if org_name:
            print(f'In organization: {org_name}')

        # List vulnerable hosts
        for result in results['matches']:
            print(f'\nVulnerable Host:')
            print(f'  IP: {result["ip_str"]}:{result["port"]}')
            print(f'  Organization: {result.get("org", "N/A")}')
            print(f'  Product: {result.get("product", "N/A")}')

            # Get vulnerability details
            if 'vulns' in result:
                for vuln in result['vulns']:
                    print(f'  CVE: {vuln}')

    except shodan.APIError as e:
        print(f'Error: {e}')

# Check for Heartbleed in your organization
check_vulnerability('CVE-2014-0160', 'Your Company Name')
```

---

### Example 6: Export to CSV

```python
import shodan
import csv

api = shodan.Shodan('YOUR_API_KEY')

def export_to_csv(query, filename):
    """Export Shodan results to CSV"""

    try:
        results = api.search(query)

        with open(filename, 'w', newline='') as csvfile:
            fieldnames = ['ip', 'port', 'organization', 'os', 'product', 'version']
            writer = csv.DictWriter(csvfile, fieldnames=fieldnames)

            writer.writeheader()

            for result in results['matches']:
                writer.writerow({
                    'ip': result['ip_str'],
                    'port': result['port'],
                    'organization': result.get('org', ''),
                    'os': result.get('os', ''),
                    'product': result.get('product', ''),
                    'version': result.get('version', '')
                })

        print(f'Exported {len(results["matches"])} results to {filename}')

    except shodan.APIError as e:
        print(f'Error: {e}')

# Export MongoDB results
export_to_csv('product:mongodb -authentication', 'mongodb_exposed.csv')
```

---

## Step-by-Step Tutorials

### Tutorial 1: Finding and Securing Your Organization's Assets

**Objective**: Discover what internet-facing assets your organization has

**Prerequisites**:
- Shodan account (free or paid)
- Authorization from your organization
- Your organization's name or IP ranges

**Step 1: Identify Your Organization**

```bash
# Try searching by organization name
shodan search 'org:"Your Company Name"' --fields ip_str,port,product
```

**Step 2: Search by IP Ranges**

If you know your IP ranges:
```bash
# Single IP range
shodan search 'net:203.0.113.0/24' --fields ip_str,port,product

# Multiple IP ranges
shodan search 'net:203.0.113.0/24,198.51.100.0/24' --fields ip_str,port,product
```

**Step 3: Search by Hostname**

```bash
# Main domain
shodan search 'hostname:yourcompany.com' --fields ip_str,port,product

# Include subdomains
shodan search 'hostname:*.yourcompany.com' --fields ip_str,port,product
```

**Step 4: Analyze Results**

Save results to file:
```bash
shodan search 'org:"Your Company"' > company_assets.txt
```

Look for:
- ✅ Expected services (web servers, email, etc.)
- ⚠️ Unexpected services (databases, admin panels, etc.)
- 🚨 Concerning exposures (Telnet, unprotected databases, etc.)

**Step 5: Create Alerts** (Requires membership)

```bash
# Monitor your IP range
shodan alert create "Company Network" 203.0.113.0/24

# Get notifications of changes
shodan alert list
```

**Step 6: Remediate**

For each unexpected exposure:
1. Verify it's actually yours
2. Determine if it needs to be public
3. If not, firewall it or add authentication
4. If yes, ensure it's properly secured
5. Document the decision

---

### Tutorial 2: Checking for Common Vulnerabilities

**Objective**: Scan for well-known vulnerabilities in your infrastructure

**Step 1: Create Vulnerability Query List**

```python
vulnerabilities = {
    'Heartbleed': 'vuln:CVE-2014-0160',
    'Shellshock': 'vuln:CVE-2014-6271',
    'EternalBlue': 'vuln:MS17-010',
    'Intel AMT': 'vuln:CVE-2017-5689',
    'BlueKeep': 'vuln:CVE-2019-0708'
}
```

**Step 2: Check Each Vulnerability**

```python
import shodan

api = shodan.Shodan('YOUR_API_KEY')
org_name = 'Your Company Name'

for vuln_name, query in vulnerabilities.items():
    full_query = f'{query} org:"{org_name}"'

    try:
        results = api.search(full_query)
        count = results['total']

        if count > 0:
            print(f'🚨 {vuln_name}: {count} vulnerable systems found!')
            for result in results['matches'][:5]:  # Show first 5
                print(f'  - {result["ip_str"]}:{result["port"]}')
        else:
            print(f'✅ {vuln_name}: No vulnerabilities found')

    except shodan.APIError:
        print(f'⚠️  {vuln_name}: Unable to check')
```

**Step 3: Generate Report**

```python
import shodan
from datetime import datetime

def generate_vuln_report(org_name, output_file):
    api = shodan.Shodan('YOUR_API_KEY')

    with open(output_file, 'w') as f:
        f.write(f'Vulnerability Scan Report\n')
        f.write(f'Organization: {org_name}\n')
        f.write(f'Date: {datetime.now()}\n')
        f.write(f'=' * 60 + '\n\n')

        for vuln_name, query in vulnerabilities.items():
            full_query = f'{query} org:"{org_name}"'

            try:
                results = api.search(full_query)
                count = results['total']

                f.write(f'{vuln_name}: {count} systems\n')

                if count > 0:
                    for result in results['matches']:
                        f.write(f'  IP: {result["ip_str"]}:{result["port"]}\n')
                        f.write(f'  Product: {result.get("product", "Unknown")}\n')
                        f.write(f'  Version: {result.get("version", "Unknown")}\n\n')

            except shodan.APIError as e:
                f.write(f'{vuln_name}: Error - {e}\n')

    print(f'Report saved to {output_file}')

generate_vuln_report('Your Company Name', 'vuln_report.txt')
```

---

### Tutorial 3: Monitoring Competitors (Ethical Intelligence)

**Objective**: Understand technology choices in your industry (legally)

**Note**: Only search publicly available information. Do NOT access systems.

**Step 1: Identify Technology Patterns**

```bash
# Check web server usage
shodan stats 'org:"Competitor Name"' --facets product

# Check programming languages
shodan stats 'org:"Competitor Name"' --facets http.component
```

**Step 2: Compare Technology Stacks**

```python
import shodan

api = shodan.Shodan('YOUR_API_KEY')

companies = ['Company A', 'Company B', 'Company C', 'Your Company']

for company in companies:
    query = f'org:"{company}"'

    try:
        results = api.search(query, facets=[('product', 10)])

        print(f'\n{company}:')
        print(f'  Total services: {results["total"]}')
        print(f'  Top products:')

        for item in results['facets']['product']:
            print(f'    {item["value"]}: {item["count"]}')

    except shodan.APIError as e:
        print(f'{company}: Error - {e}')
```

**Step 3: Analyze Security Posture (Read-Only)**

```python
def analyze_security_posture(org_name):
    api = shodan.Shodan('YOUR_API_KEY')

    # Security indicators
    indicators = {
        'Unencrypted Web': 'port:80 org:"{}" -port:443',
        'Legacy SSL': 'ssl.version:SSLv2 org:"{}"',
        'Telnet': 'port:23 org:"{}"',
        'Open Databases': 'product:mongodb -authentication org:"{}"',
        'RDP Exposed': 'port:3389 org:"{}"'
    }

    print(f'\nSecurity Analysis for {org_name}:')

    for indicator, query_template in indicators.items():
        query = query_template.format(org_name)

        try:
            results = api.search(query)
            count = results['total']

            if count > 0:
                print(f'  ⚠️  {indicator}: {count} instances')
            else:
                print(f'  ✅ {indicator}: None found')

        except shodan.APIError:
            print(f'  ❓ {indicator}: Unable to check')

analyze_security_posture('Your Company Name')
```

---

## Real-World Scenarios

### Scenario 1: Incident Response

**Situation**: You suspect a breach and need to identify all external exposures quickly.

**Solution**:

```python
import shodan
from datetime import datetime, timedelta

api = shodan.Shodan('YOUR_API_KEY')

def emergency_asset_discovery(org_name):
    """Quick asset discovery during incident response"""

    print(f'=== EMERGENCY ASSET DISCOVERY ===')
    print(f'Organization: {org_name}')
    print(f'Time: {datetime.now()}')
    print('=' * 50)

    # Get all assets
    query = f'org:"{org_name}"'
    results = api.search(query, limit=1000)

    print(f'\nTotal Exposed Services: {results["total"]}')

    # Group by IP
    ips = {}
    for result in results['matches']:
        ip = result['ip_str']
        if ip not in ips:
            ips[ip] = []
        ips[ip].append(result['port'])

    print(f'Unique IP Addresses: {len(ips)}')

    # Print all IPs and their open ports
    print('\nComplete Asset List:')
    for ip, ports in sorted(ips.items()):
        print(f'{ip}: {", ".join(map(str, ports))}')

    # Flag high-risk services
    high_risk = {
        22: 'SSH',
        23: 'Telnet',
        3389: 'RDP',
        5900: 'VNC',
        27017: 'MongoDB'
    }

    print('\nHigh-Risk Exposures:')
    for result in results['matches']:
        if result['port'] in high_risk:
            service = high_risk[result['port']]
            print(f'🚨 {result["ip_str"]}:{result["port"]} - {service}')

    # Save to file
    filename = f'emergency_scan_{datetime.now().strftime("%Y%m%d_%H%M%S")}.txt'
    with open(filename, 'w') as f:
        f.write(f'Emergency Asset Discovery\n')
        f.write(f'Organization: {org_name}\n')
        f.write(f'Time: {datetime.now()}\n\n')

        for ip, ports in sorted(ips.items()):
            f.write(f'{ip}: {", ".join(map(str, ports))}\n')

    print(f'\nResults saved to: {filename}')

emergency_asset_discovery('Your Company Name')
```

---

### Scenario 2: Mergers & Acquisitions Due Diligence

**Situation**: Your company is acquiring another company. Assess their security posture.

**Solution**:

```python
import shodan
import json

api = shodan.Shodan('YOUR_API_KEY')

def ma_security_assessment(company_name):
    """Security assessment for M&A due diligence"""

    report = {
        'company': company_name,
        'timestamp': datetime.now().isoformat(),
        'findings': {}
    }

    # 1. Overall exposure
    query = f'org:"{company_name}"'
    results = api.search(query)
    report['findings']['total_services'] = results['total']

    # 2. Check for known vulnerabilities
    vulns = ['CVE-2014-0160', 'CVE-2017-5689', 'MS17-010']
    report['findings']['vulnerabilities'] = {}

    for vuln in vulns:
        vuln_query = f'vuln:{vuln} org:"{company_name}"'
        try:
            vuln_results = api.search(vuln_query)
            report['findings']['vulnerabilities'][vuln] = vuln_results['total']
        except:
            report['findings']['vulnerabilities'][vuln] = 'Unable to check'

    # 3. Check for sensitive exposures
    sensitive = {
        'databases': 'product:mongodb org:"{}" -authentication',
        'admin_panels': 'http.title:"admin" org:"{}"',
        'backup_files': 'http.html:".sql" org:"{}"',
        'git_repos': '.git org:"{}"'
    }

    report['findings']['sensitive_exposures'] = {}

    for category, query_template in sensitive.items():
        query = query_template.format(company_name)
        try:
            results = api.search(query)
            report['findings']['sensitive_exposures'][category] = results['total']
        except:
            report['findings']['sensitive_exposures'][category] = 0

    # 4. Technology stack
    facet_query = f'org:"{company_name}"'
    facet_results = api.search(facet_query, facets=[('product', 20)])

    report['findings']['technology_stack'] = []
    for item in facet_results['facets']['product']:
        report['findings']['technology_stack'].append({
            'product': item['value'],
            'count': item['count']
        })

    # Generate report
    print(f'\n=== M&A SECURITY ASSESSMENT ===')
    print(f'Target Company: {company_name}')
    print(f'Assessment Date: {datetime.now()}')
    print('=' * 50)

    print(f'\nOverall Exposure: {report["findings"]["total_services"]} services')

    print('\nKnown Vulnerabilities:')
    for vuln, count in report['findings']['vulnerabilities'].items():
        if isinstance(count, int) and count > 0:
            print(f'  🚨 {vuln}: {count} systems')
        elif isinstance(count, int):
            print(f'  ✅ {vuln}: None found')

    print('\nSensitive Exposures:')
    for category, count in report['findings']['sensitive_exposures'].items():
        if count > 0:
            print(f'  ⚠️  {category}: {count} instances')

    print('\nTop Technologies:')
    for tech in report['findings']['technology_stack'][:10]:
        print(f'  - {tech["product"]}: {tech["count"]}')

    # Risk score (simple calculation)
    risk_score = 0
    risk_score += sum(v for v in report['findings']['vulnerabilities'].values() if isinstance(v, int))
    risk_score += sum(report['findings']['sensitive_exposures'].values()) * 2

    print(f'\n📊 Risk Score: {risk_score}')
    if risk_score > 50:
        print('   Assessment: HIGH RISK')
    elif risk_score > 20:
        print('   Assessment: MODERATE RISK')
    else:
        print('   Assessment: LOW RISK')

    # Save report
    filename = f'ma_assessment_{company_name.replace(" ", "_")}.json'
    with open(filename, 'w') as f:
        json.dump(report, f, indent=2)

    print(f'\nDetailed report saved to: {filename}')

    return report

# Run assessment
ma_security_assessment('Target Company Name')
```

---

### Scenario 3: Red Team Reconnaissance

**Situation**: Authorized penetration test - gather OSINT before engagement.

**Solution**:

```python
import shodan

api = shodan.Shodan('YOUR_API_KEY')

def red_team_recon(target_org, target_domains, target_ips):
    """Reconnaissance for authorized red team engagement"""

    print('=== RED TEAM RECONNAISSANCE ===')
    print('AUTHORIZED ENGAGEMENT ONLY')
    print('=' * 50)

    all_assets = []

    # 1. Organization search
    print('\n[1] Searching by Organization...')
    org_query = f'org:"{target_org}"'
    org_results = api.search(org_query)

    for result in org_results['matches']:
        all_assets.append({
            'ip': result['ip_str'],
            'port': result['port'],
            'service': result.get('product', 'Unknown'),
            'banner': result.get('data', '')[:100]
        })

    print(f'    Found {len(all_assets)} services')

    # 2. Domain searches
    print('\n[2] Searching by Domains...')
    for domain in target_domains:
        domain_query = f'hostname:{domain}'
        domain_results = api.search(domain_query)
        print(f'    {domain}: {domain_results["total"]} results')

    # 3. IP range searches
    print('\n[3] Searching by IP Ranges...')
    for ip_range in target_ips:
        ip_query = f'net:{ip_range}'
        ip_results = api.search(ip_query)
        print(f'    {ip_range}: {ip_results["total"]} results')

    # 4. Identify interesting targets
    print('\n[4] Identifying High-Value Targets...')

    interesting_services = {
        'web': [80, 443, 8080, 8443],
        'remote_access': [22, 23, 3389, 5900],
        'databases': [1433, 3306, 5432, 27017],
        'file_shares': [21, 445, 2049]
    }

    targets_by_category = {k: [] for k in interesting_services.keys()}

    for asset in all_assets:
        for category, ports in interesting_services.items():
            if asset['port'] in ports:
                targets_by_category[category].append(asset)

    for category, targets in targets_by_category.items():
        if targets:
            print(f'\n    {category.upper()}:')
            for target in targets[:5]:  # Show first 5
                print(f'      {target["ip"]}:{target["port"]} - {target["service"]}')

    # 5. Generate target list for tools
    print('\n[5] Generating Target Lists...')

    # Create Nmap target file
    with open('nmap_targets.txt', 'w') as f:
        ips = set(asset['ip'] for asset in all_assets)
        for ip in sorted(ips):
            f.write(f'{ip}\n')
    print('    Nmap targets: nmap_targets.txt')

    # Create detailed asset list
    with open('detailed_assets.txt', 'w') as f:
        for asset in all_assets:
            f.write(f'{asset["ip"]}:{asset["port"]} - {asset["service"]}\n')
    print('    Detailed assets: detailed_assets.txt')

    print('\n=== RECONNAISSANCE COMPLETE ===')

# Run recon (with proper authorization)
red_team_recon(
    target_org='Target Company Name',
    target_domains=['target.com', '*.target.com'],
    target_ips=['203.0.113.0/24', '198.51.100.0/24']
)
```

---

## Advanced Techniques

### Technique 1: Favicon Hash Generation and Search

```python
import requests
import mmh3
import codecs

def generate_favicon_hash(url):
    """Generate Shodan-compatible favicon hash"""

    try:
        response = requests.get(url, timeout=5)
        favicon = codecs.encode(response.content, 'base64')
        hash_value = mmh3.hash(favicon)
        return hash_value
    except Exception as e:
        return None

# Generate hash
url = 'http://example.com/favicon.ico'
hash_value = generate_favicon_hash(url)

print(f'Favicon hash: {hash_value}')

# Search for systems with same favicon
query = f'http.favicon.hash:{hash_value}'
print(f'Search query: {query}')
```

**Use Case**: Find all systems using the same web application

---

### Technique 2: Historical Data Analysis

```python
import shodan

api = shodan.Shodan('YOUR_API_KEY')

def analyze_historical_exposure(ip_address):
    """Analyze historical data for an IP (requires membership)"""

    try:
        host = api.host(ip_address, history=True)

        print(f'Historical Analysis for {ip_address}')
        print('=' * 50)

        # Group by timestamp
        timeline = {}
        for item in host['data']:
            timestamp = item['timestamp'][:10]  # YYYY-MM-DD

            if timestamp not in timeline:
                timeline[timestamp] = []

            timeline[timestamp].append({
                'port': item['port'],
                'service': item.get('product', 'Unknown')
            })

        # Show changes over time
        for date in sorted(timeline.keys()):
            print(f'\n{date}:')
            for service in timeline[date]:
                print(f'  Port {service["port"]}: {service["service"]}')

    except shodan.APIError as e:
        print(f'Error: {e}')

analyze_historical_exposure('203.0.113.45')
```

---

### Technique 3: Combining Filters for Precision

```python
def build_precision_query(params):
    """Build highly specific Shodan query"""

    filters = []

    # Product and version
    if 'product' in params:
        filters.append(f'product:{params["product"]}')
    if 'version' in params:
        filters.append(f'version:{params["version"]}')

    # Network location
    if 'org' in params:
        filters.append(f'org:"{params["org"]}"')
    if 'country' in params:
        filters.append(f'country:{params["country"]}')
    if 'city' in params:
        filters.append(f'city:"{params["city"]}"')

    # Service characteristics
    if 'port' in params:
        filters.append(f'port:{params["port"]}')
    if 'ssl' in params:
        filters.append(f'ssl:"{params["ssl"]}"')

    # Exclusions
    if 'exclude' in params:
        for term in params['exclude']:
            filters.append(f'-{term}')

    return ' '.join(filters)

# Example: Find specific vulnerable nginx in US
query = build_precision_query({
    'product': 'nginx',
    'version': '1.10.3',
    'country': 'US',
    'port': '443',
    'exclude': ['cloudflare', 'amazon']
})

print(query)
# Output: product:nginx version:1.10.3 country:US port:443 -cloudflare -amazon
```

---

## Automation Scripts

### Script 1: Daily Security Monitoring

```bash
#!/bin/bash
# daily_shodan_monitor.sh

API_KEY="YOUR_API_KEY"
ORG_NAME="Your Company Name"
EMAIL="security@yourcompany.com"

# Get current date
DATE=$(date +%Y-%m-%d)

# Create report directory
mkdir -p reports/$DATE

# Run searches
echo "Running daily Shodan monitoring for $ORG_NAME..."

# 1. Overall exposure
shodan search "org:\"$ORG_NAME\"" --fields ip_str,port,product > reports/$DATE/all_services.txt
TOTAL=$(wc -l < reports/$DATE/all_services.txt)

# 2. High-risk services
shodan search "org:\"$ORG_NAME\" port:23" > reports/$DATE/telnet.txt
shodan search "org:\"$ORG_NAME\" port:3389" > reports/$DATE/rdp.txt
shodan search "org:\"$ORG_NAME\" product:mongodb -authentication" > reports/$DATE/mongodb.txt

# 3. Generate summary
cat > reports/$DATE/summary.txt <<EOF
Daily Shodan Security Report
Date: $DATE
Organization: $ORG_NAME

Total Exposed Services: $TOTAL

High-Risk Exposures:
- Telnet (port 23): $(wc -l < reports/$DATE/telnet.txt)
- RDP (port 3389): $(wc -l < reports/$DATE/rdp.txt)
- MongoDB (no auth): $(wc -l < reports/$DATE/mongodb.txt)

Full details in reports/$DATE/

EOF

# 4. Email report (if mailutils is installed)
if command -v mail &> /dev/null; then
    cat reports/$DATE/summary.txt | mail -s "Daily Shodan Report - $DATE" $EMAIL
fi

echo "Report generated: reports/$DATE/summary.txt"
```

**Setup**:
```bash
chmod +x daily_shodan_monitor.sh

# Add to crontab for daily execution
crontab -e
# Add: 0 8 * * * /path/to/daily_shodan_monitor.sh
```

---

### Script 2: Continuous Monitoring with Python

```python
#!/usr/bin/env python3
# continuous_monitor.py

import shodan
import time
import smtplib
from email.mime.text import MIMEText
from datetime import datetime

class ShodanMonitor:
    def __init__(self, api_key, org_name):
        self.api = shodan.Shodan(api_key)
        self.org_name = org_name
        self.baseline = {}

    def scan(self):
        """Perform a scan"""
        query = f'org:"{self.org_name}"'
        results = self.api.search(query)

        # Build current state
        current = {}
        for result in results['matches']:
            key = f"{result['ip_str']}:{result['port']}"
            current[key] = {
                'product': result.get('product', 'Unknown'),
                'version': result.get('version', 'Unknown')
            }

        return current

    def compare(self, current):
        """Compare current scan with baseline"""
        changes = {
            'new': [],
            'removed': [],
            'changed': []
        }

        # Find new services
        for key in current:
            if key not in self.baseline:
                changes['new'].append(key)

        # Find removed services
        for key in self.baseline:
            if key not in current:
                changes['removed'].append(key)

        # Find changed services
        for key in current:
            if key in self.baseline:
                if current[key] != self.baseline[key]:
                    changes['changed'].append(key)

        return changes

    def alert(self, changes):
        """Send alert if changes detected"""
        if any(changes.values()):
            print(f'\n🚨 ALERT: Changes detected at {datetime.now()}')

            if changes['new']:
                print(f'  New services: {len(changes["new"])}')
                for service in changes['new'][:5]:
                    print(f'    + {service}')

            if changes['removed']:
                print(f'  Removed services: {len(changes["removed"])}')
                for service in changes['removed'][:5]:
                    print(f'    - {service}')

            if changes['changed']:
                print(f'  Changed services: {len(changes["changed"])}')
                for service in changes['changed'][:5]:
                    print(f'    ~ {service}')

            # TODO: Send email/Slack notification
        else:
            print(f'✅ No changes detected at {datetime.now()}')

    def monitor(self, interval=3600):
        """Continuous monitoring loop"""
        print(f'Starting continuous monitoring of {self.org_name}')
        print(f'Scan interval: {interval} seconds')

        # Initial baseline
        self.baseline = self.scan()
        print(f'Baseline established: {len(self.baseline)} services')

        while True:
            try:
                time.sleep(interval)

                # Scan and compare
                current = self.scan()
                changes = self.compare(current)
                self.alert(changes)

                # Update baseline
                self.baseline = current

            except KeyboardInterrupt:
                print('\nMonitoring stopped')
                break
            except Exception as e:
                print(f'Error: {e}')
                time.sleep(60)  # Wait before retry

# Run monitor
if __name__ == '__main__':
    monitor = ShodanMonitor(
        api_key='YOUR_API_KEY',
        org_name='Your Company Name'
    )

    # Monitor every hour
    monitor.monitor(interval=3600)
```

**Usage**:
```bash
# Run in background
nohup python3 continuous_monitor.py > monitor.log 2>&1 &

# Check status
tail -f monitor.log
```

---

## Quick Reference

### Common Query Patterns

```bash
# Search by organization
org:"Company Name"

# Search by IP range
net:203.0.113.0/24

# Search by hostname
hostname:example.com
hostname:*.example.com

# Search by port
port:80
port:80,443,8080

# Search by product
product:apache
product:nginx version:1.14

# Search by country/city
country:US
city:"New York"

# Search by SSL cert
ssl:"company name"

# Combine filters
product:mongodb -authentication country:US

# Boolean operators
("apache" OR "nginx") port:80

# Exclude terms
apache -cloudflare -amazon

# Search by HTTP title
http.title:"Dashboard"

# Search by favicon hash
http.favicon.hash:1307375944

# Search by vulnerability
vuln:CVE-2014-0160

# Time-based
after:01/01/2024
before:31/12/2024
```

### CLI Quick Reference

```bash
# Initialize
shodan init YOUR_API_KEY

# Search
shodan search "query"
shodan search --fields ip_str,port,org "query"
shodan search --limit 100 "query"

# Count results
shodan count "query"

# Host info
shodan host 203.0.113.45

# Statistics
shodan stats "query" --facets country,org,port

# Download
shodan download filename "query"

# Parse downloaded
shodan parse --fields ip_str,port filename.json.gz

# Alerts (requires membership)
shodan alert create "name" 203.0.113.0/24
shodan alert list

# Stream (requires subscription)
shodan stream
shodan stream --ports 22,23,3389
```

### Python Quick Reference

```python
import shodan

# Initialize
api = shodan.Shodan('YOUR_API_KEY')

# Search
results = api.search('query')
results = api.search('query', page=2)

# Count
count = api.count('query')

# Host info
host = api.host('203.0.113.45')
host = api.host('203.0.113.45', history=True)

# With facets
results = api.search('query', facets=[('country', 10)])

# Account info
info = api.info()

# Streaming (requires subscription)
for banner in api.stream.ports([22, 23, 3389]):
    print(banner)
```

---

**Remember**: Always obtain proper authorization before testing systems you don't own!

**Last Updated**: November 2025
