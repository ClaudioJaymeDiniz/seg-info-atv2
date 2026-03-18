# 🛡️ Guia Prático: UFW vs. iptables (Linux Mint)

Este guia apresenta uma comparação direta entre o **UFW** (Uncomplicated Firewall) e o **iptables**, demonstrando como realizar as configurações básicas de segurança em sistemas baseados em Debian/Ubuntu.

---

## 📋 Visão Geral

| Característica | **UFW** | **iptables** |
| :--- | :--- | :--- |
| **Nível** | Interface simplificada (High-level) | Comando direto do kernel (Low-level) |
| **Uso Principal** | Desktop e servidores rápidos | Configurações complexas e específicas |
| **Facilidade** | Alta (Comandos intuitivos) | Moderada/Alta (Sintaxe rigorosa) |

---

## 🚀 Parte 1: Políticas e Status do Sistema

### 1. Verificar Status e Regras Ativas
Verifica se o firewall está filtrando pacotes no momento.

* **UFW:**
    ```bash
    sudo ufw status verbose
    ```
* **iptables:**
    ```bash
    sudo iptables -L -n -v
    ```
    > **Dica:** O `-n` evita que o sistema tente resolver nomes de domínio, tornando o comando instantâneo.

---

### 2. Habilitar e Desabilitar
O UFW funciona como um serviço on/off. O iptables é parte integrante do kernel; "desativá-lo" significa limpar as restrições.

* **UFW:**
    ```bash
    sudo ufw enable    # Ativa
    sudo ufw disable   # Desativa
    ```
* **iptables:**
    Para "desativar", limpamos as regras (**Flush**) e permitimos todo o tráfego:
    ```bash
    sudo iptables -F
    sudo iptables -P INPUT ACCEPT
    sudo iptables -P FORWARD ACCEPT
    sudo iptables -P OUTPUT ACCEPT
    ```

---

### 3. Política Padrão de Entrada (INPUT)
Define o que acontece com pacotes que não batem com nenhuma regra específica (Segurança Padrão: Bloquear tudo).

* **UFW:**
    ```bash
    sudo ufw default deny incoming
    ```
* **iptables:**
    ```bash
    sudo iptables -P INPUT DROP
    ```

---

### 4. Política Padrão de Saída (OUTPUT)
Define se a sua máquina pode enviar dados para fora (Padrão: Permitir para atualizações e navegação).

* **UFW:**
    ```bash
    sudo ufw default allow outgoing
    ```
* **iptables:**
    ```bash
    sudo iptables -P OUTPUT ACCEPT
    ```

---

## 🛠️ Cenários de Uso Comum (Dev & DB)

### Registro (LOG) antes de Bloqueio (Ex: Porta 5432)
Registra tentativas de conexão no banco de dados antes de descartar o pacote.

* **UFW:**
    ```bash
    sudo ufw log limit 5432/tcp
    sudo ufw deny 5432/tcp
    ```
* **iptables:**
    ```bash
    sudo iptables -A INPUT -p tcp --dport 5432 -j LOG --log-prefix "DB_ATTEMPT: "
    sudo iptables -A INPUT -p tcp --dport 5432 -j DROP
    ```

### Redirecionamento de Porta (Port Forwarding)
Cenário: Rodar sua aplicação na porta `8080` e expô-la na porta `80`.

* **iptables (NAT):**
    ```bash
    sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
    ```

---

## ⚠️ Notas Importantes
1. **Persistência:** As regras do `iptables` são perdidas ao reiniciar. Use `iptables-persistent` para salvá-las.
2. **Ordem importa:** No iptables, se você colocar um `DROP` antes de um `ACCEPT` para o mesmo alvo, o acesso será bloqueado.

---
*Documentação desenvolvida por Claudio Jayme - FATEC Jessen Vidal.*
