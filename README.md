# Lab: Prometheus + Blackbox Exporter (Docker Compose)

Este repositório é um laboratório simples para entender o fluxo do **Blackbox Exporter** com **Prometheus**, monitorando um alvo HTTP (Nginx) via endpoint `/probe`.

---

## Objetivo do lab

- Subir **Prometheus**, **Blackbox Exporter** e **Nginx** com Docker Compose
- Entender como o Prometheus faz scrape do Blackbox em `/probe`
- Validar métricas como `probe_success` no Blackbox e no Prometheus UI

---

## Fluxo de execução da probe:

1. **Prometheus inicia o scrape**
   - O Prometheus executa um scrape no **Blackbox Exporter** usando o endpoint **`/probe`**.
   - Ele envia os parâmetros: `target` (endpoint a ser monitorado: `http://nginx:80`) e `module` (define como um probe deve ser executado)

2. **Servidor HTTP do Blackbox recebe a requisição**
   - A requisição chega no servidor HTTP do Blackbox.
   - O endpoint `/probe` direciona o processamento para o **Probe Handler**.

3. **Probe Handler interpreta os parâmetros`target` e `module`**
   - Ele consulta o **SafeConfig** para encontrar as regras do módulo solicitado (`http_2xx`).

4. **A probe é executada no target (Nginx), respeitando as regras do módulo (timeout, método HTTP etc.).**

5. **Conversão do resultado em métricas**
   - Exemplo:
     - `probe_success`

6. **Resposta do `/probe` retorna as métricas**
   - As métricas geradas são enviadas na resposta HTTP do endpoint `/probe`.

7. **Prometheus ingere e armazena na TSDB**

---

## Resultados da Lab:

- Blackbox retornando probes:

```bash
curl "http://localhost:9115/probe?target=http://nginx:80&module=http_2xx"
```
<img width="426" height="53" alt="image" src="https://github.com/user-attachments/assets/83169d26-a785-40ce-802b-1c6583c28885" />

- Prometheus conseguindo scrapear o Blackbox (Target Health):
<img width="1301" height="321" alt="image" src="https://github.com/user-attachments/assets/a0a4ee0a-333a-4b22-835e-dac7b7c32a97" />

- Métricas disponíveis para consulta no Prometheus:
<img width="1302" height="536" alt="image" src="https://github.com/user-attachments/assets/fb007e26-b270-4b92-9e5e-7f62978d4b4d" />

### Referências:
https://www.geeksforgeeks.org/devops/prometheus-blackbox-exporter/
https://deepwiki.com/prometheus/blackbox_exporter


  



