# Bypass Url Parser

[![PyPI - Version](https://img.shields.io/pypi/v/bypass-url-parser)](https://pypi.org/project/bypass-url-parser/) [![PyPI - Python Version](https://img.shields.io/pypi/pyversions/bypass-url-parser)](https://pypi.org/project/bypass-url-parser/) [![PyPI - License](https://img.shields.io/pypi/l/bypass-url-parser)](https://github.com/laluka/bypass-url-parser/blob/main/LICENSE) [![pdm-managed](https://img.shields.io/badge/pdm-managed-blueviolet)](https://pdm.fming.dev)

Tool that tests `MANY` url bypasses to reach a `40X protected page`.

If you wonder why this code is `nothing but a dirty curl wrapper`, here's why:

- Most of the python requests do url/path/parameter encoding/decoding, and I hate this.
- If I submit raw chars, I want raw chars to be sent.
- If I send a weird path, I want it weird, not normalized.

This is `surprisingly hard` to achieve in python without loosing all of the lib goodies like parsing, ssl/tls encapsulation and so on. \
So, be like me, use `curl as a backend`, it's gonna be just fine.

Also, this tool can be used as a library, see [lib_sample_usage.py](lib_sample_usage.py)

## Installation

We recommend using `pipx` to install this tool:

```bash
pipx install bypass-url-parser
# or for the latest dev version
pipx install git+https://github.com/laluka/bypass-url-parser
```

Alternatively, you can use `pip`:

```bash
pip install bypass-url-parser
```

## Usage

```
Bypass Url Parser, made with love by @TheLaluka
A tool that tests MANY url bypasses to reach a 40X protected page.

Usage:
    bypass-url-parser (-u <URL> | -R <file>) [-m <mode>] [-o <outdir>] [-S <level>] [ (-H <header>)...] [-r <num>]
                           [-s <ip>] [--spoofip-replace] [-p <port>] [--spoofport-replace] [--request-tls]
                           [--dump-payloads] [-t <threads>] [-T <timeout>] [-x <proxy_url>] [-v | -d | -dd]

Program options:
    -u, --url <URL>           URL (path is optional) to run bypasses against
    -R, --request <file>      Load HTTP raw request from a file
    -H, --header <header>     Header(s) to use, format: "Cookie: can_i_haz=fire"
    -m, --mode <mode>         Bypass modes. See 'Bypasser.BYPASS_MODES' in code [Default: all]
    -o, --outdir <outdir>     Output directory for results
    -x, --proxy <proxy_url>   Set a proxy in the format http://proxy_ip:port.
    -S, --save-level <level>  Save results level. From 0 (DISABLE) to 3 (FULL) [Default: 1]
    -s, --spoofip <ip>        IP(s) to inject in ip-specific headers
    -p, --spoofport <port>    Port(s) to inject in port-specific headers
    -r, --retry <num>         Retry attempts of failed requests. Set 0 to disable all retry tentatives [Default: 3]
    -t, --threads <threads>   Scan with N parallel threads [Default: 1]
    -T, --timeout <timeout>   Request times out after N seconds [Default: 5]

General options:
    -h, --help                Show help, you are here :)
    -v, --verbose             Verbose output
    -d, --debug               Show more details like curl commands generated by this tool
    -dd, --debug              Print Debug level 2 (with all classes debug_class output)
    -V, --version             Show version info

Misc options:
    --spoofip-replace         Disable list of default internal IPs in 'http_headers_ip' bypass mode
    --spoofport-replace       Disable list of default internal ports in 'http_headers_port' bypass mode
    --request-tls             Force usage of TLS/HTTPS for the request load with the '-R, --request' option
    --dump-payloads           Print all payloads (curls) generated by this tool.

Examples:
    bypass-url-parser -u "http://127.0.0.1/juicy_403_endpoint/" -s 8.8.8.8 -d
    bypass-url-parser -u /path/urls -t 30 -T 5 -H "Cookie: me_iz=admin" -H "User-agent: test"
    bypass-url-parser -R /path/request_file --request-tls -m "mid_paths, end_paths"
```

## Expected result

```bash
bypass-url-parser -u http://127.0.0.1:8000/foo/bar
2022-08-09 14:52:40 lalu-perso bup[361559] WARNING Trying to bypass 'http://127.0.0.1:8000/foo/bar' url (3213 payloads)...
2022-08-09 14:52:40 lalu-perso bup[361559] INFO Doing: 50 / 3213
[...]
2022-08-09 14:52:54 lalu-perso bup[361559] INFO Doing: 3200 / 3213
2022-08-09 14:52:54 lalu-perso bup[361559] INFO Retry (1/3) the '16' failed curl commands with 10 threads and 10s timeout
2022-08-09 14:52:54 lalu-perso bup[361559] INFO Retry (2/3) the '16' failed curl commands with 5 threads and 20s timeout
2022-08-09 14:52:54 lalu-perso bup[361559] INFO Retry (3/3) the '16' failed curl commands with 1 threads and 30s timeout
2022-08-09 14:52:55 lalu-perso bup[361559] INFO
[#####] [bypass_method] [payload] => [status_code] [content_type] [content_length] [lines_count] [word_counts] [title] [server] [redirect_url]
[GROUP (1587)] [original_request] [http://127.0.0.1:8000/foo/bar] => [404] [text/html] [469] [14] [95] [Error response] [SimpleHTTP/0.6 Python/3.8.10] []
[GROUP (10)] [http_methods] [-X CONNECT http://127.0.0.1:8000/foo/bar] => [501] [text/html] [500] [14] [96] [Error response] [SimpleHTTP/0.6 Python/3.8.10] []
[SINGLE] [mid_paths] [http://127.0.0.1:8000/???foo/bar] => [200] [text/html] [913] [26] [27] [Directory listing for /???foo/bar] [SimpleHTTP/0.6 Python/3.8.10] []
[SINGLE] [mid_paths] [http://127.0.0.1:8000//???foo/bar] => [301] [] [] [0] [0] [] [SimpleHTTP/0.6 Python/3.8.10] [/???foo/bar]
[SINGLE] [mid_paths] [http://127.0.0.1:8000/??foo/bar] => [200] [text/html] [911] [26] [27] [Directory listing for /??foo/bar] [SimpleHTTP/0.6 Python/3.8.10] []
[SINGLE] [mid_paths] [http://127.0.0.1:8000//??foo/bar] => [301] [] [] [0] [0] [] [SimpleHTTP/0.6 Python/3.8.10] [/??foo/bar]
[SINGLE] [mid_paths] [http://127.0.0.1:8000/?foo/bar] => [200] [text/html] [909] [26] [27] [Directory listing for /?foo/bar] [SimpleHTTP/0.6 Python/3.8.10] []
[SINGLE] [mid_paths] [http://127.0.0.1:8000//?foo/bar] => [301] [] [] [0] [0] [] [SimpleHTTP/0.6 Python/3.8.10] [/?foo/bar]
[SINGLE] [mid_paths] [http://127.0.0.1:8000///?anythingfoo/bar] => [200] [text/html] [929] [26] [27] [Directory listing for ///?anythingfoo/bar] [SimpleHTTP/0.6 Python/3.8.10] []
[SINGLE] [mid_paths] [http://127.0.0.1:8000////?anythingfoo/bar] => [200] [text/html] [931] [26] [27] [Directory listing for ////?anythingfoo/bar] [SimpleHTTP/0.6 Python/3.8.10] []
[GROUP (2)] [mid_paths] [http://127.0.0.1:8000/#?foo/bar] => [200] [text/html] [893] [26] [27] [Directory listing for /] [SimpleHTTP/0.6 Python/3.8.10] []
[GROUP (2)] [mid_paths] [http://127.0.0.1:8000//#?foo/bar] => [301] [] [] [0] [0] [] [SimpleHTTP/0.6 Python/3.8.10] [/]
```

## Setup

### LINUX

```bash
# Deps
sudo apt install -y bat curl virtualenv python3
# Tool
virtualenv -p python3 .py3
source .py3/bin/activate
pip install -r requirements.txt
bypass-url-parser -u http://thinkloveshare.com/juicy_403_endpoint/
cat /tmp/tmpRANDOM-bypass-url-parser/triaged-bypass.json  | jq -r '.results[].request_curl_cmd'
cat /tmp/tmpRANDOM-bypass-url-parser/triaged-bypass.json  | jq -r '.results[].response_data'
```

### DOCKER

```bash
docker build -t thelaluka/bypass-url-parser:latest .
docker run --rm -it --net=host -v "$PWD:/host" thelaluka/bypass-url-parser:latest -u http://127.0.0.1:8000/dummy
docker run --rm -it --net=host -v "$PWD:/host" thelaluka/bypass-url-parser:latest -u /host/urls.lst
# TODO later, tag & push releases to dockerhub :)
# -t thelaluka/bypass-url-parser:0.2.0
# docker push thelaluka/bypass-url-parser:latest
```

## More about supported arguments

### Arguments parsing

Bypass_url_parser allows to define some arguments in many ways:

- `-m, --mode`, `-s, --spoofip` and `-p, --spoofport` arguments can be a filename, a string, a comma-separated string list or a list (when `Bypasser` is used as a library);
- `-u, --url` argument can be a filename, a string or a list (when `Bypasser` is used as a library);
- `stdin` (with `-`) is supported for all these arguments.

For example, if you want to define several target urls (`-u, --url`), all the following commands produce the same result:

```bash
bypass-url-parser -u http://thinkloveshare.com/test
bypass-url-parser -u /path/urls
cat /path/urls | bypass-url-parser -u -
echo 'http://thinkloveshare.com/test' | bypass-url-parser -u -
```

### Target definition

A target must be defined for the tool to work. 2 options:

- `-u, --url`: URL(s), in GET
- `-R, --request`: Request file. The protocol can't be guessed from file, so `http` by default or `https` if `--request-tls` option is present.

### Bypass mode

If `-m, --mode` is specified, you can select the desired bypass mode to run a specific test (or tests) and reduce the number of requests sent by the tool.

For now, the following bypass mode(s) are supported:

```
all, mid_paths, end_paths, case_substitution, char_encode, http_methods, http_versions, http_headers_method, http_headers_scheme, http_headers_ip, http_headers_port, http_headers_url
```

Example:

```bash
bypass-url-parser -u /path/urls -m "case_substitution, char_encode, http_headers_scheme"
```

### Spoofip / Spoofport

In order to customize the ip addresses and ports used in bypass attempts, the tool supports the following options:

- With `-s, --spoofip` you can set some IP(s) to inject into `ip-specific` headers (`X-Forwarded-For`, `X-Real-Ip`, etc.)
- With `-p, --spoofport` you can set some ports to inject into `port-specific` headers (`X-Forwarded-Port`)

By default, these custom entries are added to the internal IP/port lists. If you want to use only your IP(s)/port(s), you can use `--spoofip-replace` and/or `--spoofport-replace` arguments.

Example:

```bash
bypass-url-parser -u /path/urls -s /path/custom_ip --spoofip-replace
bypass-url-parser -u /path/urls -p "3000, 9443, 10443"
```

### Results saving

By default, if target url is unique, the tool saves a copy of the results in `/tmp/tmpXXX-bypass-url-parser/` directory.

***Notes:** If multiple target urls are passed to `-u`, results are prefixed with the url as directory (`/tmp/tmpXXX-bypass-url-parser/http-target-com-8080-api-users/`).*

There are two arguments to customize this behavior:

- `-o, --outdir` to set a custom output directory
- `-S, --save-level` to choose a saving level

The saving levels are:

- `0` (NONE): Disable output saving and output directory creation;
- `1` (MINIMAL): Only save the program log file which contains the results: `triaged-bypass.log`;
- `2` (PERTINENT): Save the program log file `triaged-bypass.log` and **pertinent (results)** curl responses in `triaged-bypass.json` file and separate html files (Default);
- `3` (FULL): Save the program log file `triaged-bypass.log` and **all** curl responses in `triaged-bypass.json` file and separate html files.

#### Example

```bash
bypass-url-parser -S 0
bypass-url-parser -S 1 -o /tmp/bypass-res
bypass-url-parser -S 2 -o /tmp/bypass-res2 -H "User-Agent: curl 7.74.0" -u http://thinkloveshare.com/juicy_403_endpoint/
tree /tmp/bypass-res2/
├── bypass-2469eecf6c38b5817d2248e911ad4382.html
├── bypass-6f7cce7caf0a0a4b440859fa189d496d.html
├── bypass-80f4ab5d32b4e74c20630c7e67f2e42f.html
├── bypass-93079abffe63d34f79ac4a511cd6b5e6.html
├── bypass-945822230d58d1ad4680d5dfbc470ecb.html
├── bypass-e6118c315eea0e5b2ebc4fcafe0559c0.html
├── triaged-bypass.json
└── triaged-bypass.log

0 directories, 8 files
```

#### Results export

Starting from `MINIMAL` level, the results displayed by the program are saved in the `triaged-bypass.log` file.

#### JSON export

With `PERTINENT` and `FULL` saving levels, the program additionally exports all results in the `triaged-bypass.json` file:

```json
{
  "url": "http://thinkloveshare.com/juicy_403_endpoint/",
  "bypass_modes": "all",
  "results": [
    {
      "request_curl_cmd": "/usr/bin/curl -sS -kgi -H 'User-Agent: curl 7.74.0' --path-as-is -H 'X-BlueCoat-Via: localhos[...SNIP...]",
      "request_curl_payload": "-H X-BlueCoat-Via: localhost http://thinkloveshare.com/juicy_403_endpoint/",
      "response_headers": "HTTP/1.1 301 Moved Permanently\nConnection: keep-alive\nContent-Length: 162\nServer: GitHub.c[...SNIP...]",
      "response_data": "<html>\n<head><title>301 Moved Permanently</title></head>\n<body>\n<center><h1>301 Moved Permane[...SNIP...]",
      "response_status_code": 301,
      "response_content_type": "text/html",
      "response_content_length": 162,
      "response_lines_count": 7,
      "response_words_count": 4,
      "response_title": "301 Moved Permanently",
      "response_server_type": "GitHub.com",
      "response_redirect_url": "https://thinkloveshare.com/juicy_403_endpoint/",
      "response_html_filename": "bypass-e6118c315eea0e5b2ebc4fcafe0559c0.html"
    },
    {
      "request_curl_cmd": "/usr/bin/curl -sS -kgi -H 'User-Agent: curl 7.74.0' --path-as-is -X PROPFIND http://thinklove[...SNIP...]",
      "request_curl_payload": "-X PROPFIND http://thinkloveshare.com/juicy_403_endpoint/",
      "response_headers": "HTTP/1.1 405 Method Not Allowed\nConnection: close\nContent-Length: 131\nServer: Varnish\nRet[...SNIP...]",
      "response_data": "<html>\n<head><title>405 Not Allowed</title></head>\n<body bgcolor=\"white\">\n<center><h1>405 N[...SNIP...]",
      "response_status_code": 405,
      "response_content_type": "",
      "response_content_length": 131,
      "response_lines_count": 5,
      "response_words_count": 5,
      "response_title": "405 Not Allowed",
      "response_server_type": "Varnish",
      "response_redirect_url": "",
      "response_html_filename": "bypass-945822230d58d1ad4680d5dfbc470ecb.html"
    },
    {  "...": "[...SNIP...]"},
    {
      "request_curl_cmd": "/usr/bin/curl -sS -kgi -H 'User-Agent: curl 7.74.0' --path-as-is 'http://thinkloveshare.com/j[...SNIP...]",
      "request_curl_payload": "http://thinkloveshare.com/juicy_403_endpoint/\u00b0//",
      "response_headers": "HTTP/1.1 400 Bad request\nConnection: keep-alive\nContent-Length: 90\nCache-Control: no-cache[...SNIP...]",
      "response_data": "<html><body><h1>400 Bad request</h1>\nYour browser sent an invalid request.\n</body></html>\n",
      "response_status_code": 400,
      "response_content_type": "text/html",
      "response_content_length": 90,
      "response_lines_count": 3,
      "response_words_count": 7,
      "response_title": "",
      "response_server_type": "",
      "response_redirect_url": "",
      "response_html_filename": "bypass-2469eecf6c38b5817d2248e911ad4382.html"
    }
  ]
}
```

Making them easier to handle with `jq`:

```bash
$ jq -r '.results[] | [.request_curl_payload, .response_status_code, .response_content_type, .response_content_length] | join("|")' /tmp/bypass-res2/triaged-bypass.json

-H X-BlueCoat-Via: localhost http://thinkloveshare.com/juicy_403_endpoint/|301|text/html|162
-X PROPFIND http://thinkloveshare.com/juicy_403_endpoint/|405||131
http://thinkloveshare.com/%3b%2f%2e%2e%2f%2e%2e%2f%2fjuicy_403_endpoint/|400|text/html|9121
-H Host: 8.8.8.8 http://thinkloveshare.com/juicy_403_endpoint/|404|text/html|9115
-X CONNECT http://thinkloveshare.com/juicy_403_endpoint/|400|text/plain|15
http://thinkloveshare.com/juicy_403_endpoint/°//|400|text/html|90
```

#### HTML files

With `PERTINENT` and `FULL` saving levels, curl commands and full HTTP responses are also stored in pseudo `.html` files:

```bash
$ echo /tmp/bypass-res2/*.html | xargs batcat
───────┬───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: /tmp/bypass-res2/bypass-2469eecf6c38b5817d2248e911ad4382.html
───────┼───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ /usr/bin/curl -sS -kgi -H 'User-Agent: curl 7.74.0' --path-as-is 'http://thinkloveshare.com/juicy_403_endpoint/°//'
   2   │
   3   │ HTTP/1.1 400 Bad request
   4   │ Connection: keep-alive
   5   │ Content-Length: 90
   6   │ Cache-Control: no-cache
   7   │ Content-Type: text/html
   8   │ Accept-Ranges: bytes
   9   │ Date: Tue, 25 Apr 2023 23:51:38 GMT
  10   │ Via: 1.1 varnish
  11   │ X-Served-By: cache-par-lfpg1960025-PAR
  12   │ X-Cache: MISS
  13   │ X-Cache-Hits: 0
  14   │ X-Timer: S1682466698.230664,VS0,VE10
  15   │ Vary: Accept-Encoding
  16   │ X-Fastly-Request-ID: b6bbb82302420db4f101a316dca39cc283a4fd44
  17   │
  18   │ <html><body><h1>400 Bad request</h1>
  19   │ Your browser sent an invalid request.
  20   │ </body></html>
───────┴───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
───────┬───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: /tmp/bypass-res2/bypass-6f7cce7caf0a0a4b440859fa189d496d.html
───────┼───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ /usr/bin/curl -sS -kgi -H 'User-Agent: curl 7.74.0' --path-as-is -X CONNECT http://thinkloveshare.com/juicy_403_endpoint/
   2   │
   3   │ HTTP/1.1 400 Bad Request
   4   │ Connection: close
   5   │ Content-Length: 15
   6   │ content-type: text/plain; charset=utf-8
   7   │ x-served-by: cache-par-lfpg1960083
   8   │
   9   │ invalid request
───────┴───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
───────┬───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: /tmp/bypass-res2/bypass-80f4ab5d32b4e74c20630c7e67f2e42f.html
───────┼───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ /usr/bin/curl -sS -kgi -H 'User-Agent: curl 7.74.0' --path-as-is http://thinkloveshare.com/%3b%2f%2e%2e%2f%2e%2e%2f%2fjuicy_403_endpoint/
   2   │
   3   │ HTTP/1.1 400 Bad Request
   4   │ Connection: keep-alive
   5   │ Content-Length: 9121
   6   │ Server: GitHub.com
   7   │ Content-Type: text/html; charset=utf-8
   8   │ ETag: "64417b9f-23a1"
   9   │ Content-Security-Policy: default-src 'none'; style-src 'unsafe-inline'; img-src data:; connect-src 'self'
  10   │ X-GitHub-Request-Id: 598E:F13E:26EE27D:284F5D2:64486731
  11   │ Accept-Ranges: bytes
  12   │ Date: Tue, 25 Apr 2023 23:50:11 GMT
  13   │ Via: 1.1 varnish
  14   │ X-Served-By: cache-par-lfpg1960046-PAR
  15   │ X-Cache: MISS
  16   │ X-Cache-Hits: 0
  17   │ X-Timer: S1682466611.396077,VS0,VE101
  18   │ Vary: Accept-Encoding
  19   │ X-Fastly-Request-ID: 30bd6af5892c40da130ee49bbeacd147a1a6b3c3
  20   │
  21   │ <!DOCTYPE html>
  22   │ <html>
  23   │   <head>
  24   │     <meta http-equiv="Content-type" content="text/html; charset=utf-8">
  25   │     <meta http-equiv="Content-Security-Policy" content="default-src 'none'; style-src 'unsafe-inline'; img-src data:; connect-src 'self'">
  26   │     <title>Bad request &middot; GitHub Pages</title>
  27   │     <style type="text/css" media="screen">
[...SNIP...]
```

## Non-Regression tests & Code Cleanup

```bash
# Code Cleanup
pre-commit run --all-files
# Ensure no regression is pushed
bypass-url-parser -S 0 -v -u http://127.0.0.1:8000/foo/bar --dump-payloads > "tests-history/bup-payloads-$(date +'%Y-%m-%d').lst"
# Compare /tmp/bup-payloads-YYYY-MM-DD.lst and the latest tests-history/bup-payloads-YYYY-MM-DD.lst
git diff --no-index $(find tests-history -type f | sort -n | tail -n 2)
# Push your changes
git add .
git commit -m "My cool feature or bugfix"
git tag -a vX.Y.Z "$COMMIT_HASH" -m "New release: vX.Y.Z"
git push --tags
# If X or Y is bumped, create new release on github
```

## Contributors

- Initial release by [@TheLaluka](https://twitter.com/TheLaluka)
- Huge refactoring & lib-mode with thanks to [@jtop_fap](https://twitter.com/jtop_fap)


## License

Copyright (C) 2022 Laluka

This program is free software: you can redistribute it and/or modify it under the terms of the GNU Affero General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU Affero General Public License for more details.

You should have received a copy of the GNU Affero General Public License along with this program. If not, see <https://www.gnu.org/licenses/>.
