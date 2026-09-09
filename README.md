# lab-zabbix

Lab pessoal de estudo: Zabbix Server + Frontend + PostgreSQL via Docker Compose, com o Zabbix Agent 2 rodando nativamente na máquina host para monitorar o próprio sistema.

## Requisitos

- Docker e Docker Compose instalados
- Zabbix Agent 2 instalado na máquina host (fora do compose) — [guia oficial](https://www.zabbix.com/download_agents)

## Setup

1. Clone o repositório e entre na pasta.
2. Copie o arquivo de variáveis de ambiente e edite a senha:
```bash
   cp .env.example .env
```
3. Suba os containers:
```bash
   docker compose up -d
```
4. Acesse a interface em `http://localhost:8080`. Login inicial: `Admin` / `zabbix` — troque a senha no primeiro acesso.

## Estrutura

| Serviço | Função |
|---|---|
| `postgres-server` | Banco de dados — configuração e histórico do Zabbix |
| `zabbix-server` | Motor: recebe dados, avalia triggers, dispara alertas |
| `zabbix-web-service` | Frontend web (nginx + php-fpm) |

## Agente

O `zabbix-agent2` roda direto na máquina host (não containerizado), usando *active checks* apontando pra `127.0.0.1:10051` (porta do `zabbix-server` exposta pro host). Configuração em `/etc/zabbix/zabbix_agent2.conf`.

## Segurança

Este é um ambiente de estudo/local. Nunca commitar o `.env` real, e trocar a senha padrão do Postgres/Zabbix antes de expor isso além do `localhost`.