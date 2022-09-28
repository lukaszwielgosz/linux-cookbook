# Pipe one serial to another using socat

Create pipe using socat
```
sudo socat PTY,link=/dev/ttyS15,raw,echo=0,b9600 PTY,link=/dev/ttyS16,raw,echo=0,b9600
```

```
sudo cat /dev/ttyS15
```

```
echo "hello world" > /dev/ttyS16
```


# UDP serial bridge

UDP broadcast from serial port to udp
```
sudo socat -v pty,link=/dev/ttyS12,raw,echo=0,b115200 udp4-datagram:127.0.0.1:14555,broadcast
```

Listen for data from udp:
```
socat - udp4-listen:14555,reuseaddr,fork
```

```
socat -v udp4-datagram:172.24.176.116:54321 open:/dev/ttyS10,raw,nonblock,waitlock=/tmp/s0.locak,echo=0,b115200,crnl
```


- `-v` - verbose
- `udp4-datagram:172.24.176.207:54321` - UDP listening on port 54321
- `open:/dev/ttyUSB0` - sets up sending intput/output to your USB #0 device
- `raw` - raw mode, passes input/output unprocessed
- `nonblock` - opens/uses files in nonblocking mode
- `waitlock=/tmp/s0.locak` - creates a waitlock file - guards against multiple socat's
- `echo=0` - disables local echo on server
- `b115200` - sets the baud rate to 115,200 bits
- `crnl` - converts newlines from \n to \r\n

server:
```
socat -v udp4-listen:12345,fork,reuseaddr open:/dev/ttyUSB0,raw,nonblock,echo=0,b115200,crnl
```

client (takes input from stdin)
```
socat - udp4-datagram:127.0.0.1:12345,broadcast
```

# TCP serial bridge
```
socat -v tcp-l:15760,reuseaddr pty,link=/dev/ttyS12,raw,echo=0,b115200
```
