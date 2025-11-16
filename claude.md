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
