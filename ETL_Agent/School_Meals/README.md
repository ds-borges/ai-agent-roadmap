# 🍎 School Meals ETL Agent (Agente de Merenda Escolar)

> **Status:** Em Produção 🟢

## 📋 Sobre o Projeto
Este projeto consiste em um agente autônomo de ETL (Extract, Transform, Load) desenvolvido para otimizar o processo de relatórios da merenda escolar. 

O objetivo principal é eliminar o preenchimento manual e calculo de valores em planilhas complexas, substituindo-o por uma entrada de dados simples via linguagem natural (mensagem de texto), garantindo consistência, tratamento de erros e formatação automática para os relatórios finais.

## ⚙️ Arquitetura do Fluxo

O pipeline de dados segue a seguinte lógica:

1.  **Ingestão (Extract):** O agente recebe uma mensagem não estruturada via Telegram contendo a lista de insumos e quantidades (ex: *"arroz 5, feijão 2, carne 10..."*).
2.  **Processamento (Transform - Camada 1):** * Um script em **JavaScript** (via n8n) normaliza o texto, identifica padrões (Regex) e aplica lógica fuzzy para associar produtos a uma lista oficial padronizada.
    * Gera um resumo automático do cardápio (Café da Manhã e Almoço) para feedback imediato.
3.  **Armazenamento e Enriquecimento (Transform - Camada 2):**
    * Os dados brutos são enviados para o **Supabase (PostgreSQL)**.
    * Uma **Materialized View** aplica regras de negócio (cálculo de per capita, preenchimento de valores ausentes e correção de unidades) diretamente no banco.
4.  **Carga (Load):**
    * O n8n recupera os dados processados da View e realiza o *upload* para o **Google Sheets**.
    * Os dados são formatados horizontalmente para compatibilidade direta com o relatório oficial da secretaria.

## 🛠️ Tecnologias Utilizadas

* **Orquestração:** [n8n](https://n8n.io/) (Workflow Automation)
* **Linguagem de Script:** JavaScript (ES6+) para parsing de texto e lógica de regex.
* **Banco de Dados:** PostgreSQL (via Supabase)
    * Uso de *Materialized Views* para camada de transformação.
    * Tipagem forte (`NUMERIC`, `INTEGER`) para integridade de dados.
* **Interface de Entrada:** Telegram Bot API.
* **Destino Final:** Google Sheets API.

## ✨ Funcionalidades Chave

* **Parsing Inteligente:** O agente distingue automaticamente entre ingredientes do café da manhã e do almoço baseando-se no contexto.
* **Tratamento de Erros:** Identifica formatações numéricas incorretas (ponto vs vírgula) e corrige antes da inserção.
* **Feedback em Tempo Real:** Retorna ao usuário um resumo legível do que foi entendido (ex: *"Cardápio identificado: Pão com leite..."*).
* **Enriquecimento SQL:** Cálculos complexos de proporção são delegados ao banco de dados para maior performance.

## 🚀 Como Executar

1. Importe o arquivo JSON do workflow no n8n.
2. Configure as credenciais do Telegram e Google Sheets.
3. Execute o script SQL fornecido (`database_schema.sql`) para criar as tabelas e views no Supabase.
4. Inicie o agente.

---
*Desenvolvido por Diego Sousa Borges - Engenharia de Dados*