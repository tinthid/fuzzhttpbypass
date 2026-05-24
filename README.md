# FuzzHTTPBypass

This tool uses fuzzing to try to bypass unknown authentication methods, who knows...

This is mainly for CTFs.

## Installation

Requires **wfuzz**, **requests**, **beautifulsoup4**, and **urllib3**:

```bash
pip3 install wfuzz requests beautifulsoup4 urllib3
```

## Usage

```
./fuzzhttpbypass.py (-u <url> | -L <url-list>) -W "<wfuzz-filter>" [-i <ip>] [-b]
```

| Flag | Description |
| --- | --- |
| `-u`, `--url` | Single URL to test (e.g. `http://example.com/index.php`). Mutually exclusive with `-L`. |
| `-L`, `--url-list` | Path to a file containing one URL per line for bulk testing. Mutually exclusive with `-u`. |
| `-W`, `--wfuzz-filter` | **Required.** wfuzz filter options passed through verbatim (e.g. `"--hc 403,404 --hl 100"`). |
| `-i`, `--ip` | Extra IP to use when impersonating via HTTP headers. By default the resolved IP(s) of the target plus `127.0.0.1` and `8.8.4.4` are used. |
| `-b`, `--bypass-verification` | Skip SSL certificate verification (useful against misconfigured targets). |

## Features

- [+] Initial GET request reports **status code**, **response length**, **cookies set by the server**, and **body if the response is a redirect**
- [+] Fuzz **Path** variations using encoded traversal payloads: `%2e`, `%252e`, `%ef%bc%8f`
- [+] Fuzz **HTTP Verbs (Methods)**: *GET, HEAD, POST, DELETE, CONNECT, OPTIONS, TRACE, PUT, INVENTED* — also retried against common index paths (`index.php`, `index`, `index.html`, `index.asp`, `index.aspx`, `/`)
- [+] Fuzz **HTTP Headers**: *Forwarded, X-Originating-IP, X-Forwarded-For, X-Forwarded, Forwarded-For, X-Remote-IP, X-Remote-Addr, X-ProxyUser-Ip, X-Original-URL, Client-IP, True-Client-IP, Cluster-Client-IP, Host, Referer, User-Agent, Cookies*
- [+] Fuzz **HTTP Authentication**: *Basic* and *NTLM* (same-value pairs and user/password combinations)
- [+] **Filter** results with any wfuzz filter syntax via `-W` (`--hc`, `--hl`, `--hw`, `--hh`, `--sc`, `--ss`, `--hs`, etc.)
- [+] **Bulk URL testing** via `-L`
- [+] **Autocontained**

## Examples

Hide responses with status code 403 against a single URL:

```bash
./fuzzhttpbypass.py -u http://example.com/index.php -W "--hc 403"
```

Hide responses with codes 403 or 404 **and** a body length of 100:

```bash
./fuzzhttpbypass.py -u http://example.com/index.php -W "--hc 403,404 --hl 100"
```

Hide responses containing the word "Invalid":

```bash
./fuzzhttpbypass.py -u http://example.com/index.php -W "--hs Invalid"
```

Bulk-test a list of URLs and skip SSL verification:

```bash
./fuzzhttpbypass.py -L urls.txt -W "--hc 403" -b
```

Add a custom IP to the impersonation set:

```bash
./fuzzhttpbypass.py -u http://example.com/index.php -W "--hc 403" -i 10.0.0.5
```
