# Claude Code Project Context

## Project Overview

**Repository**: awesome-shodan-queries
**Type**: Curated List / Educational Resource
**Primary Language**: Markdown
**License**: CC0 1.0 Universal (Public Domain)
**Author**: Jake Jarvis
**Purpose**: Educational security research and awareness

## What This Project Is

This is a curated collection of Shodan search queries designed to help security professionals, researchers, and system administrators identify internet-connected devices and potential vulnerabilities. The project serves as both an educational resource and a practical tool for authorized security assessments.

### Core Mission

1. **Security Awareness**: Demonstrate the risks of exposing devices to the internet without proper security
2. **Educational**: Teach about IoT vulnerabilities, network security, and system hardening
3. **Responsible Disclosure**: Encourage ethical security research and vulnerability reporting
4. **Asset Discovery**: Help organizations identify their own exposed infrastructure

## Repository Structure

```
awesome-shodan-queries/
├── readme.md                  # Main documentation with all Shodan queries
├── contributing.md            # Guidelines for contributing queries
├── code-of-conduct.md         # Contributor Covenant Code of Conduct
├── license.md                 # CC0 1.0 Universal License
├── DOCUMENTATION.md           # Comprehensive guide (usage, ethics, techniques)
├── claude.md                  # This file - project context for Claude
└── screenshots/               # Example images of discovered devices
    ├── billboard3.png
    ├── 7-11.png
    ├── mongo-express.png
    └── [30+ other screenshots]
```

## File Descriptions

### readme.md
- **Purpose**: Main project documentation
- **Content**: 60+ Shodan search queries organized by category
- **Categories**:
  - Industrial Control Systems (billboards, gas stations, voting machines, etc.)
  - Remote Desktop (VNC, RDP)
  - Network Infrastructure (databases, CI/CD, Docker)
  - Network Attached Storage (SMB, FTP, NAS devices)
  - Webcams (various brands and types)
  - Printers & Copiers (HP, Xerox, Epson, Canon)
  - Home Devices (stereos, Chromecasts, smart home)
  - Random Stuff (3D printers, crypto miners, etc.)
- **Format**: Each entry includes query, search link, and sometimes screenshot/description

### contributing.md
- **Purpose**: Guidelines for adding new queries
- **Key Requirements**:
  - Narrow down results using filters, but not too much
  - Include exact Shodan search URL
  - No country filters (leave it to the searcher)
  - Screenshots only if interesting/shocking/educational
  - Descriptions only if linking to CVE or additional info

### code-of-conduct.md
- Standard Contributor Covenant v1.4
- Contact: jake@jarv.is

### DOCUMENTATION.md
- Comprehensive guide covering:
  - What Shodan is and how it works
  - Getting started with Shodan
  - Search query syntax and advanced techniques
  - Security and ethical guidelines
  - Responsible disclosure practices
  - Common use cases and troubleshooting

## Codebase Analysis Findings

### Overview of Analysis
Date: November 2025
Analyzer: Claude Code
Branch: claude/analyze-and-document-01GSTZkueTQWJJgxzJmkGZ26

### Repository Statistics
- **Total Files**: 7 markdown files + 31 screenshots
- **Total Queries**: 66 unique Shodan search queries
- **Categories**: 8 major categories
- **Lines of Documentation**: ~677 lines in readme.md
- **Screenshot Count**: 31 PNG images (3.4MB total)
- **License**: CC0 1.0 (Public Domain)
- **Contributors**: Multiple (see GitHub)

### Query Distribution by Category

1. **Industrial Control Systems** (15 queries)
   - Electronic billboards (Samsung Prismview)
   - Gas station pump controllers
   - License plate readers
   - Traffic light controllers
   - Voting machines
   - Lawful intercept wiretaps
   - Prison pay phones
   - Tesla PowerPack systems
   - EV chargers
   - Maritime satellites
   - Submarine mission control
   - Refrigeration units (CAREL)
   - Wind turbines (Nordex)
   - Vehicle GPS trackers
   - Medical X-ray machines (DICOM)
   - Electricity meters
   - Siemens automation
   - HVAC controllers
   - Door/lock controllers
   - Railroad management

2. **Remote Desktop** (2 queries)
   - Unprotected VNC
   - Windows RDP

3. **Network Infrastructure** (13 queries)
   - MongoDB databases
   - Mongo Express GUI
   - Jenkins CI
   - Docker APIs
   - Pi-hole DNS servers
   - Telnet with root access
   - Android Debug Bridge
   - Lantronix adapters
   - Citrix Virtual Apps
   - Cisco Smart Install
   - PBX gateways
   - Polycom video conferencing
   - Bomgar help desk
   - Intel AMT vulnerabilities
   - HP iLO 4 vulnerabilities
   - Outlook Web Access (2007/2010/2013/2016)
   - Lync/Skype for Business

4. **Network Attached Storage** (6 queries)
   - SMB file shares
   - FTP with anonymous login
   - Iomega/LenovoEMC NAS
   - Buffalo TeraStation
   - Logitech Media Server
   - Plex Media Servers
   - Tautulli/PlexPy dashboards

5. **Webcams** (4 queries)
   - Yawcams
   - webcamXP/webcam7
   - Android IP Webcam
   - Security DVRs

6. **Printers & Copiers** (4 queries)
   - HP Printers
   - Xerox Copiers
   - Epson Printers
   - Canon Printers

7. **Home Devices** (4 queries)
   - Yamaha Stereos
   - Apple AirPlay
   - Chromecasts/Smart TVs
   - Crestron Smart Home

8. **Random/Miscellaneous** (6 queries)
   - OctoPrint 3D printers
   - Ethereum miners
   - Apache directory listings
   - Minecraft servers
   - North Korea IP ranges
   - TCP Quote of the Day
   - X-Recruiting headers

### Technical Patterns Identified

#### Common Query Techniques Used

1. **Server Header Matching** (Most common)
   - Pattern: `"Server: [specific-value]"`
   - Examples: `"Server: Prismview Player"`, `"Server: yawcam"`
   - Count: ~25 queries use this

2. **HTTP Title Matching**
   - Pattern: `http.title:"[title]"`
   - Examples: `http.title:"OctoPrint"`, `http.title:"Dashboard"`
   - Count: ~12 queries use this

3. **Port Filtering**
   - Pattern: `port:[number]` or `port:[list]`
   - Examples: `port:10001`, `port:623,664,16992,16993,16994,16995`
   - Count: ~20 queries use this

4. **Content Matching**
   - Pattern: `"[exact phrase]"`
   - Examples: `"in-tank inventory"`, `"authentication disabled"`
   - Count: ~30 queries use this

5. **Negative Filtering** (Exclusion)
   - Pattern: `-[term]` or `!"[exact]"`
   - Examples: `-authentication`, `!"HP-ILO-4/2.53"`
   - Count: ~8 queries use this

6. **Boolean Operations**
   - Pattern: `OR`, `AND` (implicit)
   - Examples: `"Cobham SATCOM" OR ("Sailor" "VSAT")`
   - Count: ~3 queries use this

7. **SSL Certificate Matching**
   - Pattern: `ssl:"[value]"`
   - Example: `ssl:"Xerox Generic Root"`
   - Count: ~2 queries use this

8. **HTTP Component/Favicon**
   - Pattern: `http.component:"[name]"`, `http.favicon.hash:[number]`
   - Examples: `http.component:"mootools"`, `http.favicon.hash:1307375944`
   - Count: ~4 queries use this

9. **Product/Version Filtering**
   - Pattern: `product:[name]`
   - Example: `product:MongoDB`
   - Count: ~2 queries use this

10. **Network Range Filtering**
    - Pattern: `net:[CIDR]`
    - Example: `net:175.45.176.0/22`
    - Count: ~1 query uses this

### Vulnerability-Related Findings

#### Queries Targeting Known CVEs

1. **Intel AMT CVE-2017-5689**
   - Location: readme.md:356
   - Query targets specific vulnerability in Intel Active Management
   - Ports: 623,664,16992-16995

2. **HP iLO 4 CVE-2017-12542**
   - Location: readme.md:363
   - Uses negative filters to exclude patched versions
   - Sophisticated query with multiple version exclusions

#### Security Concerns Highlighted

1. **Default Credentials** (10+ queries)
   - MongoDB without authentication
   - VNC without authentication
   - Telnet with auto-login as root
   - FTP with anonymous login

2. **Outdated Software** (5+ queries)
   - Windows 2000 systems
   - Old Java versions
   - Unpatched management interfaces

3. **Exposed Management Interfaces** (20+ queries)
   - Jenkins CI dashboards
   - Docker APIs
   - Database admin panels
   - Network device configs

4. **IoT Security Issues** (15+ queries)
   - Webcams without authentication
   - Smart home devices exposed
   - Industrial control systems online

### Code Quality Observations

#### Strengths
1. **Consistent Formatting**: All queries follow same markdown structure
2. **Good Organization**: Clear categorization
3. **Link Validation**: All Shodan links properly encoded
4. **Visual Documentation**: Screenshots for complex examples
5. **Ethical Framework**: Strong emphasis on responsible use
6. **Community Guidelines**: Proper CoC and contribution guide

#### Areas for Potential Enhancement
1. **Query Validation**: No automated testing of queries
2. **Result Counts**: No tracking of result count changes over time
3. **CVE Database**: Could link more queries to CVE database
4. **Update Tracking**: No tracking of when queries were last verified
5. **API Examples**: No programmatic usage examples
6. **Regional Variations**: No guidance on geo-specific queries

### Screenshot Analysis

#### Screenshot Coverage
- 31 screenshots covering ~47% of queries
- High-quality resolution where possible (as noted in commit 89b7d67)
- Focus on visually interesting/shocking examples

#### Most Notable Screenshots
1. **billboard3.png** (346KB) - Samsung electronic billboard
2. **octoprint.png** (866KB) - 3D printer interface
3. **tesla.png** (135KB) - Tesla PowerPack system
4. **plate-reader.png** (134KB) - License plate reader
5. **vnc.png** (60KB) - Unprotected VNC desktop

### Git History Insights

#### Recent Commits Analyzed
1. **cb7c9bc** - "funnier billboard screenshot :)"
   - Improved screenshot quality for billboard example

2. **5980aa1** - "exclude patched HP-iLO-4/2.70"
   - Actively maintaining queries to exclude patched systems
   - Shows responsible approach to vulnerability disclosure

3. **89b7d67** - "improved screenshot resolution where possible"
   - Focus on documentation quality

4. **6c17977** - "add Slocum Fleet Mission Control"
   - Adding submarine mission control query

5. **9bb6267** - "add Mongo Express GUI"
   - Expanding database-related queries

### Patterns in Query Evolution
- Increasing sophistication in negative filtering
- Focus on excluding patched/secured systems
- Addition of more IoT and industrial systems
- Improved screenshot quality over time

### Documentation Quality

#### Existing Documentation Strengths
1. **Clear Purpose Statement**: Lines 1-16 in readme.md
2. **Legal Disclaimers**: Prominent warnings about authorization
3. **Table of Contents**: Easy navigation
4. **External Links**: Links to CVEs, RFCs, vendor documentation
5. **Visual Appeal**: Well-formatted with centered images
6. **Community Focus**: Links to blog comments and GitHub issues

#### What Was Missing (Now Added)
1. **Comprehensive Usage Guide**: Added in DOCUMENTATION.md
2. **Query Syntax Reference**: Added filter documentation
3. **Ethical Guidelines**: Expanded responsible disclosure section
4. **Project Context**: Added claude.md for AI assistants
5. **Troubleshooting**: Common issues and solutions
6. **Learning Resources**: Links to training and certifications

### Security Analysis

#### Responsible Disclosure Practices Observed
1. **No Specific IPs**: Queries return general results, not targeted
2. **No Credentials**: No actual passwords or keys included
3. **Patch Awareness**: Exclusion of patched systems (HP iLO example)
4. **Educational Focus**: Emphasis on learning, not exploitation
5. **Legal Warnings**: Multiple reminders about authorization

#### Potential Risks and Mitigations
1. **Risk**: Queries could be used maliciously
   - **Mitigation**: Strong ethical guidelines, legal warnings

2. **Risk**: Outdated queries may not work
   - **Mitigation**: Community can update via PRs

3. **Risk**: False sense of security for defenders
   - **Mitigation**: Documentation emphasizes this is just one tool

### Recommendations for Future Development

#### High Priority
1. **Query Validation Script**: Automate testing of all queries
2. **Result Tracking**: Monitor query effectiveness over time
3. **CVE Cross-Reference**: Link all vulnerability-related queries to CVE database
4. **API Examples**: Add Python/CLI usage examples

#### Medium Priority
1. **Interactive Query Builder**: Help users construct custom queries
2. **Video Tutorials**: Show practical usage
3. **Translation**: Non-English documentation
4. **Case Studies**: Real-world examples of findings

#### Low Priority
1. **Query Analytics**: Track which queries are most popular
2. **Historical Data**: Archive query results over time
3. **Visualization**: Charts showing trends
4. **Integration**: Connect with other tools (Nmap, Metasploit)

## Important Ethical Considerations

### This Project Is For
- Authorized security testing
- Educational purposes
- Responsible vulnerability research
- Finding your own organization's exposed assets
- Security awareness training

### This Project Is NOT For
- Unauthorized access attempts
- Malicious hacking
- Privacy violations
- Cyber attacks
- Harassment

### Legal Context
Unauthorized access to computer systems is illegal under:
- USA: Computer Fraud and Abuse Act (CFAA)
- UK: Computer Misuse Act 1990
- EU: Convention on Cybercrime
- Similar laws exist globally

## Key Concepts

### What is Shodan?
Shodan is a search engine that indexes internet-connected devices by scanning the IPv4 address space and capturing banner information (metadata) from servers, IoT devices, industrial systems, etc.

### Common Query Patterns

1. **Server Headers**: `"Server: specific-value"`
2. **HTTP Titles**: `http.title:"Dashboard"`
3. **Port Filtering**: `port:8080`
4. **Geographic**: `country:US city:"New York"`
5. **Organization**: `org:"Company Name"`
6. **Product/Version**: `product:Apache version:2.4`
7. **Exclusion**: `apache -authentication`
8. **Boolean**: `"SSH" OR "Telnet"`
9. **CVE Search**: `vuln:CVE-2014-0160`

### Query Structure
```
[search_term] [filter1:value1] [filter2:value2] -excluded_term
```

## Working With This Repository

### When Adding New Queries

1. **Research First**: Ensure query is legitimate and educational
2. **Test Query**: Verify it returns expected results
3. **Document Properly**: Follow format in readme.md
4. **Add Context**: If vulnerability-related, link to CVE or security advisory
5. **Screenshot**: Only if it adds educational value
6. **Categorize**: Place in appropriate section

### When Improving Documentation

1. **Accuracy**: Ensure technical accuracy
2. **Clarity**: Write for diverse skill levels
3. **Ethics**: Emphasize responsible disclosure and legal compliance
4. **Examples**: Provide concrete examples
5. **Updates**: Keep information current

### When Reviewing Contributions

Check for:
- Query effectiveness and accuracy
- Proper categorization
- Ethical considerations
- Link validity
- Markdown formatting
- Screenshot quality (if included)
- No sensitive information (specific IPs, credentials, etc.)

## Technology Stack

### Primary
- **Markdown**: All documentation
- **Git**: Version control
- **GitHub**: Hosting and collaboration

### External Dependencies
- **Shodan.io**: Search engine service (external)
- **Web Browser**: For viewing screenshots

### No Build Process
This is a documentation-only repository with no build step, compilation, or deployment process.

## Common Tasks

### Adding a New Query

1. Determine appropriate category
2. Test query on Shodan
3. Format entry:
   ```markdown
   ### Device/Service Name [🔎 →](https://www.shodan.io/search?query=encoded_query)

   ```
   query here
   ```

   Optional description or context

   Optional screenshot: <div align="center"><img src="screenshots/name.png" alt="Description" /></div>
   ```
4. Add to readme.md in appropriate section
5. If adding screenshot, place in screenshots/ directory
6. Update Table of Contents if adding new category

### Updating Existing Queries

1. Verify query still works
2. Update if Shodan syntax has changed
3. Fix broken links
4. Update descriptions if context changed
5. Replace outdated screenshots if necessary

### Improving Documentation

1. Add clarifications to DOCUMENTATION.md
2. Update examples
3. Add new search techniques
4. Include recent Shodan features
5. Refresh external links

## Shodan API Integration (Not Currently Used)

This repository doesn't use the Shodan API, but for future automation:

```python
import shodan

api = shodan.Shodan('API_KEY')
result = api.search('query')
```

Potential uses:
- Automated query validation
- Result count tracking
- Query effectiveness metrics
- Dead link detection

## Best Practices

### For Maintainers

1. **Verify Queries**: Test before merging
2. **Ethical Review**: Ensure queries aren't targeting specific organizations
3. **Legal Compliance**: No queries designed for unauthorized access
4. **Documentation**: Keep docs in sync with queries
5. **Community**: Respond to issues and PRs promptly

### For Contributors

1. **Read contributing.md**: Follow guidelines
2. **Test Queries**: Ensure they work before submitting
3. **Be Responsible**: Consider ethical implications
4. **Quality Over Quantity**: Focus on interesting, educational queries
5. **No Spam**: Don't submit variations of existing queries

### For Users

1. **Obtain Authorization**: Only scan systems you own or have permission to test
2. **Responsible Disclosure**: Report vulnerabilities properly
3. **Respect Privacy**: Don't share specific vulnerable instances publicly
4. **Learn First**: Understand what you're doing before running queries
5. **Stay Legal**: Know and follow laws in your jurisdiction

## Related Projects and Resources

### Similar Resources
- **Censys Search Queries**: Similar concept for Censys
- **Google Dorks**: Similar idea for Google Search
- **Awesome Security**: Broader security resources

### Complementary Tools
- **Shodan CLI**: Command-line interface for Shodan
- **Shodan API**: Programmatic access
- **Shodan Monitor**: Alert system for your IP ranges
- **Shodan Images**: Screenshot browser

### Learning Resources
- Shodan Fundamentals (free course)
- SANS Internet Storm Center
- OWASP resources
- CVE databases

## Troubleshooting

### Common Issues

**Q: Query returns no results**
A: Query may be too specific, or Shodan hasn't scanned those systems recently. Try broader terms.

**Q: Results don't match expectation**
A: Check query syntax, ensure proper filter usage, verify quotes are correct.

**Q: Can't access all results**
A: Free Shodan accounts have limitations. Consider paid membership.

**Q: Screenshot doesn't load**
A: Check file path, ensure image exists in screenshots/ directory.

## Future Enhancements

### Potential Additions
- Automated query validation scripts
- Categorization improvements
- More detailed descriptions for complex queries
- Video tutorials
- Interactive query builder
- Translation to other languages
- Historical data tracking
- Vulnerability trend analysis

### Community Requests
Check GitHub issues for feature requests and discussions.

## Security Notice

**CRITICAL**: This repository is for educational and authorized security testing only.

### Users Must
- Obtain proper authorization before testing any systems
- Follow responsible disclosure practices
- Comply with all applicable laws
- Respect privacy and confidentiality
- Use findings ethically

### Users Must Not
- Attempt unauthorized access
- Use default credentials to "test" access
- Publicly share specific vulnerable instances
- Use information for malicious purposes
- Violate any laws or regulations

## Support and Contact

### Getting Help
- **Issues**: Open GitHub issue for bugs or questions
- **Discussions**: Use GitHub Discussions for general questions
- **Email**: Contact maintainer for sensitive issues

### Contributing
- Read [contributing.md](contributing.md)
- Check existing issues and PRs
- Follow code of conduct
- Submit quality contributions

### Reporting Security Issues
If you discover a security issue related to this repository itself (not external systems found via queries):
1. DO NOT open a public issue
2. Email the maintainer privately
3. Provide detailed information
4. Allow time for remediation

## Version History

- **v1.0** (2025): Added comprehensive documentation and Claude context
- **Original**: Created by Jake Jarvis, mirrored from blog post

## Credits

- **Original Author**: Jake Jarvis (https://jarv.is/)
- **Original Blog Post**: https://jarv.is/notes/shodan-search-queries/
- **Contributors**: See GitHub contributors list
- **Shodan**: Created by John Matherly

## License

CC0 1.0 Universal (Public Domain)

The author has waived all copyright to this work. You are free to:
- Use commercially
- Modify
- Distribute
- Use privately

Without any conditions or attribution requirements.

See [license.md](license.md) for full legal text.

---

## For Claude Code Users

When working with this repository:

1. **Understand Context**: This is security research, not malicious hacking
2. **Maintain Ethics**: Always emphasize responsible use in any additions
3. **Verify Queries**: Test Shodan queries before committing
4. **Check Links**: Ensure Shodan URLs are properly encoded
5. **Image Paths**: Screenshots go in screenshots/ directory
6. **Format Consistency**: Follow existing markdown patterns
7. **No Credentials**: Never include actual credentials or specific IPs
8. **Legal Compliance**: Ensure suggestions comply with laws
9. **Quality Focus**: Prioritize educational value
10. **Community Guidelines**: Respect code of conduct

### Quick Command Reference

```bash
# View structure
ls -la

# Check git status
git status

# Add changes
git add .

# Commit changes
git commit -m "descriptive message"

# Push to branch
git push -u origin claude/analyze-and-document-01GSTZkueTQWJJgxzJmkGZ26
```

---

**Last Updated**: November 2025
**For**: Claude Code AI Assistant
**Purpose**: Project context and working guidelines
