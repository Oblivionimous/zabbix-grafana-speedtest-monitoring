# Zabbix SpeedTest Monitoring

Monitoramento automatizado da velocidade da internet (download, upload, latência, jitter e perda de pacotes), com integração ao Zabbix e visualização gráfica no Grafana.

---

## Visão Geral

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
Claro! Aqui está a seção **“Instalação do Speedtest CLI”** formatada em Markdown para você incluir diretamente no `README.md`:

---

## Instalação do Speedtest CLI

### Pré-requisitos

Certifique-se de instalar os pacotes básicos:

```bash
sudo apt install wget tar nano vi -y
```

### Preparar diretório para instalação

```bash
mkdir ~/speedtest
cd ~/speedtest
```

### Baixar Speedtest CLI da Ookla

#### Para sistemas **x86 (32 bits)**:

```bash
wget https://bintray.com/ookla/download/download_file?file_path=ookla-speedtest-1.0.0-i386-linux.tgz
```

#### Para sistemas **x64 (64 bits)**:

```bash
wget https://bintray.com/ookla/download/download_file?file_path=ookla-speedtest-1.0.0-x86_64-linux.tgz
```

### Renomear e extrair o pacote

```bash
mv download_file?file_path=ookla-speedtest-1.0.0-x86_64-linux.tgz ~/speedtest/speedtest-1.0.0-x86_64-linux.tgz
tar zxvf speedtest-1.0.0-x86_64-linux.tgz
sudo chmod +x speedtest
```

---

## Executando o Speedtest CLI

O binário `speedtest` já está pronto para execução. Use um dos comandos abaixo, conforme sua preferência ou ambiente:

```bash
./speedtest
~/speedtest/speedtest
speedtest --accept-gdpr
```

Na **primeira execução**, será necessário aceitar os termos de uso. Você pode fazer isso manualmente ou adicionando as opções abaixo ao comando:

```bash
speedtest --accept-license --accept-gdpr
```

---

## Exemplo de Saída Esperada

```text
Speedtest by Ookla

     Server: Algar Telecom - São Paulo (id: 28443)
        ISP: Algar Telecom
Idle Latency:    16.52 ms   (jitter: 0.36ms, low: 16.12ms, high: 17.26ms)
   Download:   785.51 Mbps (data used: 998.5 MB)
                68.59 ms   (jitter: 41.15ms, low: 17.27ms, high: 663.49ms)
     Upload:   685.79 Mbps (data used: 1.1 GB)
                17.52 ms   (jitter: 7.03ms, low: 15.48ms, high: 256.98ms)
Packet Loss: Not available.
 Result URL: https://www.speedtest.net/result/c/624e636d-92f9-47cc-a98b-ea54293a8188
```

Se você visualizar uma saída semelhante, a instalação foi concluída com sucesso.

---

## ⚙Configuração

### Configuração de Caminhos e UserParameters

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

## Referências

* [https://www.namehero.com/blog/measuring-internet-speed-on-linux-with-speedtest-cli/](https://www.namehero.com/blog/measuring-internet-speed-on-linux-with-speedtest-cli/)
* [https://phoenixnap.com/kb/linux-network-speed-test](https://phoenixnap.com/kb/linux-network-speed-test)
* [https://gist.github.com/itsChris/6f7a5d59b408f0cb774bf2570137a0ef](https://gist.github.com/itsChris/6f7a5d59b408f0cb774bf2570137a0ef)
* [https://plus.diolinux.com.br/t/velocidade-da-internet-com-speedtest-no-linux-monitoramento-pelo-zabbix/26280](https://plus.diolinux.com.br/t/velocidade-da-internet-com-speedtest-no-linux-monitoramento-pelo-zabbix/26280)

---

## 📜 Licença

Distribuído sob a licença [MIT](LICENSE).

---

Se quiser, posso gerar esse `README.md` já formatado em Markdown e te entregar o arquivo pronto para subir no GitHub. Deseja?
