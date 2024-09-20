

```python
import socket


client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

host = '192.168.3.85'
port = 6082

client_socket.connect((host, port))
client_socket.close()
```

