PenTest Labs

These labs are from the book "101 Labs - CompTIA PenTest+ from 101Labs.net"

Notes from hands-on labs I'm doing while learning security tooling. All testing is done on my own machine, my own site, or practice sites built for this purpose.

Lab 1: Port scanning with nmap
Scanned the lab book's example IP address and got empty/filtered results, because that address didn't exist on my network (mine was on a different subnet)
Learned to check my own VM's address before scanning
Scanned my own web server and saw its port as open

Takeaway: nmap finds which doors (ports) are open on a computer, and a scan is only useful if the target address is real.

Lab 2: Content discovery with ffuf
Built my own practice target on Kali: a folder with admin/ and backup/ directories and a secret.txt file, served with Python's built-in web server
Ran ffuf with the SecLists common.txt wordlist plus the .txt extension: 9,500 requests in about 3 seconds (roughly 2,600 requests per second)
ffuf found all three items: admin (301), backup (301), and secret.txt (200)
Troubleshooting: my first runs showed 100% errors because I had stopped the web server before running ffuf, so nothing was listening. The fix was running the server in one terminal tab and ffuf in a second tab
Also tried the lab book's example IP address, which doesn't exist on my network, and saw slow timeouts and no results

Takeaway: ffuf can't see inside a site, so it guesses names from a wordlist and reports the ones that exist. It only finds names that are in its list, so a guessable path like /admin isn't real security. Defenders should keep backups and configs out of the web root, protect admin panels, and watch logs for bursts of requests.

Lab 3: Burp Suite proxy and site map
Installed Burp Suite Community Edition on Kali Linux (arm64, UTM VM)
Troubleshooting: a case-sensitivity typo in the command (Burpsuite vs burpsuite), then a "No space left on device" error caused by the host Mac's disk filling up; freed space and reinstalled
Pointed Firefox at Burp's proxy (127.0.0.1:8080)
Hit certificate warnings on HTTPS sites and learned that HSTS blocks exceptions on some of them
Browsed apple.com through Burp, built a site map, and read a raw request and response (301 redirect to www.apple.com)

Takeaway: Burp shows the messages a browser normally hides, which is the foundation of web testing.

Lab 4: Web crawling and scraping with Photon
Installed Photon from GitHub on Kali and resolved Kali's "externally-managed-environment" pip error
Crawled a practice site (books.toscrape.com) at depth 2: 75 requests and 3,447 internal links saved to internal.txt, in 48 seconds
Read the output and spotted site structure (category pages, book pages, relative links)

Takeaway: Crawling maps a site and scraping extracts data from it, and both are early steps in recon.

Lab 13: Manual inspection of web links (OWASP ZAP + DVWA)
Installed DVWA on Kali using sudo apt install dvwa and started it with dvwa-start
Started OWASP ZAP using zaproxy (lowercase, because Linux is case-sensitive)
Ran an Automated Scan against my own DVWA
Troubleshooting: scanning /vulnerabilities returned a 403 because the server wouldn't list that folder; scanning the root URL (http://127.0.0.1:42001/) worked
ZAP reported 9 alerts: 0 High, 2 Medium, 4 Low, and 3 Informational
Findings included a missing CSP header, missing anti-clickjacking header, a cookie without SameSite, and server version information being exposed
Results differed from the book's (no Directory Browsing alert) because of different server configuration and because the scan wasn't logged in

Takeaway: An automated scanner identifies potential weaknesses in web applications, especially headers and cookies. Results depend on server configuration and what the scanner can access.

Lab 14: Load balancing detection (LBD)
Used LBD (Load Balancing Detector) on Kali to check whether websites use load balancing
Ran lbd hackthissite.org and lbd google.com
Both websites returned multiple IP addresses, so LBD detected DNS-based load balancing
For hackthissite.org, HTTP Server and HTTP Diff checks were not found; the Date check was skipped because no usable Date header was found
For google.com, the HTTP Server check was not found, the Date check was not found, and the HTTP Diff check detected differences in the responses
Google's HTTP responses contained different Content Security Policy nonces, which contributed to the HTTP Diff result

Takeaway: LBD helps identify signs of load balancing by checking DNS records and HTTP responses. This is useful for understanding a website's infrastructure during reconnaissance, but finding load balancing is not finding a vulnerability. Multiple IP addresses alone don't prove there are multiple physical servers.

Lab 15: Web Application Firewall (WAF) detection (Nmap + Wafw00f)
Used Nmap's http-waf-detect NSE script to investigate whether a website uses a Web Application Firewall (WAF)
Ran nmap -Pn --script http-waf-detect -T4 --reason equifaxsecurity2017.com
Learned that -Pn skips host discovery and tells Nmap to assume the target is online
Nmap reported 13 open TCP ports and 987 filtered ports, but did not provide a confirmed WAF detection result
Learned that Nmap is a general-purpose network scanner and its WAF detection script may not always provide a useful result
Tried Wafw00f, a tool specifically designed for WAF detection, using wafw00f -v -a equifaxsecurity2017.com
Wafw00f detected signs of a WAF or security solution, with possible matches including BIG-IP AppSec Manager (F5 Networks), Envoy, and Google Cloud Armor
The detection was based partly on different HTTP response codes: a normal request returned 200, while a suspicious XSS test request returned 502
Learned that Wafw00f could identify possible WAF products but couldn't confidently determine which specific product was in use

Takeaway: A WAF acts as a protective layer between visitors and a web application, filtering potentially malicious HTTP requests. Nmap offers basic WAF detection, while Wafw00f specializes in identifying WAFs by analyzing server responses. Detection results are indicators, not absolute proof of the exact security product.

Tools
Kali Linux – Linux distribution designed for penetration testing and cybersecurity.
nmap – Scans networks and identifies open ports, services, and devices.
ffuf – Discovers hidden directories, files, and web endpoints using wordlists.
SecLists – Collection of wordlists used for penetration testing and content discovery.
Burp Suite – Intercepts and inspects HTTP requests and responses for web security testing.
Photon – Web crawler that discovers links, URLs, and other website information.
Python – Programming language used for scripting, automation, and building tools.
OWASP ZAP – Web application security scanner used to identify potential vulnerabilities.
DVWA – Intentionally vulnerable web application for practicing web security.
LBD – Load Balancing Detector; checks websites for signs of load balancing.
Wafw00f – Specialized tool for detecting Web Application Firewalls.
