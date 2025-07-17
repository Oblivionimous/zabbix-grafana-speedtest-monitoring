# Zabbix SpeedTest Monitoring

Monitoramento automatizado da velocidade da internet (download, upload, latência, jitter e perda de pacotes), com integração ao Zabbix e visualização gráfica no Grafana.

---

## 🚀 Visão Geral

Este projeto fornece uma solução completa para verificar e monitorar a qualidade da sua conexão com a internet. A coleta dos dados é realizada por meio do Speedtest CLI da Ookla e os resultados são enviados ao Zabbix usando **UserParameters**. As informações são exibidas graficamente em dashboards do Grafana.

---

## 🔧 Tecnologias Utilizadas

* **Python + Speedtest CLI** (Ookla)
* **Zabbix Agent** ou **Zabbix Agent 2**
* **Grafana**
* **Linux** (Ubuntu, RHEL, AlmaLinux etc.)

---

## 📑 Funcionalidades

* Execução automática de testes de velocidade (download, upload, latência, jitter e perda de pacotes)
* Envio dos dados para o Zabbix através de arquivos `.txt` lidos por *UserParameters*
* Templates prontos para importar no Zabbix
* Dashboards prontos para importar no Grafana
* Suporte a múltiplas interfaces de rede
* Fácil customização por interface e estrutura

> 💡 **Observação:** Adapte os nomes de interface, caminhos e parâmetros de acordo com a estrutura da sua rede.

---

## ⚠️ Observações Importantes

> Este projeto **não tem como objetivo ensinar a configurar interfaces de rede, rotas ou regras de NAT em firewall de borda**.

Para monitorar múltiplos links de internet com interfaces distintas, é **imprescindível**:

* Ter as **interfaces de rede configuradas corretamente** com rotas dedicadas;
* Configurar **NAT no firewall de borda**, garantindo que o tráfego de cada interface saia pelo link correto;
* Criar **um script e um conjunto de parâmetros para cada interface** monitorada;
* Adaptar os nomes e caminhos nos scripts, crontab, e Zabbix conforme sua infraestrutura.

Se for utilizar apenas uma interface, basta configurar **um único script**, um conjunto de *UserParameters* e um template no Zabbix.

---

## ⚙️ Configuração

### 🛠️ Configuração de Caminhos e UserParameters

Os caminhos para os scripts e arquivos `.txt` devem ser definidos de acordo com o local onde você deseja armazenar os arquivos no seu ambiente.

**Exemplo de inclusão no arquivo `zabbix_agent.conf` ou `zabbix_agent2.conf`:**

#### SpeedTest `ens224`

```conf
UserParameter=latency_ens224,grep "Idle Latency:" /home/admlocal/speedtest/speedtestzabbix_ens224.txt | awk '{print $3}'
UserParameter=jitter_ens224,grep "Idle Latency:" /home/admlocal/speedtest/speedtestzabbix_ens224.txt | sed 's/.*jitter: \([0-9.]*\)ms.*/\1/'
UserParameter=download_ens224,grep "Download:" /home/admlocal/speedtest/speedtestzabbix_ens224.txt | awk '{print $3}'
UserParameter=upload_ens224,grep "Upload:" /home/admlocal/speedtest/speedtestzabbix_ens224.txt | awk '{print $3}'
UserParameter=loss_ens224,grep "Packet Loss:" /home/admlocal/speedtest/speedtestzabbix_ens224.txt | awk '{if ($3=="Not") print 0; else print $3}' | sed 's/%//'
UserParameter=isp_ens224,tail -n 1 /home/admlocal/speedtest/speedtestzabbix_ens224.txt
```

#### SpeedTest `ens256`

```conf
UserParameter=latency_ens256,grep "Idle Latency:" /home/admlocal/speedtest/speedtestzabbix_ens256.txt | awk '{print $3}'
UserParameter=jitter_ens256,grep "Idle Latency:" /home/admlocal/speedtest/speedtestzabbix_ens256.txt | sed 's/.*jitter: \([0-9.]*\)ms.*/\1/'
UserParameter=download_ens256,grep "Download:" /home/admlocal/speedtest/speedtestzabbix_ens256.txt | awk '{print $3}'
UserParameter=upload_ens256,grep "Upload:" /home/admlocal/speedtest/speedtestzabbix_ens256.txt | awk '{print $3}'
UserParameter=loss_ens256,grep "Packet Loss:" /home/admlocal/speedtest/speedtestzabbix_ens256.txt | awk '{if ($3=="Not") print 0; else print $3}' | sed 's/%//'
UserParameter=isp_ens256,tail -n 1 /home/admlocal/speedtest/speedtestzabbix_ens256.txt
```

> Após editar o arquivo do agente, **reinicie o serviço**:
>
> ```bash
> sudo systemctl restart zabbix-agent
> ```

---

### ⏱️ Agendamento via `crontab`

Configure o agendamento para execução periódica dos testes com os scripts `runspeedtest_ens224` e `runspeedtest_ens256`.

```bash
crontab -e
```

Adicione as linhas abaixo:

```cron
# Speedtest ens224 a cada minuto (ou ajuste para rodar de 5 em 5 minutos)
* * * * * /home/admlocal/speedtest/runspeedtest_ens224

# Speedtest ens256 a cada minuto (ou ajuste para rodar de 5 em 5 minutos)
* * * * * /home/admlocal/speedtest/runspeedtest_ens256
```

## 📚 Referências

* [https://www.namehero.com/blog/measuring-internet-speed-on-linux-with-speedtest-cli/](https://www.namehero.com/blog/measuring-internet-speed-on-linux-with-speedtest-cli/)
* [https://phoenixnap.com/kb/linux-network-speed-test](https://phoenixnap.com/kb/linux-network-speed-test)
* [https://gist.github.com/itsChris/6f7a5d59b408f0cb774bf2570137a0ef](https://gist.github.com/itsChris/6f7a5d59b408f0cb774bf2570137a0ef)
* [https://plus.diolinux.com.br/t/velocidade-da-internet-com-speedtest-no-linux-monitoramento-pelo-zabbix/26280](https://plus.diolinux.com.br/t/velocidade-da-internet-com-speedtest-no-linux-monitoramento-pelo-zabbix/26280)

---

## 📜 Licença

Distribuído sob a licença [MIT](LICENSE).

---

Se quiser, posso gerar esse `README.md` já formatado em Markdown e te entregar o arquivo pronto para subir no GitHub. Deseja?
