# lab-zabbix

Lab pessoal de estudo: Zabbix Server + Frontend + PostgreSQL + Grafana via Docker Compose, com o Zabbix Agent 2 rodando nativamente na máquina host para monitorar o próprio sistema.

## Requisitos

- Docker e Docker Compose instalados
- Zabbix Agent 2 instalado na máquina host (fora do compose) — [guia oficial](https://www.zabbix.com/download_agents)

## Setup

1. Clone o repositório e entre na pasta.
2. Copie o arquivo de variáveis de ambiente e edite a senha do Postgres:
```bash
   cp .env.example .env
```
3. Suba os containers:
```bash
   docker compose up -d
```
4. Zabbix: acesse `http://localhost:8080`. Login inicial `Admin` / `zabbix` — troca de senha forçada no primeiro acesso.
5. Grafana: acesse `http://localhost:3000`. Login inicial `admin` / `admin` — troca de senha forçada no primeiro acesso.

## Estrutura

| Serviço | Função |
|---|---|
| `postgres-server` | Banco de dados — configuração e histórico do Zabbix |
| `zabbix-server` | Motor: recebe dados, avalia triggers, dispara alertas |
| `zabbix-web-service` | Frontend web do Zabbix (nginx + php-fpm) |
| `grafana` | Dashboards e visualização, consumindo dados do Zabbix via plugin de datasource |

Dados persistidos em volumes nomeados gerenciados pelo Docker (`postgres-data`, `grafana-data`) — sobrevivem a `docker compose down`, mas são apagados com `docker compose down -v`.

## Agente

O `zabbix-agent2` roda direto na máquina host (não containerizado), usando *active checks* apontando pra `127.0.0.1:10051` (porta do `zabbix-server` exposta pro host). Configuração em `/etc/zabbix/zabbix_agent2.conf`. O `Hostname` no config precisa bater exatamente (case-sensitive) com o nome do host cadastrado no frontend do Zabbix, usando o template **"Linux by Zabbix agent active"**.

## Grafana + Zabbix

Requer o plugin de datasource do Zabbix instalado no Grafana (via catálogo de plugins na UI). Ao configurar a URL do datasource, use o **nome do serviço no Compose**, não `localhost`:

```
http://zabbix-web-service:8080
```

## Segurança

Este é um ambiente de estudo/local. Nunca commitar o `.env` real, e trocar as senhas padrão (Postgres, Zabbix, Grafana) antes de expor isso além do `localhost`.
