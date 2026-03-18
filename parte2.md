# 🛡️ Guia de Firewall - Parte 2: Liberação de Portas e Protocolos

Nesta etapa do estudo, focamos na abertura seletiva de portas para serviços essenciais. O objetivo é manter a política padrão de "Bloquear Tudo" (**Deny/Drop**) e permitir apenas o tráfego necessário para o funcionamento das aplicações.

---

## 🌐 1. Serviços Web e Administração (TCP)

O protocolo **TCP** é utilizado quando a integridade dos dados é prioritária (confirmação de recebimento).

### 5. SSH (Porta 22)
Essencial para acesso remoto seguro ao terminal.
* **UFW:** `sudo ufw allow 22/tcp`
* **iptables:** `sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT`
    * *Explicação:* `-A INPUT` adiciona à lista de entrada; `--dport 22` foca no destino; `-j ACCEPT` autoriza o pacote.

### 6. HTTP (Porta 80)
Tráfego padrão de sites e servidores web não criptografados.
* **UFW:** `sudo ufw allow 80/tcp`
* **iptables:** `sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT`

### 7. Porta de Desenvolvimento / API (Porta 8080)
Muito utilizada para rodar containers Docker ou servidores locais (Node.js, Spring Boot).
* **UFW:** `sudo ufw allow 8080/tcp`
* **iptables:** `sudo iptables -A INPUT -p tcp --dport 8080 -j ACCEPT`

---

## ⚡ 2. Serviços de Infraestrutura (UDP)

O protocolo **UDP** é focado em velocidade, ideal para consultas rápidas onde a perda de um pacote não interrompe o serviço.

### 8. DNS (Porta 53)
Responsável por traduzir nomes de domínio (ex: google.com) em IPs.
* **UFW:** `sudo ufw allow 53/udp`
* **iptables:** `sudo iptables -A INPUT -p udp --dport 53 -j ACCEPT`

---

## 🔑 3. Regras de Manutenção do Sistema (iptables)

No **iptables**, o fluxo de pacotes é manual. Sem as regras abaixo, mesmo com as portas acima abertas, o sistema pode apresentar instabilidade ou falta de conectividade.

### 9. Interface de Loopback (`lo`)
Permite que o sistema operacional e os bancos de dados falem com o `localhost` (127.0.0.1).
```bash
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT
