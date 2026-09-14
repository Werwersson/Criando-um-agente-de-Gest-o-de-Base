# 🧭 Laboratório Prático: A Wiki Perdida dos Arquivos Corporativos

## 🎮 Contexto da Missão

Você acaba de encontrar uma pasta chamada `raw/`.

Dentro dela estão documentos brutos de uma empresa fictícia: uma ata de reunião em PDF, uma folha de ata digitalizada e uma exportação de oportunidades do CRM.

Os três chegam de jeitos diferentes e exigem tratamentos diferentes. Um já nasce com texto dentro, outro é só imagem e precisa de OCR, e o terceiro não é texto corrido, é tabela. Parte do desafio é descobrir isso abrindo os arquivos.

Esses arquivos representam anos de conhecimento espalhado, sem padronização, sem busca eficiente e sem uma forma simples de encontrar decisões, responsáveis, datas, temas discutidos ou próximos passos definidos em reuniões anteriores.

Sua missão é propor, usando apenas serviços da AWS, como transformar esses dados brutos em uma **Wiki Corporativa Inteligente, pesquisável e segura**.

Você não precisa implementar a solução completa.

O objetivo deste laboratório é analisar os arquivos da pasta `raw/` e preencher o arquivo [`resposta.md`](./resposta.md), descrevendo como você resolveria esse desafio passo a passo.

---

## 🏛️ A Lenda dos Arquivos Perdidos

Durante anos, a empresa registrou decisões importantes em diferentes formatos de documentos. Com o tempo, esses arquivos foram se acumulando dentro da pasta `raw/`, sem organização e sem uma estrutura clara de consulta.

Agora, a liderança quer responder perguntas como:

- “Quais decisões foram tomadas sobre o projeto X?”
- “Quem ficou responsável pela ação Y?”
- “Em quais reuniões o tema segurança foi discutido?”
- “Quais foram os principais riscos apontados no último trimestre?”
- “Existe algum documento que fale sobre orçamento, contratação ou fornecedores?”
- “Quais próximos passos ficaram pendentes em reuniões anteriores?”

Para isso, a empresa deseja criar uma Wiki Inteligente, capaz de pesquisar, resumir e responder perguntas com base nos documentos originais.

---

## 📁 Estrutura Inicial do Repositório

```bash
.
├── README.md
├── resposta.md
└── raw/
    ├── ata_reuniao_vendas_sa.pdf                    # 5 paginas, camada de texto: sem OCR
    ├── ata_resultados_vendas_novos_dados.png        # 1 pagina digitalizada, so pixels: exige OCR
    └── vendas_sa_dados_ficticios_laboratorio.csv    # 240 oportunidades do CRM, 19 colunas
```

A pasta `raw/` representa os dados brutos da empresa.

> **Importante:** não existem subpastas dentro de `raw/`. Todos os arquivos estarão misturados diretamente nessa pasta.

Parte do desafio é explicar como você organizaria, processaria e classificaria esses documentos usando serviços da AWS.

---

## 🎯 Objetivo do Desafio

Criar uma proposta técnica explicando como transformar os arquivos da pasta `raw/` em uma Wiki de Dados pesquisável usando somente serviços da AWS.

Sua resposta final deve ser escrita no arquivo:

```bash
resposta.md
```

Ao final, uma pessoa deve conseguir entender:

- Como os documentos seriam armazenados;
- Como os arquivos escaneados seriam processados;
- Como o texto seria extraído e limpo;
- Como os metadados seriam organizados;
- Como os documentos seriam indexados;
- Como a busca semântica funcionaria;
- Como uma IA poderia responder perguntas com base nos documentos;
- Como garantir segurança, rastreabilidade e governança.

---

## ⚔️ Regras da Expedição

Antes de começar, respeite as regras do templo:

- Use apenas serviços da AWS.
- Não use ferramentas externas de OCR, banco vetorial ou IA fora da AWS.
- Não altere os arquivos da pasta `raw/`.
- Considere que todos os documentos estão diretamente dentro da pasta `raw/`, sem subpastas.
- Preencha sua solução no arquivo `resposta.md`.
- Descreva sua proposta de forma clara, organizada e objetiva.
- Justifique suas escolhas técnicas.
- Explique o fluxo de dados do início ao fim.
- Pense em segurança, rastreabilidade, custo e escalabilidade.
- Não basta listar serviços: explique como eles se conectam.

---

# 🗺️ Quests Principais

## ✅ Quest 1: O Mapa dos Arquivos Perdidos

Antes de construir qualquer solução, você precisa entender o terreno.

Explore os arquivos da pasta `raw/` e descreva quais tipos de documentos existem, quais informações eles podem conter e quais desafios eles apresentam.

### Sua missão

- [ ] Identificar os formatos de arquivo presentes na pasta `raw/`.
- [ ] Diferenciar documentos digitais de documentos escaneados.
- [ ] Identificar possíveis desafios, como baixa qualidade de imagem, arquivos sem padrão, documentos longos, tabelas, anotações soltas ou textos incompletos.
- [ ] Descrever quais informações são importantes extrair das atas e documentos.
- [ ] Explicar como você classificaria os arquivos sem depender de subpastas.

### Pontos de atenção

Considere que os documentos podem conter:

- Datas de reuniões;
- Participantes;
- Temas discutidos;
- Decisões tomadas;
- Responsáveis por ações;
- Prazos;
- Riscos;
- Pendências;
- Projetos citados;
- Áreas ou departamentos envolvidos.

---

## ✅ Quest 2: O Portal de Entrada na AWS

Agora que você conhece os documentos, explique como eles entrariam no ambiente da AWS e como seriam processados.

Sua missão é descrever o pipeline inicial: armazenamento, leitura dos arquivos, extração de texto e preparação dos dados.

### Serviços AWS que você pode considerar

- Amazon S3
- Amazon Textract
- AWS Lambda
- AWS Step Functions
- Amazon CloudWatch
- AWS IAM
- AWS KMS

### Sua missão

- [ ] Explicar como os arquivos da pasta `raw/` seriam enviados para o Amazon S3.
- [ ] Definir como preservar os arquivos originais.
- [ ] Explicar como identificar quais documentos precisam de OCR.
- [ ] Descrever como o Amazon Textract seria usado para documentos escaneados.
- [ ] Explicar como o PDF com camada de texto seria tratado, sem passar por OCR.
- [ ] Explicar como o CSV do CRM entraria na solução, lembrando que ele é tabela e não texto corrido.
- [ ] Definir onde os textos extraídos seriam armazenados.
- [ ] Explicar como falhas de processamento seriam registradas.

---

## ✅ Quest 3: A Relíquia dos Metadados

Uma Wiki inteligente não depende apenas do texto dos documentos.

Ela também precisa de metadados para organizar, filtrar e contextualizar as informações.

Nesta quest, explique como você transformaria documentos bagunçados em registros organizados e úteis.

### Serviços AWS que você pode considerar

- Amazon Bedrock
- Amazon Bedrock Knowledge Bases
- AWS Lambda
- Amazon S3
- Amazon DynamoDB
- AWS Glue Data Catalog

### Sua missão

- [ ] Definir um formato padronizado para os textos processados.
- [ ] Explicar como limpar ruídos, quebras de linha e conteúdos duplicados.
- [ ] Propor quais metadados seriam extraídos de cada documento.
- [ ] Explicar como a IA poderia ajudar a identificar temas, decisões, responsáveis e pendências.
- [ ] Descrever onde os metadados seriam armazenados.
- [ ] Explicar como conectar cada metadado ao documento original.

### Exemplos de metadados úteis

| Metadado | Exemplo |
|---|---|
| Nome do documento | `ata_reuniao_vendas_sa.pdf` |
| Tipo de documento | Ata de reunião |
| Data identificada | 15/03/2026 |
| Tema principal | Planejamento comercial |
| Participantes | Ana, Bruno, Camila |
| Decisões tomadas | Aprovar nova campanha |
| Responsáveis | Bruno |
| Próximos passos | Enviar proposta revisada |
| Nível de confidencialidade | Interno |
| Arquivo original | Caminho no Amazon S3 |

---

## ✅ Quest 4: O Oráculo da Wiki Inteligente

Com os documentos processados e enriquecidos, chegou a hora de propor como a Wiki Inteligente funcionaria.

Nesta quest, descreva como a empresa poderia pesquisar os documentos, fazer perguntas em linguagem natural e receber respostas com base nos arquivos originais.

### Serviços AWS que você pode considerar

- Amazon Bedrock
- Amazon Bedrock Knowledge Bases
- Amazon Bedrock Agents
- Amazon OpenSearch Serverless
- Amazon Aurora PostgreSQL com pgvector
- Amazon S3 Vectors
- Amazon Q Business
- Amazon API Gateway
- AWS Lambda
- Amazon Cognito
- Amazon CloudWatch
- AWS CloudTrail

### Sua missão

- [ ] Explicar como os documentos seriam divididos em trechos menores.
- [ ] Descrever como embeddings seriam gerados.
- [ ] Definir onde a base vetorial seria armazenada.
- [ ] Explicar como a busca semântica encontraria informações relevantes.
- [ ] Descrever como o Amazon Bedrock responderia perguntas com base nos documentos.
- [ ] Explicar como as respostas citariam ou referenciariam os arquivos de origem.
- [ ] Propor uma interface de consulta para os usuários.
- [ ] Explicar como controlar acesso, segurança e auditoria.
- [ ] Descrever como monitorar uso, erros, custos e qualidade das respostas.

### Exemplo de experiência esperada

Um usuário poderia perguntar:

```md
Quais foram as principais decisões tomadas sobre o projeto de expansão comercial?
```

A Wiki Inteligente deveria retornar uma resposta baseada nos documentos processados, indicando:

- Resumo da resposta;
- Documentos usados como fonte;
- Datas relacionadas;
- Pessoas envolvidas;
- Decisões encontradas;
- Possíveis próximos passos.

---

# 🏆 Sistema de Pontuação

Sua entrega será avaliada como uma jornada de exploração.

## 🥉 Nível Explorador

Você alcança este nível se:

- Explicar o problema com clareza;
- Listar os principais serviços AWS;
- Descrever um fluxo básico de processamento;
- Mostrar como os documentos poderiam se tornar pesquisáveis.

## 🥈 Nível Aventureiro

Você alcança este nível se:

- Separar bem armazenamento, extração, normalização, indexação e consulta;
- Explicar o papel de cada serviço;
- Incluir metadados;
- Pensar em segurança e monitoramento;
- Justificar suas escolhas.

## 🥇 Nível Guardião da Wiki Perdida

Você alcança este nível se:

- Criar uma arquitetura completa e coerente;
- Explicar o fluxo de ponta a ponta;
- Usar busca semântica e RAG de forma bem descrita;
- Propor filtros, metadados e rastreabilidade;
- Pensar em custos, segurança, governança e evolução futura;
- Apresentar a solução como se fosse um projeto real para uma empresa.

---

# 🚀 Entrega Final

Para concluir o laboratório:

1. Faça um fork deste repositório.
2. Leia os documentos disponíveis na pasta `raw/`.
3. Abra o arquivo `resposta.md`.
4. Preencha as quatro quests principais.
5. Descreva sua arquitetura usando apenas serviços AWS.
6. Faça o commit da sua resposta.
7. Envie o link do seu repositório.

---

# 🏁 Mensagem Final da Missão

Você não está apenas organizando arquivos.

Você está reconstruindo a memória de uma empresa.

Cada ata, cada anotação e cada PDF pode esconder uma decisão importante, um risco esquecido ou uma oportunidade perdida.

Sua missão é transformar esse caos documental em uma Wiki Inteligente, pesquisável e segura, usando o poder da nuvem AWS.

Boa expedição, explorador(a).  
A Wiki Perdida espera por você.
