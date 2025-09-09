# FIAP - Faculdade de Informática e Administração Paulista

<p align="center">
<a href= "https://www.fiap.com.br/"><img src="assets/logo-fiap.png" alt="FIAP - Faculdade de Informática e Admnistração Paulista" border="0" width=40% height=40%></a>
</p>

<br>

# Enterprise Challenge - Sprint 3 - Reply

## Nome do grupo

Rumo ao NEXT!

## 👨‍🎓 Integrantes:

- Felipe Livino dos Santos (RM 563187)
- Daniel Veiga Rodrigues de Faria (RM 561410)
- Tomas Haru Sakugawa Becker (RM 564147)
- Daniel Tavares de Lima Freitas (RM 562625)
- Gabriel Konno Carrozza (RM 564468)

## 👩‍🏫 Professores:

### Tutor(a)

- Leonardo Ruiz Orabona

### Coordenador(a)

- ANDRÉ GODOI CHIOVATO


## 📜 Descrição

## 📜 Descrição

Este repositório corresponde à **Fase 5 do Challenge Reply (FIAP)**, realizada em parceria com a **Hermes Reply**, cujo foco é unir a **modelagem de banco de dados** a uma aplicação prática de **Machine Learning**. O trabalho parte da necessidade de estruturar dados de sensores em um banco relacional e, em seguida, aplicar algoritmos de aprendizado de máquina para identificar padrões e realizar previsões a partir dessas informações.

Dentro do repositório, na pasta `_notebooks`, encontra-se o **notebook principal do projeto**, que reúne todo o processo: análise exploratória dos dados, preparação do dataset, construção do modelo de Machine Learning, avaliação de desempenho e geração de gráficos para visualização dos resultados. Esse notebook concentra a documentação prática do trabalho, integrando código, explicações e evidências gráficas dos achados obtidos.

## Diagrama Entidade-Relacionamento (DER) - Descrição Detalhada

O banco de dados foi projetado para gerenciar informações relacionadas a equipamentos, sensores e suas respectivas leituras. O objetivo é centralizar e organizar esses dados de forma estruturada, permitindo fácil consulta, manutenção e integração com aplicações externas.
A modelagem é composta por três tabelas principais:

- T_EQUIPMENTS: armazena informações dos equipamentos, como marca, modelo e status.
- T_SENSOR: registra os dados referentes a cada sensor individual.
- T_LEITURA_SENSOR: armazena as leituras de temperatura, umidade e vibração, relacionando-as diretamente aos equipamentos e sensores por meio de chaves estrangeiras.

A criação dessas tabelas é realizada por meio de scripts SQL, executados em um banco de dados Oracle em execução dentro de um container Docker.

<img src="_modelagem/diagrama_ER.png" alt="Diagrama ER" width="500"/>

O modelo é composto por três entidades centrais que se relacionam para registrar as medições dos sensores de um equipamento específico.

---

## Entidade: T_EQUIPAMENTO

Esta tabela armazena o cadastro dos equipamentos que estão sendo monitorados.

**Propósito:** Manter um registro único para cada máquina ou ativo industrial, permitindo agrupar e analisar os dados por equipamento.

**Atributos:**

- `id` (NUMBER(15), Chave Primária - PK): Identificador numérico único para cada equipamento.  
- `marca` (VARCHAR2(255)): Marca do fabricante do equipamento (ex: "Siemens", "WEG").  
- `modelo` (VARCHAR2(255)): Modelo específico do equipamento.  
- `status` (VARCHAR2(60)): Estado operacional atual do equipamento (ex: "Ativo", "Em Manutenção", "Inativo").  

---

## Entidade: T_SENSOR

Esta tabela armazena o cadastro dos sensores utilizados no monitoramento.

**Propósito:** Manter um registro de cada sensor individualmente, já que um equipamento pode ter vários sensores e estes podem ser substituídos ao longo do tempo.

**Atributos:**

- `id` (NUMBER(15), Chave Primária - PK): Identificador numérico único para cada sensor.  
- `nome` (VARCHAR2(255)): Nome ou tipo do sensor (ex: "DHT22", "MPU6050").  
- `status` (VARCHAR2(50)): Estado operacional do sensor (ex: "Operacional", "Com Falha", "Desativado").  
- `data_ativacao` (DATE): Data em que o sensor foi instalado e começou a operar.  

---

## Entidade: T_LEITURA_SENSOR

Esta é a tabela principal (fato), que armazena os dados coletados ao longo do tempo.

**Propósito:** Registrar cada medição individual enviada pelo microcontrolador (ESP32), sendo a principal fonte de dados para análises e modelos de Machine Learning.

**Atributos:**

- `id` (NUMBER(15), Chave Primária - PK): Identificador único para cada registro de leitura.  
- `status` (VARCHAR2(50)): Status reportado no momento da coleta (ex: "Normal", "Alerta de Vibração").  
- `temperatura` (NUMBER(6,2)): Valor da temperatura aferido, com duas casas decimais.  
- `umidade` (NUMBER(6,2)): Valor da umidade aferido, com duas casas decimais.  
- `vibracao` (NUMBER(6,2)): Valor da vibração aferido, com duas casas decimais.  
- `data_coleta` (TIMESTAMP): Registro exato da data e hora da coleta.  
- `T_EQUIPAMENTO_id` (NUMBER(15), Chave Estrangeira - FK): Referência ao equipamento que gerou a leitura.  
- `T_SENSOR_id` (NUMBER(15), Chave Estrangeira - FK): Referência ao sensor que realizou a leitura.  

---

## Relacionamentos

- **T_EQUIPAMENTO e T_LEITURA_SENSOR:** Relacionamento de um-para-muitos (1:N). Um equipamento pode ter muitas leituras, mas cada leitura pertence a um único equipamento.  
- **T_SENSOR e T_LEITURA_SENSOR:** Relacionamento de um-para-muitos (1:N). Um sensor pode realizar muitas leituras, mas cada leitura é feita por um único sensor.  

---

## Script SQL de Criação das Tabelas (CREATE TABLE)

```sql
-- Tabela para armazenar os equipamentos monitorados
CREATE TABLE T_EQUIPAMENTO (
    id NUMBER(15) NOT NULL,
    marca VARCHAR2(255) NOT NULL,
    modelo VARCHAR2(255) NOT NULL,
    status VARCHAR2(60) NOT NULL,
    CONSTRAINT T_EQUIPAMENTO_PK PRIMARY KEY (id)
);

-- Tabela para armazenar os sensores individuais
CREATE TABLE T_SENSOR (
    id NUMBER(15) NOT NULL,
    nome VARCHAR2(255) NOT NULL,
    status VARCHAR2(50) NOT NULL,
    data_ativacao DATE,
    CONSTRAINT T_SENSOR_PK PRIMARY KEY (id)
);

-- Tabela para armazenar os dados coletados pelos sensores
CREATE TABLE T_LEITURA_SENSOR (
    id NUMBER(15) NOT NULL,
    status VARCHAR2(50) NOT NULL,
    temperatura NUMBER(6,2),
    umidade NUMBER(6,2),
    vibracao NUMBER(6,2),
    data_coleta TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL,
    T_EQUIPAMENTO_id NUMBER(15) NOT NULL,
    T_SENSOR_id NUMBER(15) NOT NULL,
    CONSTRAINT T_LEITURA_SENSOR_PK PRIMARY KEY (id),
    CONSTRAINT T_LEITURA_SENSOR_T_EQUIPAMENTO_FK FOREIGN KEY (T_EQUIPAMENTO_id) REFERENCES T_EQUIPAMENTO(id),
    CONSTRAINT T_LEITURA_SENSOR_T_SENSOR_FK FOREIGN KEY (T_SENSOR_id) REFERENCES T_SENSOR(id)
);

-- Criar sequências para autoincremento dos IDs
CREATE SEQUENCE SEQ_EQUIPAMENTO START WITH 1 INCREMENT BY 1;
CREATE SEQUENCE SEQ_SENSOR START WITH 1 INCREMENT BY 1;
CREATE SEQUENCE SEQ_LEITURA_SENSOR START WITH 1 INCREMENT BY 1;

4. Previsão de Integração Futura com Ferramentas de Visualização
Esta modelagem de dados é ideal para integração com ferramentas de Business Intelligence (BI) e visualização de dados como Power BI, Tableau ou Grafana.
•	Conectividade: As ferramentas de BI podem se conectar diretamente a este banco de dados via conectores padrão (ODBC/JDBC).
•	Análise de Séries Temporais: O campo data_coleta (TIMESTAMP) na tabela T_LEITURA_SENSOR é a chave para a criação de gráficos de linha que mostram a evolução da temperatura, umidade e vibração ao longo do tempo.
•	Dashboards: É possível criar dashboards interativos que filtrem os dados por equipamento (T_EQUIPAMENTO_id), permitindo que um gerente de manutenção visualize o status de uma máquina específica em tempo real ou em um período selecionado.
•	Modelos de ML: Os dados bem estruturados na tabela T_LEITURA_SENSOR servem como uma fonte de dados limpa e organizada para treinar os modelos de Machine Learning, que poderão prever falhas com base em padrões históricos de vibração e temperatura.
```
**4. Previsão de Integração Futura com Ferramentas de Visualização**
Esta modelagem de dados é ideal para integração com ferramentas de Business Intelligence (BI) e visualização de dados como Power BI, Tableau ou Grafana.
 -	Conectividade: As ferramentas de BI podem se conectar diretamente a este banco de dados via conectores padrão (ODBC/JDBC).
 - Análise de Séries Temporais: O campo data_coleta (TIMESTAMP) na tabela T_LEITURA_SENSOR é a chave para a criação de gráficos de linha que mostram a evolução da temperatura, umidade e vibração ao longo do tempo.
 - Dashboards: É possível criar dashboards interativos que filtrem os dados por equipamento (T_EQUIPAMENTO_id), permitindo que um gerente de manutenção visualize o status de uma máquina específica em tempo real ou em um período selecionado.
 -	Modelos de ML: Os dados bem estruturados na tabela T_LEITURA_SENSOR servem como uma fonte de dados limpa e organizada para treinar os modelos de Machine Learning, que poderão prever falhas com base em padrões históricos de vibração e temperatura.

## 🤖 Análise da Implementação e Resultados do Machine Learning

### Implementação do Machine Learning

**Fonte de Dados e Objetivo:**  
O modelo foi treinado utilizando uma base de dados gerada a partir dos sensores do projeto, contendo as variáveis **temperatura**, **umidade** e **vibração**. O objetivo é um **problema de classificação multiclasse**, em que o modelo prevê o **status do equipamento** como `"normal"`, `"alerta"` ou `"perigo"`.

**Análise e Preparação dos Dados:**  
Antes do treinamento, foi realizada uma análise exploratória para compreender o comportamento das variáveis. Essa análise incluiu:
- Exibição das primeiras linhas do dataset;  
- Estatísticas descritivas (média, mínimo, máximo, percentis);  
- Verificação de valores nulos (não foram encontrados valores nulos);  
- Análise visual, com histogramas das features e matriz de correlação.  

Para o treinamento, as colunas **id** e **status** foram separadas: todas as demais colunas foram usadas como **features**, enquanto a coluna **status** foi usada como **target**. O dataset foi dividido em **80% para treinamento** e **20% para teste**.

### Modelagem e Treinamento

Foram testados cinco algoritmos de classificação distintos:
- **Árvore de Decisão**  
- **Support Vector Classifier (SVC)**  
- **AdaBoost**  
- **Regressão Logística**  
- **Random Forest**

Cada modelo foi treinado e avaliado com base em métricas de performance, incluindo **acurácia**, **precisão**, **recall** e **F1-score**. O objetivo foi identificar o modelo com melhor desempenho no conjunto de teste.

### Principais Resultados e Conclusões

**Performance dos Modelos:**  
- A **Árvore de Decisão** e o **Random Forest** apresentaram desempenho máximo, alcançando **100% de acurácia, precisão e recall** para todas as classes.  
- Os demais modelos apresentaram desempenho inferior:
  - **AdaBoost** não classificou nenhum caso como `"normal"`.  
  - **Regressão Logística** apresentou resultados insatisfatórios, especialmente considerando a métrica F1-score.  

**Seleção do Melhor Modelo:**  
Apesar de a Árvore de Decisão e o Random Forest terem alcançado performance máxima, a **Árvore de Decisão** foi escolhida como modelo final por ser **mais performática e menos complexa**, consistindo em uma única árvore, em contraste com as múltiplas árvores do Random Forest.

**Finalização:**  
O modelo final de Árvore de Decisão foi salvo em formato **pickle (`.pkl`)**, permitindo sua reutilização para classificar novos dados provenientes dos sensores em tempo real.

## 🎥 Vídeo Demonstrativo

Confira o funcionamento do projeto neste vídeo demonstrativo:
▶️ [Assista ao vídeo no YouTube](https://youtu.be/AHhEeYmowgA)

## 📁 Estrutura de pastas
```
challenge-fase5-FIAP/
├── 01_assets/                                # Imagens, diagramas e outros arquivos visuais
│
├── 02_data/                                  # Arquivos de dados utilizados no projeto
│   └── dados_sensores_challenge.csv          # Dataset usado para treino do modelo
│
├── 03_modelagem/                             # Arquivos de modelagem do banco de dados
│   ├── challenge_sql.dmd                     # Modelo conceitual/DER
│   ├── Diagrama entidade relacionamento.pdf  # Script SQL e relatorio do projeto de banco de dados dessa fase
│   └── diagrama_ER.png                       # Imagem exportada do Diagrama ER
│
├── 04_notebooks/                             # Notebook com análise e machine learning
│   └── main_challenge.ipynb                  # Implementação e treino do modelo de ML
│
├── README.md                                 # Documentação do projeto
```

## 🔧 Como executar o código

Este projeto foi desenvolvido em **Python** e utiliza **Jupyter Notebook** para documentar todo o fluxo de análise de dados e Machine Learning.

### Pré-requisitos

- Python 3.9 ou superior  
- Jupyter Notebook ou Jupyter Lab  
- Bibliotecas Python:
  - `pandas`
  - `numpy`
  - `matplotlib`
  - `seaborn`
  - `scikit-learn`(from `sklearn.model_selection` import `train_test_split`)

> **Dica:** É recomendado criar um ambiente virtual antes de instalar as bibliotecas.

### Passo a passo

1. **Clonar o repositório**  
   ```bash
   git clone https://github.com/danivrf/challenge-fase5-FIAP.git
2. **Navegar até a pasta do projeto**
    ```bash
   cd challenge-fase5-FIAP
3. **Instalar as bibliotecas necessárias**
   ```bash
   pip install pandas numpy matplotlib seaborn scikit-learn
4. **Abrir o notebook**
   - Entre na pasta `_notebooks`
   - Abra o arquivo `.ipynb` no Jupyter Notebook ou Jupyter Lab
 5. **Executar o notebook**
    - Execute as células na ordem, que incluem:
        - Análise exploratória dos dados
        - Preparação do dataset
        - Construção e treinamento do modelo de Machine Learning
        - Visualizações e gráficos de resultados
 **Visualizar resultados**
    - Os gráficos e outputs do notebook mostram insights sobre os dados coletados pelos sensores e as predições do modelo.

## 📋 Licença

<img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/cc.svg?ref=chooser-v1"><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/by.svg?ref=chooser-v1"><p xmlns:cc="http://creativecommons.org/ns#" xmlns:dct="http://purl.org/dc/terms/"><a property="dct:title" rel="cc:attributionURL" href="https://github.com/agodoi/template">MODELO GIT FIAP</a> por <a rel="cc:attributionURL dct:creator" property="cc:attributionName" href="https://fiap.com.br">Fiap</a> está licenciado sobre <a href="http://creativecommons.org/licenses/by/4.0/?ref=chooser-v1" target="_blank" rel="license noopener noreferrer" style="display:inline-block;">Attribution 4.0 International</a>.</p>


