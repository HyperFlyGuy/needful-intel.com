---
title: VSHELL Infrastructure Hunt
tags:
  - hunting
  - infrastructure
  - c2
  - go
---

## Overview

Vshell is a Go based remote administration tool that provides post-compromise capabilities. Though it is not marketed as a penetration testing tool or C2 framework, its capabilities have caused it to be used in that capacity. In the industry we have seen this tool being adopted by mainly PRC affiliated threat actors. This hunt is being created to map common characteristics observed in VSHELL deployments over the years due to its recent resurgence since 2025 (see references).

## Technical Details

**I was unable to get a hold of a VSHELL code so the pivots and analysis that I do will be based on analysis done by others (see references)**

The purpose of this section is to aggregate some potential hunting characteristics that we can use to make infrastructure searches. Once we have some good potetntial pivots we will develop a hunt.

### Default  Listener Values:

|                   |                      |                            |
| ----------------- | -------------------- | -------------------------- |
| **Listener Type** | **Default address**  | **Description**            |
| TCP               | 0.0.0.0:8084         | Communication over UDP     |
| KCP/UDP           | 0.0.0.0:8084         | Communication over TCP     |
| WebSocket         | ws://0.0.0.0:8084/ws | C2 using HTTP Websockets   |
| DNS               | 0.0.0.0:53           | C2 over DNS                |
| DOH               | 0.0.0.0:53           | DNS-over-HTTPS             |
| DOT               | 0.0.0.0:53           | DNS-over-TLS               |
| OSS               | S3 bucket URL        | Object Storage System (S3) |

### Default Certificate Values
|           |                                                                  |                 |
| --------- | ---------------------------------------------------------------- | --------------- |
| **Field** | **Value**                                                        | **Full**        |
| C         | CN                                                               | C=CN            |
| ST        | Shanghai                                                         | ST=Shanghai     |
| L         | Shanghai                                                         | L=Shanghai      |
| CN        | 127.0.0.1                                                        | CN=127.0.0.1    |
| O         | Example Inc.                                                     | O=Example Inc.  |
| OU        | Web Security                                                     | OU=Web Security |
| SHA256    | AD1A0B3E22A10A2BD680B773B178A0D3824CFCBDF3551016F3D052A0B823079F |                 |

### Example of Client HTTP Headers:
```
GET /?a={l64}&h={host}&t={ws_}&p={port} HTTP/1.1\r\n
Host: {host}:{port}\r\n
User-Agent: Mozilla/5.0 (Windows NT 6.1; rv:48.0) Gecko/20100101 Firefox/48.0\r\n
\r\n
```

Where the variables are below.

|              |                                                                               |                            |
| ------------ | ----------------------------------------------------------------------------- | -------------------------- |
| **Variable** | **Value**                                                                     | **Description**            |
| a            | I64                                                                           | Client Type                |
| h            | C2 hostname                                                                   | host IP Address            |
| t            | ws_                                                                           | Protocol being used        |
| p            | portnumber                                                                    | port dependent on protocol |
| User Agent   | User-Agent: Mozilla/5.0 (Windows NT 6.1; rv:48.0) Gecko/20100101 Firefox/48.0 | User agent valyue          |


## References 
- https://censys.com/blog/vshell/
- https://github.com/Esonhugh/How-AI-Kills-the-VShell/blob/Skyworship/Killing_that_VShell.md
- https://www.trellix.com/blogs/research/the-silent-fileless-threat-of-vshell/
- https://www.seqrite.com/blog/operation-dragonclone-chinese-telecom-veletrix-vshell-malware/
- https://www.sysdig.com/blog/unc5174-chinese-threat-actor-vshell
- https://hunt.io/blog/chinese-operators-claude-deepseek-government-intrusion