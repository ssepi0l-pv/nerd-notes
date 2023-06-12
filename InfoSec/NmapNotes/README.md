# Nmap notes!

Official docs: https://nmap.org/book/man-port-scanning-techniques.html

| Options | Name             | Description                                                                                                                                                     |
|---------|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -sS     | TCP SYN Scan     | Fast and stealthy scan. Only sends one TCP SYN package and does not answer back.                                                                                |
| -sT     | TCP Connect Scan | Slow, although stealthier than SYN scans, since it establishes a three-way-handshake. Less likely to be detected by an IDS or IPS.                              |
| -sU     | UDP Scan         | Slower scan, although it could give good results, since people more often than not forget that UDP ports can be scanned, they forget about them. Great mistake! |

In a SYN scan, a port will be considered to be open if Nmap receives a SYN-ACK answer. If it receives a RST call, the port
may be closed. If no answer is received, the port will be considered to be filtering.

In a Connect scan, Nmap uses the high-level syscall connect()

In a UDP scan, if a machine answers with a ICMP 3 code, the port is considered to be closed. If it responds with any other
ICMP code, it will be considered to be filtered. If and only if Nmap receives a UDP answer, the port will be considered open.


