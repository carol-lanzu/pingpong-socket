# pingpong-socket

## UDP


### Cliente
### Código do cliente UDP
```python
import socket
import time

# Define o endereço e a porta do servidor
server_address = ('200.135.87.59', 12000)  # Altere para o IP/porta do servidor, se necessário

# Cria o socket UDP
client_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# # Define o tempo máximo de espera por uma resposta (timeout) como 1 segundo
client_socket.settimeout(1)

# Envia 10 mensagens ping numeradas de 1 a 10
for i in range(1, 11):
    # Cria a mensagem com timestamp atual
    message = f"Ping {i} {time.time()}"
    
    try:
        # Armazena o tempo de envio da mensagem
        start_time = time.time()
        
        # Envia a mensagem para o servidor
        client_socket.sendto(message.encode(), server_address)
        
        # Aguarda a resposta do servidor (pong)
        data, server = client_socket.recvfrom(1024)
        
        # Armazena o tempo de recebimento da resposta
        end_time = time.time()
        
        # Calcula o RTT
        rtt = end_time - start_time
        
        # Exibe o resultado com o RTT formatado
        print(f"Resposta do servidor: {data.decode()} | RTT: {rtt:.6f} segundos")
    
    except socket.timeout:
        # Se não houver resposta em até 1 segundo, considera o pacote perdido
        print(f"Ping {i} - Tempo esgotado. Pacote perdido.")

# Fecha o socket após terminar os envios
client_socket.close()
```
<img src="https://i.postimg.cc/J0y404CN/Captura-de-tela-2025-05-20-171640.png" alt="pingpong" width="600">


Servidor


<img src="https://i.postimg.cc/4yWG6Yp7/Captura-de-tela-2025-05-20-171702.png" alt="pingpong" width="600">

### TCP


 Cliente

 
 <img src="https://i.postimg.cc/DfYSTR3Q/Captura-de-tela-2025-05-20-171534.png" alt="pingpong" width="500">

 
 Servidor
### Código do servidor TCP
```python
import socket  # Biblioteca para sockets

# Define o endereço e a porta onde o servidor vai escutar
server_address = ('200.135.66.17', 12000)  # '0.0.0.0' aceita conexões de qualquer IP na porta 12000

# Cria o socket TCP
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Liga o socket ao endereço e porta
server_socket.bind(server_address)

# Coloca o socket em modo de escuta (fila de até 1 conexão)
server_socket.listen(1)

print("Servidor TCP pronto e aguardando conexão...")

# Aceita uma conexão do cliente
connection, client_address = server_socket.accept()
print(f"Conexão estabelecida com {client_address}")

# Loop para receber múltiplas mensagens do cliente
try:
    while True:
        # Recebe até 1024 bytes de dados
        dados = connection.recv(1024)

        # Se não recebeu nada, o cliente fechou a conexão
        if not dados:
            print("Conexão encerrada pelo cliente.")
            break

        # Decodifica a mensagem recebida
        mensagem = dados.decode()
        print(f"Recebido: {mensagem}")

        # Cria a resposta "pong"
        resposta = f"Pong: {mensagem}"

        # Envia a resposta de volta ao cliente
        connection.send(resposta.encode())

except Exception as e:
    print("Erro durante a comunicação:", e)

# Fecha a conexão com o cliente
connection.close()
print("Servidor encerrado.")
```
 
<img src="https://i.postimg.cc/QCypRthv/Captura-de-tela-2025-05-20-171534.png" alt="pingpong" width="500">
