# Trabalho 1 programa Ping Pong
O cliente enviará uma mensagem ping a um servidor e receberá uma mensagem pong correspondente de volta do servidor e o progarama determinará o atraso entre o
momento em que o cliente enviou e recebeu a mensagem. O programa está na linguagem python e mostrará dois protocolos: o UDP e o TCP.

[![source.gif](https://i.postimg.cc/RFCzhHFH/source.gif)](https://postimg.cc/sBLL015j)

## UDP 🚀


- ### Cliente🙋‍♀️
Código do cliente UDP
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
Resposta do Cliente:  


<img src="https://i.postimg.cc/52FPVGBj/Captura-de-tela-2025-06-10-151011.png" alt="pingpong" width="600">


- ### Servidor ⚙️
Código do servidor UDP
```python
import socket

# Define o endereço IP e a porta em que o servidor escutará
server_address = ('200.135.87.59', 12000)  # Escuta em todas as interfaces de rede, na porta 12000

# Cria o socket UDP
server_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# Associa o socket ao endereço e porta definidos
server_socket.bind(server_address)

print("Servidor UDP aguardando mensagens...")

# Loop infinito para atender requisições continuamente
while True:
    # Aguarda receber dados do cliente
    data, client_address = server_socket.recvfrom(1024)  # Recebe até 1024 bytes

# Decodifica os dados recebidos
message = data.decode()

# Imprime a mensagem recebida para depuração
print(f"Recebido de {client_address}: {message}")

# Cria a resposta tipo "pong", pode incluir a mesma mensagem ou apenas um OK
response = f"Pong: {message}"

# Envia a resposta de volta ao cliente
server_socket.sendto(response.encode(), client_address)
```
Resposta do Servidor:


<img src="https://i.postimg.cc/4d4bmF96/Imagem-do-Whats-App-de-2025-06-10-s-15-11-41-e360b582.jpg" alt="pingpong" width="600">


## TCP 🔒


- ### Cliente 🙋‍♀️
Código do cliente TCP:
```python
import socket      # Biblioteca para comunicação de rede
import time        # Para medir o tempo (RTT)

# Endereço e porta do servidor TCP
server_address = ('200.135.66.17', 12000)  # Altere o IP se necessário

try:
    # Cria um socket TCP (SOCK_STREAM)
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    # Define o tempo máximo de espera por resposta como 1 segundo
    client_socket.settimeout(1)

    # Conecta ao servidor
    client_socket.connect(server_address)
    print("Conectado ao servidor TCP.\n")

    # Envia 10 mensagens ping numeradas
    for i in range(1, 11):
        # Cria a mensagem com número e timestamp
        mensagem = f"Ping {i} {time.time()}"

        try:
            # Marca o tempo de envio
            start_time = time.time()

            # Envia a mensagem ao servidor
            client_socket.send(mensagem.encode())

            # Tenta receber a resposta (até 1024 bytes)
            resposta = client_socket.recv(1024)

            # Marca o tempo de recebimento
            end_time = time.time()

            # Calcula o RTT
            rtt = end_time - start_time

            # Exibe a resposta e o RTT
            print(f"Resposta: {resposta.decode()} | RTT: {rtt:.6f} segundos")

        except socket.timeout:
            # Caso nenhuma resposta seja recebida em 1 segundo
            print(f"Ping {i} - Tempo esgotado. Pacote perdido.")

    # Encerra o socket ao final
    client_socket.close()
    print("\nConexão encerrada.")

except Exception as e:
    print("Erro ao conectar ao servidor:", e)
```
Resposta do Cliente:

 
 <img src="https://i.postimg.cc/mZJ6hCbP/Imagem-do-Whats-App-de-2025-06-10-s-15-14-09-0e9ce333.jpg" alt="pingpong" width="500">

 
- ### Servidor ⚙️
Código do servidor TCP:
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
Resposta do Servidor:  


<img src="https://i.postimg.cc/SRTZSb1Y/Captura-de-tela-2025-06-10-151541.png" alt="pingpong" width="500">

## 📊 Conclusão dos testes dos programas
|     UPD           |       TCP     | 
| ---------------   | ------------- |
| MAX: 0.014124 seg | MAX: 0.017595 seg |


### 🙋‍♀️UDP:

- Envia **rapidamente** os pacotes.

- Pode **perder pacotes**, especialmente em redes instáveis.

- Não garante ordem ou entrega.

- É útil para testes simples, mas exige mais cuidado com perdas.

### 🔒TCP:

- Garante a entrega de cada mensagem ping.

- Mesmo que a rede tenha atrasos, os pacotes chegam com segurança.

- O RTT tende a ser **levemente maior** devido ao controle de **confiabilidade**.

- Ideal quando não se pode perder pacotes, mesmo com um pouco mais de latência.
