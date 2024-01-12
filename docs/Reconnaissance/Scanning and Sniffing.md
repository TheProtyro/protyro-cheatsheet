- https://danielmiessler.com/study/tcpdump/

##### Filtering options
```
tcpdump -nnSX port 80 # The only command to remember
tcpdump -i eth0 # listen on your interface
tcpdump host 1.1.1.1 # filter on host
tcpdump src 1.1.1.1 # filter on source
tcpdump dst 1.0.0.1 # filter on destination
tcpdump net 1.2.3.0/24 # filter on ... yes you guess it
tcpdump ip6
tcpdump icmp # filter using protocol
tcpdump portrange 21-23
```

##### Combining

Combine options using and (&&), or (||) and not (!).

```
tcpdump -nnSX src 10.5.2.3 and not dst port 443
```

Complex queries need single quotes

```
tcpdump 'src 10.0.2.4 and (dst port 3389 or 22)'
```

##### Reading and writing to file

```
tcpdump port 80 -w capture.pcap
tcpdump -r capture.pcap
```
Save the file and view the packets
```
tcpdump -w - | tee file.pcap | tcpdump -r - 
``` 

#### Nmap Probing

|Windows and UID 0 User on Linux|non-UID 0 on Linux|
|-|-|
|Same subnet : ARP only|TCP SYN to port 80|
|ICMP Echo Request|TCP SYN to port 443|
|TCP SYN to port 443|No ICMP used|
|TCP ACK to port 80|
|ICMP Timestamp Request|

`-Pn` assume hosts are up

#### Network Sweep
```
nmap -sP 10.10.10.0/24
```
#### Optimizing Host Detection
- `-PSports` use TCP SYN to specified ports
- `-PE` use ICMP Echo Request
```
nmap -PS21,22,23,25,53,80,110,111,135,137,139,143,443,445,502,993,995,1433,1434,1723,3306,3389,5900,8080 -PE -iL hosts.txt
```

#### One host TCP / UDP
```
nmap -p- -sC -sV --reason 10.10.10.10 -oA tcp_all_ports
```
```
nmap -sU --top-ports 100 --reason 10.10.10.10 -oA udp_top_100
```
```
nmap -sU -p- --reason --min-rate 5000 10.10.10.10 -oA udp_all_ports
```

#### NSE

```
nmap -p 445 --script="safe or smb-enum-*" -p 445 <target> 
```
- `--script-trace` for detailed output
- `--script-args` to pass arguments

##### Database location

```
grep smb-* /usr/share/nmap/scripts/script.db
```
- `--script-updatedb` update script database
  
#### IPv6 support

- `-6` for IPv6, not supported on all versions
{{% /panel %}}

#### IPv6 discovery
```
ping6 ff02::1 # All IPv6 hosts multicast address = 255.255.255.255 in IPv4
ping6 ff02::2 # All IPv6 routers multicast address 
ip neigh # Show cached neighbors find by ping6
```

#### Netcat port 

```
echo "" | nc -nvw2 <target> <port-range>
```

#### Windows ping sweep

```
for /L %i in (1,1,255) do @ping -n 1 -w 200 10.5.5.%i > nul && echo 10.5.5.%i is up.
```


#### Usage
Usage is similar to Nmap
```
masscan -p443 --rate 15000 10.0.0.0/8
```
#### Output Files
- `-oB` for binary file output to save space
- `--readscan` to convert to other formats
```
massscan --readscan masscan.binary -oX masscan.xml
```