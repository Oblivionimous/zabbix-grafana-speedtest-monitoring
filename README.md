# Zabbix SpeedTest Monitoring

Este projeto oferece uma solução completa para monitoramento de velocidade da internet (download, upload e latência) com execução automatizada de testes de velocidade integrados ao Zabbix e visualização gráfica no Grafana.

## 🔧 Tecnologias

- Python (speedtest-cli)
- Zabbix Agent ou Zabbix Sender
- Grafana
- Linux (Ubuntu/RHEL/etc)

## 📑 Funcionalidades

- Execução periódica de Speed Test
- Envio automático dos resultados ao Zabbix
- Template de itens/triggers prontos para Zabbix
- Dashboard Grafana pronto para importação

## 📂 Estrutura

- `speedtest_script/`: Script Python de coleta
- `zabbix_template/`: Template XML para importar no Zabbix
- `grafana/`: Dashboard JSON pronto para importação
- `docs/`: Documentação de uso e integração

## 📥 Como usar

1. Clone o repositório
2. Importe o template no Zabbix
3. Configure o script no cron
4. Importe o dashboard no Grafana

## 📜 Licença

Distribuído sob a licença MIT. Veja `LICENSE` para mais informações.
