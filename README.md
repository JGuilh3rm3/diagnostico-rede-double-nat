# Diagnóstico de Rede - Double NAT

## Sobre este projeto

Este projeto demonstra um processo completo de troubleshooting de rede em ambiente real, incluindo análise de conectividade, diagnóstico de falhas de DNS e identificação de problemas de Double NAT.

O objetivo foi restaurar a conectividade de um PC com falhas intermitentes de internet, utilizando abordagem estruturada e boas práticas de diagnóstico.

---

## Contexto

**Problema:** perda intermitente de conectividade em ambiente doméstico com múltiplos roteadores.

**Cenário observado:**

* PC ficava sem internet de forma aleatória
* Roteador indicava funcionamento normal (LEDs verdes)
* Outros usuários na mesma rede não apresentavam problemas

---

## Topologia da Rede

```bash
Operadora (Claro) → Roteador do prédio (ZTE F6600P) → Cabo → TP-Link EC220-G5 → PC
```

---

## Hardware

* Placa-mãe: Biostar B450MH
* Adaptador de rede: Realtek PCIe GbE Family Controller
* Roteador pessoal: TP-Link EC220-G5

---

## Processo de Diagnóstico

### 1. Verificação de Conectividade

```bash
ping 8.8.8.8 -t
```

**Objetivo:** validar acesso à internet via teste contínuo de pacotes.

**Resultado:**

```bash
Resposta de 8.8.8.8: Esgotado o tempo limite do pedido.
```

**Conclusão:** ausência total de conectividade externa (100% de perda de pacotes).

---

### 2. Rastreamento de Rota

```bash
tracert 8.8.8.8
```

**Objetivo:** identificar até onde os pacotes chegam na rede.

**Resultado:**

* Pacote chegou ao gateway local (192.168.1.1)
* Não avançou além disso (erro código 1231)

**Conclusão:** falha localizada após o roteador local.

---

### 3. Verificação de Configuração de IP

```bash
ipconfig /all
```

**Objetivo:** analisar IP, gateway e DNS.

**Resultado:**

* IP atribuído corretamente (192.168.0.100)
* Campo de DNS não preenchido corretamente

**Conclusão:** falha na distribuição de configuração de rede, afetando resolução de nomes.

---

### 4. Limpeza de Cache DNS e Renovação de IP

```bash
ipconfig /flushdns
ipconfig /release
ipconfig /renew
```

**Objetivo:** eliminar inconsistências de rede local.

**Resultado:** problema persistiu.

**Conclusão:** falha não estava relacionada a cache ou concessão DHCP.

---

### 5. Verificação de Interfaces

```bash
netsh interface show interface
```

**Objetivo:** validar interface ativa.

**Resultado:** interface `Ethernet` identificada corretamente.

---

### 6. Reset de TCP/IP

```bash
netsh winsock reset
netsh int ip reset
```

**Objetivo:** restaurar configurações de rede do sistema.

**Resultado:** sem impacto na falha.

---

### 7. Verificação de Proxy

```bash
netsh winhttp reset proxy
```

**Objetivo:** descartar interferência de proxy.

**Resultado:** nenhum proxy configurado.

---

### 8. Teste com Firewall

```bash
netsh advfirewall set allprofiles state off
```

**Objetivo:** validar possível bloqueio de tráfego.

**Resultado:** não foi a causa raiz, apenas etapa de validação.

---

### 9. Reativação do Firewall

```bash
netsh advfirewall set allprofiles state on
```

---

## Alterações no Roteador

| Configuração     | Antes              | Depois          |
| ---------------- | ------------------ | --------------- |
| Modo operacional | Roteador           | Ponto de Acesso |
| DNS              | Automático (vazio) | 192.168.0.1     |

---

## Causa Raiz

Double NAT

Dois roteadores atuando como dispositivos de roteamento na mesma rede, introduzindo múltiplas camadas de NAT.

Isso resultava em:

* mascaramento duplo de endereços IP
* falhas no encaminhamento de pacotes
* inconsistência na distribuição de configurações de rede (como DNS)

---

## Solução

Alteração do TP-Link para modo Ponto de Acesso (Access Point), eliminando a segunda camada de NAT e permitindo que apenas o roteador principal gerencie o roteamento da rede.

---

## Conclusão

Este caso reforça a importância de mapear corretamente a topologia de rede antes de aplicar soluções isoladas, evitando diagnósticos incorretos e retrabalho.

---

## Habilidades Demonstradas

* Diagnóstico de rede (TCP/IP)
* Troubleshooting estruturado
* Análise de conectividade (ping, tracert)
* Interpretação de falhas de DNS
* Identificação e resolução de Double NAT
* Configuração de dispositivos de rede
* Análise de falhas em ambiente real

---

## Arquivos

* README.md
* diagnostico_rede.pdf

---

## Contato

* GitHub: https://github.com/JGuilh3rm3
* LinkedIn: https://www.linkedin.com/in/jo%C3%A3o-guilherme-andrade-227b14346/?skipRedirect=true

---

João Guilherme | ADS - UNIP São Paulo

