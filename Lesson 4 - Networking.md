### 1. Network basics
#### Core Network Components
- **ISP (Internet Service Provider)**: The company that provide the internet connection to your home.
- **Router**: The central hub that manage traffic for devices that connect to it, allowing each machine to connect to the internet and communicate with each other.
- **Modem**: This device job is to translate incoming analog signals from ISP into digital data.
#### Others Optional Components
- **Switch**
- **Firewall**
- **Access Point**
#### Network Types
There are 3 common network type you will encounter:
- **LAN (Local Area Network)**: A group of devices connected together in a small space like home, school, or office.
- **WAN (Wide Area Network)**: A large group of computers that connects smaller netwokrs over big distances. Large businesses use WAN to connect multiple office and each office contains local area network (Internet can also considered a **WAN**)
- **WLAN (Wireless Local Area Network)**: It a local area network but instead of using cable, it uses radio waves to connect.
### 2. TCP/IP Model
The TCP/IP model is based on OSI (Open Systems Interconnection) model, a conceptual framework to help understand and standardize the function of a telecommunication. It has 7 distinct layers:
1. Physical
2. Data Link
3. Network
4. Transport
5. Session
6. Presentation
7. Application
But TCP/IP only uses 4 layers, which is **Application**, **Transport**, **Network**, and **Link**
![[images/tcp-ip_layer.png]]
#### Application Layer
This is the top layer of the TCP/IP, where applications such as web browsers, email clients, and file-sharing tools interact with the network.

This layer uses protocols like:
- HTTP
- SMTP
- FTP
#### Transport Layer
This layer responsible for end-to-end communication and data integrity which ensures reliable and efficient delivery of data between devices.
This layer primarily uses:
- TCP (Transmission Control Protocal): Provides reliable, ordered, and error-checked delivery of a stream of data. It is connection-oriented.
- UDP (User Datagram Protocal): Offers a faster, connectionless data delivery but it unreliable.
#### Network Layer
The main job of this layer is to specifies how to move packets between hosts and across different networks, which is addressing and routing.

This layer uses protocols like:
- IP (Internet Protocal): Routes packets from a source machine to a destination machine.
- ICMP (Internet Control Message Protocol): Used for sending error messages and operational information, such as with the `ping` command.
#### Link Layer
This layer responsible for physically transmitting data over network hardware, including cable, wireless, and fiber optic.
### TCP vs UDP
Make the TCP connection loss 50%
```
sudo iptables -A INPUT -m statistic -p tcp --mode random --probability 0.5 -j DROP
```

Make the UDP connection loss 50%
```
sudo iptables -A INPUT -m statistic -p tcp --mode random --probability 0.5 -j DROP
```

Make 2 terminal, one to host, one to connect. We will use `netcat` for this

Create a UDP Listen
```
nc -ul 9999
```
In another terminal, create a UDP connection to port `9999`
```
nc -u 127.0.0.1 9999
```

Create a TCP Listen
```
nc -lp 9999
```
In another terminal, create a TCP connection to port `9999`
```
nc 127.0.0.1 9999
```

Turn off these after done
```
sudo iptables -D A INPUT -m statistic -p tcp --mode random --probability 0.5 -j DROP
```

```
sudo iptables -D A INPUT -m statistic -p tcp --mode random --probability 0.5 -j DROP
```
