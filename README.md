# Web Scraping da Agenda do Presidente do Banco Central

## Descrição

Este projeto realiza a extração automatizada de informações da agenda de autoridades do Banco Central do Brasil, com foco na agenda do então Presidente do Banco Central, **Roberto Campos Neto**.

O sistema utiliza **web scraping com Selenium** para navegar pela agenda pública disponível no site do Banco Central e coletar informações sobre reuniões realizadas em um intervalo específico de datas.

Além da extração dos dados, o projeto utiliza a **API do Google Gemini** para interpretar os textos das reuniões e transformar informações não estruturadas em dados estruturados no formato JSON.

Os resultados são armazenados em um arquivo CSV, permitindo posteriormente a análise e apresentação dos dados em formato tabular.

---

## Objetivo

O objetivo do projeto é automatizar a coleta e estruturação das informações presentes na agenda pública do Presidente do Banco Central.

A extração considera reuniões realizadas no período de:

* **28 de fevereiro de 2023**
* até **28 de junho de 2024**

Para cada reunião identificada, são extraídas informações como:

* Horário de início;
* Horário de término;
* Data;
* Cargo da autoridade;
* Assunto da reunião;
* Local da reunião;
* Órgão;
* Entidade.

Posteriormente, os dados podem ser organizados em uma tabela contendo principalmente:

* Assunto da reunião;
* Local da reunião;
* Cargo;
* Órgão;
* Entidade.

---

## Tecnologias Utilizadas

O projeto foi desenvolvido em Python e utiliza as seguintes bibliotecas e ferramentas:

* **Python**
* **Selenium**
* **WebDriver Manager**
* **Microsoft Edge WebDriver**
* **Google Generative AI (Gemini)**
* **Regex (`re`)**
* **JSON**
* **CSV**

---

## Funcionamento

O fluxo de execução do projeto é dividido nas seguintes etapas:

### 1. Acesso à agenda pública

O Selenium acessa a página de agenda de autoridades do Banco Central:

**Banco Central do Brasil — Agenda de Autoridades**

[Agenda de Autoridades do Banco Central do Brasil](https://www.bcb.gov.br/acessoinformacao/agendaautoridades?utm_source=chatgpt.com)

---

### 2. Seleção das datas

O script manipula os campos de:

* Dia;
* Mês;
* Ano.

Dessa forma, realiza a navegação automática entre as datas do período definido para a coleta.

---

### 3. Identificação da autoridade

Para cada data consultada, o sistema verifica a autoridade presente na agenda.

O projeto filtra especificamente os registros relacionados a:

**Roberto Campos Neto**

Quando a autoridade encontrada corresponde ao nome esperado, o script prossegue com a extração das informações.

---

### 4. Identificação das reuniões

Os elementos presentes na agenda são analisados e o script identifica registros que contenham a palavra:

```text
reunião
```

Para isso, é utilizada uma função baseada em expressões regulares:

```python
def is_reuniao(texto, palavra):
    return re.search(r'\b' + re.escape(palavra) + r'\b', texto)
```

---

### 5. Processamento com Inteligência Artificial

Os textos das reuniões podem conter informações não estruturadas.

Para transformar essas informações em dados organizados, o projeto utiliza o modelo Gemini com um prompt solicitando o seguinte formato:

```json
{
    "horario_inicio": "",
    "horario_termino": "",
    "assunto_reuniao": "",
    "local_reuniao": "",
    "orgao": "",
    "entidade": ""
}
```

A resposta gerada é então convertida de JSON para um objeto Python utilizando:

```python
json.loads()
```

---

### 6. Armazenamento dos dados

As informações extraídas são armazenadas no arquivo:

```text
dados_agenda_reunioes.csv
```

O arquivo contém as seguintes colunas:

| Coluna            | Descrição                     |
| ----------------- | ----------------------------- |
| `horario_inicio`  | Horário de início da reunião  |
| `horario_termino` | Horário de término da reunião |
| `data`            | Data da reunião               |
| `cargo`           | Cargo da autoridade           |
| `assunto`         | Assunto da reunião            |
| `local`           | Local da reunião              |
| `orgao`           | Órgão relacionado             |
| `entidade`        | Entidade relacionada          |

---

## Estrutura do Projeto

```text
agenda-banco-central/
│
├── scraper.py
├── dados_agenda_reunioes.csv
├── requirements.txt
└── README.md
```

---

## Instalação

Clone o repositório:

```bash
git clone <URL_DO_REPOSITORIO>
```

Entre na pasta do projeto:

```bash
cd agenda-banco-central
```

Instale as dependências:

```bash
pip install selenium
pip install webdriver-manager
pip install google-generativeai
```

Ou, utilizando um arquivo `requirements.txt`:

```bash
pip install -r requirements.txt
```

---

## Configuração da API

O projeto utiliza a API do Google Gemini para estruturar os textos extraídos.

É necessário configurar uma chave de API antes da execução.

Recomenda-se utilizar uma variável de ambiente em vez de deixar a chave diretamente no código:

```python
import os
import google.generativeai as genai

api_key = os.getenv("GEMINI_API_KEY")
genai.configure(api_key=api_key)
```

No terminal:

### Windows

```bash
set GEMINI_API_KEY=sua_chave_aqui
```

### Linux/macOS

```bash
export GEMINI_API_KEY=sua_chave_aqui
```

> **Importante:** não publique chaves de API em repositórios públicos.

---

## Execução

Após instalar as dependências e configurar a API, execute:

```bash
python scraper.py
```

O Selenium abrirá o navegador e iniciará a navegação pela agenda de autoridades.

Durante a execução, o script:

1. Seleciona a data;
2. Consulta a agenda;
3. Verifica se a autoridade é Roberto Campos Neto;
4. Identifica compromissos relacionados a reuniões;
5. Envia o texto da reunião para processamento pelo Gemini;
6. Converte a resposta em JSON;
7. Salva os dados estruturados no arquivo CSV.

---

## Exemplo de Resultado

Exemplo de registro armazenado:

```text
horario_inicio,horario_termino,data,cargo,assunto,local,orgao,entidade
10:00,11:00,28_02_2023,Presidente,Reunião sobre ...,Brasília,Órgão ...,Entidade ...
```

Os dados podem então ser utilizados para gerar tabelas e análises como:

| Assunto da reunião       | Local da reunião | Cargo      | Órgão           | Entidade   |
| ------------------------ | ---------------- | ---------- | --------------- | ---------- |
| Reunião institucional    | Brasília         | Presidente | Banco Central   | Entidade X |
| Discussão sobre economia | Videoconferência | Presidente | Governo Federal | Entidade Y |

---

## Principais Desafios

Durante o desenvolvimento, alguns desafios importantes foram abordados:

* Navegação em uma página dinâmica;
* Manipulação de elementos HTML utilizando Selenium;
* Seleção automática de múltiplas datas;
* Extração de dados não estruturados;
* Identificação de reuniões através de padrões textuais;
* Uso de Inteligência Artificial para estruturação dos dados;
* Conversão das respostas geradas pelo modelo para JSON;
* Tratamento de possíveis erros durante a navegação e extração;
* Persistência dos resultados em formato CSV.

---

## Possíveis Melhorias

Algumas melhorias futuras para o projeto incluem:

* Utilizar seletores mais robustos em vez de XPath absoluto;
* Implementar um controle mais detalhado de erros e logs;
* Criar mecanismos de retomada da coleta em caso de interrupção;
* Evitar o uso de `except` genérico;
* Validar a estrutura do JSON retornado pela IA;
* Adicionar controle para evitar registros duplicados;
* Armazenar os dados em um banco de dados;
* Criar uma interface para visualização das reuniões;
* Gerar gráficos e dashboards a partir dos dados coletados;
* Permitir a seleção de outras autoridades do Banco Central;
* Atualizar a integração com modelos Gemini e suas versões atuais.

---

## Observações

Este projeto foi desenvolvido com fins de **automação, extração e estruturação de dados públicos**.

A combinação de **Web Scraping + Inteligência Artificial** permite transformar informações originalmente disponíveis em formato textual em uma base de dados estruturada, facilitando análises posteriores sobre os compromissos e reuniões presentes na agenda pública.

## Autor

Projeto desenvolvido por **João Pedro**, utilizando Python, Selenium e Inteligência Artificial Generativa.
