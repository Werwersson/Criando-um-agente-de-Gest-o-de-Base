# 📝 Resposta do Laboratório: A Wiki Perdida dos Arquivos Corporativos

> Preencha este arquivo com a sua proposta de solução.
>
> Sua resposta deve explicar como transformar os documentos brutos da pasta `raw/` em uma Wiki Corporativa Inteligente, pesquisável e segura usando apenas serviços da AWS.

---

## 👤 Identificação

**Nome:**  
Everson Vicktor Silva Silveira 

**Data:**  
14/09/2026

**Link do repositório:**  
https://github.com/Werwersson/

---

✅ Quest 1: O Mapa dos Arquivos Perdidos1.1 Formatos encontrados na pasta raw/Descreva quais tipos de arquivos existem dentro da pasta raw/.Sua resposta:Markdown- .pdf (Digitais): Nascem digitais, texto pode ser extraído diretamente via bibliotecas padrão ou parser simples. Implicam em estruturação mais limpa de contratos e relatórios.
- .pdf (Escaneados): Precisam de OCR. Geralmente são contratos assinados fisicamente, notas fiscais ou atas antigas.
- .jpg / .png: Precisam de OCR. Podem ser fotos de quadros brancos, recibos ou diagramas de arquitetura.
- .docx: Nascem digitais, podem ser lidos extraindo o XML interno. Contêm atas de reunião, rascunhos de políticas e documentação em andamento.
- .txt / .md: Nascem digitais, extração direta (plain text). Costumam conter anotações rápidas, readmes de projetos ou logs de sistema.
1.2 Principais desafios encontradosExplique quais dificuldades esses documentos podem apresentar.Sua resposta:Markdown- Falta de padronização de nomenclatura (ex: `doc1_final_v2.pdf` não diz nada sobre o conteúdo).
- Documentos escaneados com ruído visual, baixa resolução ou manchas, dificultando o OCR.
- Mistura de informações sensíveis (PII, dados financeiros) com dados públicos corporativos.
- Estruturas de layout complexas (tabelas, múltiplas colunas em PDFs) que quebram a ordem de leitura do texto se extraídos incorretamente.
- Ausência de metadados nativos confiáveis (arquivos copiados perdem a data de criação original).
1.3 Informações importantes a serem extraídasListe quais informações precisam ser identificadas para transformar os documentos em conhecimento pesquisável.Sua resposta:Markdown- Título implícito ou assunto principal do documento.
- Data de criação, assinatura ou validade.
- Entidades-chave (nomes de clientes, projetos, departamentos, funcionários).
- Resumo executivo do conteúdo.
- Decisões tomadas e próximos passos (especialmente em atas de reunião).
- Classificação de sigilo (Público, Interno, Confidencial).
1.4 Estratégia de classificação inicialComo você classificaria os documentos sem depender de subpastas dentro de raw/?Sua resposta:MarkdownA classificação inicial será feita em duas etapas automatizadas:
1. Classificação Técnica: Baseada no MIME type e extensão do arquivo (imagem, texto plano, documento complexo) para rotear para o pipeline de extração correto.
2. Classificação Semântica (Pós-extração): Utilizar um modelo de linguagem no Amazon Bedrock para analisar as primeiras páginas do texto extraído e categorizar o documento (ex: "Ata de Reunião", "Contrato", "Política Interna", "Nota Fiscal") e aplicar as tags correspondentes.
✅ Quest 2: O Portal de Entrada na AWS2.1 Armazenamento dos arquivos brutosExplique como os arquivos da pasta raw/ seriam enviados e armazenados na AWS.Sua resposta:MarkdownOs arquivos serão armazenados no Amazon S3 em um bucket de ingestão (ex: `corp-wiki-raw-zone`).
- AWS IAM: Garantirá que apenas os serviços de ingestão e administradores tenham acesso de escrita.
- AWS KMS: Será usado para criptografar os dados em repouso (SSE-KMS) garantindo conformidade de segurança.
- Amazon S3 Versioning: Será ativado para proteger contra sobrescritas acidentais ou exclusões maliciosas.
- Amazon S3 Lifecycle: Configurado para mover os arquivos originais para o S3 Glacier após 90 dias do processamento, reduzindo custos de armazenamento de longo prazo, já que o conteúdo estará indexado.
2.2 Preservação dos arquivos originaisExplique como garantir que os arquivos originais sejam mantidos intactos e rastreáveis.Sua resposta:MarkdownA integridade será garantida aplicando o S3 Object Lock no modo de Governança ou Conformidade para os arquivos brutos, tornando-os imutáveis (WORM - Write Once, Read Many). A rastreabilidade será garantida preservando o S3 URI do arquivo original (ex: `s3://corp-wiki-raw-zone/arquivo.pdf`) como um metadado central atrelado a qualquer texto extraído ou vetor gerado a partir dele.
2.3 Extração de texto dos documentosExplique como cada tipo de arquivo seria processado.Sua resposta:MarkdownO upload no S3 disparará um evento (via EventBridge) para uma AWS Step Functions, que orquestrará o fluxo:
- .txt e .md: Uma AWS Lambda lê o arquivo diretamente do S3 e extrai o texto plano.
- .docx e PDFs digitais simples: Uma AWS Lambda (com bibliotecas Python como `python-docx` ou `PyPDF2`) extrai o texto preservando a formatação básica de parágrafos.
- PDFs escaneados e Imagens (.jpg, .png): A Step Function aciona o Amazon Textract (usando `DetectDocumentText` ou `AnalyzeDocument` para tabelas/formulários) para realizar o OCR com alta precisão estrutural.
O texto final extraído de qualquer rota é salvo em um bucket S3 de processados (`corp-wiki-processed-zone`).
2.4 Tratamento de falhasExplique como sua solução identificaria e registraria erros de processamento.Sua resposta:MarkdownA AWS Step Functions terá blocos de `Catch` e `Retry` configurados. Se a extração falhar repetidamente (ex: arquivo corrompido, limite de tamanho no Textract), o fluxo será desviado para um estado de falha que:
1. Move a referência do arquivo para uma SQS Dead Letter Queue (DLQ).
2. Registra o erro detalhado e a stack trace no Amazon CloudWatch Logs.
3. Dispara um alerta via Amazon SNS para notificar a equipe de operações sobre o arquivo problemático, permitindo intervenção manual.
✅ Quest 3: A Relíquia dos Metadados3.1 Padronização dos textos processadosExplique como os textos extraídos seriam limpos, normalizados e preparados para consulta.Sua resposta:MarkdownUma AWS Lambda processará o texto bruto extraído:
- Limpeza: Remoção de caracteres especiais inválidos, cabeçalhos/rodapés repetitivos, e quebras de linha arbitrárias geradas pelo OCR.
- Normalização: Conversão de datas para o padrão ISO 8601 (YYYY-MM-DD) para facilitar ordenação, e padronização da codificação para UTF-8.
- Chunking inicial: Divisão do documento longo em seções lógicas baseadas em títulos textuais, preparatório para o particionamento semântico posterior.
3.2 Metadados propostosDefina quais metadados você extrairia de cada documento.MetadadoPor que ele é importante?Nome do documentoPermite a identificação humana rápida nos resultados da busca.Tipo do documentoEssencial para filtrar buscas (ex: "Buscar apenas em Atas" ou "Buscar em Contratos").Data identificadaCrucial para estabelecer a temporalidade da informação (saber se uma política é atual ou obsoleta).Tema principalFacilita a clusterização e a descoberta de documentos relacionados.ParticipantesPermite buscar decisões baseadas em quem estava presente na reunião.Decisões tomadasTransforma textos longos em insights acionáveis imediatos.ResponsáveisEssencial para auditoria e acompanhamento de tarefas corporativas.Próximos passosPermite rastrear pendências geradas pelo documento.Nível de confidencialidadeFundamental para controle de acesso (RBAC); garante que apenas pessoas autorizadas vejam o conteúdo.Caminho do arquivo originalGarante a rastreabilidade (S3 URI) para que o usuário possa consultar a fonte primária se necessário.3.3 Uso de IA para enriquecimento dos documentosExplique como o Amazon Bedrock poderia ajudar a identificar temas, decisões, responsáveis, pendências e resumos dos documentos.Sua resposta:MarkdownUma AWS Lambda, após receber o texto limpo, fará uma chamada de inferência para um LLM no Amazon Bedrock (ex: Claude 3.5 Sonnet). 
O prompt passará o texto do documento e solicitará que a IA retorne um objeto JSON estrito contendo: resumo executivo em 3 linhas, tema principal, entidades (pessoas, departamentos), lista de decisões e tabela de pendências com responsáveis. A IA é excelente para compreensão de linguagem natural, superando heurísticas baseadas em regex, especialmente em atas de reunião informais.
3.4 Armazenamento dos metadadosExplique onde os metadados seriam armazenados e como seriam conectados aos documentos originais.Sua resposta:MarkdownOs metadados JSON gerados pelo Bedrock serão armazenados em uma tabela do Amazon DynamoDB, usando o hash do documento ou o S3 URI original como Partition Key. 
Adicionalmente, esses metadados serão sincronizados com o Amazon Bedrock Knowledge Bases durante a indexação, anexando-os como atributos de metadados junto aos vetores de texto, o que permitirá filtragem rica no momento da busca vetorial (ex: buscar similaridade apenas em documentos marcados como "Público").
✅ Quest 4: O Oráculo da Wiki Inteligente4.1 Estratégia de indexaçãoExplique como os documentos seriam divididos em trechos menores e preparados para busca semântica.Sua resposta:MarkdownOs textos limpos serão divididos em chunks (fragmentos) usando uma estratégia de "Semantic Chunking" ou chunking por janelas deslizantes (ex: 500 tokens com 50 tokens de sobreposição). A sobreposição garante que o contexto na fronteira entre dois parágrafos não se perca. Isso é vital porque um LLM precisa recuperar blocos granulares e altamente relevantes para formular respostas precisas sem estourar a janela de contexto.
4.2 Busca semântica e base vetorialExplique como embeddings seriam gerados e onde seriam armazenados.Sua resposta:MarkdownUtilizaremos o Amazon Bedrock Knowledge Bases (KB) para orquestrar essa fase de forma nativa. 
O KB chamará automaticamente um modelo de embeddings (ex: Amazon Titan Text Embeddings) para converter os chunks de texto em vetores numéricos de alta dimensionalidade. Esses vetores serão armazenados em um índice do Amazon OpenSearch Serverless (Vector Search), configurado automaticamente pelo Bedrock KB, abstraindo o gerenciamento de infraestrutura.
4.3 Geração de respostas com IAExplique como a Wiki responderia perguntas em linguagem natural com base nos documentos originais.Sua resposta:MarkdownA solução utilizará a arquitetura RAG (Retrieval-Augmented Generation):
1. Recebimento: O usuário faz a pergunta via interface web.
2. Embed e Busca: A pergunta é convertida em vetor. O Bedrock KB busca no OpenSearch Serverless os chunks mais semanticamente similares à pergunta.
3. Geração: O prompt é montado contendo a instrução do sistema, a pergunta do usuário e os chunks recuperados como contexto, sendo enviado ao LLM no Bedrock.
4. Fontes: O LLM é instruído a responder estritamente com base no contexto fornecido e a incluir citações [1], [2], que o backend mapeia para os S3 URIs originais dos documentos.
4.4 Interface de consultaProponha como os usuários acessariam essa Wiki Inteligente.Sua resposta:MarkdownA abordagem mais rápida e robusta para o ambiente corporativo será o uso do Amazon Q Business. 
Ele fornece uma interface de chat pronta para uso empresarial, controle de acesso refinado (RBAC) e se integra nativamente ao IAM Identity Center para Single Sign-On (SSO). O Amazon Q permite conectar nossa base de documentos do S3/Bedrock como fonte de dados, criando um assistente de IA seguro, que exibe as referências (citações de documentos) diretamente na UI, sem necessidade de construir e manter um frontend web customizado do zero.
4.5 Segurança, auditoria e monitoramentoExplique como controlar acesso, proteger dados, auditar consultas e monitorar custos, erros e qualidade das respostas.Sua resposta:Markdown- Acesso e Autenticação: AWS IAM e IAM Identity Center (SSO integrado ao diretório corporativo) garantindo acesso baseado em roles (RBAC).
- Proteção e Privacidade: Amazon Macie para varrer o S3 e alertar sobre dados sensíveis expostos indevidamente; KMS para criptografia.
- Auditoria: AWS CloudTrail para registrar todas as chamadas de API (quem acessou o quê e quando).
- Monitoramento e Custos: Amazon CloudWatch para dashboards de erros da pipeline e latência de respostas do Bedrock; AWS Cost Explorer com tags obrigatórias (`Project: CorporateWiki`) para rastrear gastos específicos com LLM e infraestrutura.
🧩 Arquitetura Final da SoluçãoAgora reúna tudo em uma visão única.1. Visão geralExplique em poucas linhas a ideia central da sua arquitetura.Sua resposta:MarkdownA arquitetura propõe um pipeline de ingestão automatizado (Event-driven RAG) totalmente serverless. Arquivos brutos no S3 são processados por Step Functions, Textract (OCR) e Bedrock (extração de metadados). O conteúdo é fragmentado, vetorizado e armazenado via Bedrock Knowledge Bases e OpenSearch, permitindo que os colaboradores consultem o acervo corporativo por meio de uma interface segura em linguagem natural usando o Amazon Q Business.
2. Serviços AWS utilizadosServiço AWSPapel na soluçãoAmazon S3Armazenamento de arquivos brutos, processados e bucket de origem para a Knowledge Base.Amazon TextractExtração de texto (OCR) de PDFs escaneados e imagens com preservação de layout/tabelas.Amazon BedrockProvisionamento dos modelos de LLM para extração de metadados e geração de respostas em linguagem natural.Amazon Bedrock Knowledge BasesOrquestração nativa do RAG, criação de embeddings e integração com a base vetorial.AWS LambdaExecução de código leve para limpeza de texto, extração de arquivos simples (.txt) e formatação de JSONs.AWS Step FunctionsOrquestração do workflow de extração, tratamento de erros e retentativas automatizadas.Amazon CloudWatchMonitoramento de logs, alarmes de falha no processamento e métricas de latência/performance.AWS IAMControle estrito de acesso e permissões entre os serviços (Princípio do Menor Privilégio).AWS KMSCriptografia de dados em repouso (arquivos no S3 e banco vetorial).Amazon OpenSearch ServerlessArmazenamento dos vetores gerados e motor de busca semântica veloz.Amazon Q BusinessInterface de usuário final para consultas seguras, com citações e suporte a SSO corporativo.3. Fluxo de dados de ponta a pontaDescreva o caminho dos dados desde a pasta raw/ até a Wiki Inteligente.Sua resposta:Markdown1. Arquivos brutos são enviados para o bucket S3 de entrada (`raw/`).
2. O EventBridge detecta o upload e dispara o workflow da AWS Step Functions.
3. A orquestração decide a rota: documentos escaneados e imagens passam pelo Textract; texto puro pelo Lambda.
4. Os textos extraídos passam por uma Lambda para limpeza, remoção de lixo de OCR e normalização.
5. Uma chamada ao Bedrock (LLM) analisa o texto e extrai metadados essenciais em formato JSON.
6. Os documentos limpos e os metadados são salvos no bucket S3 de saída (`processed/`).
7. O Amazon Bedrock Knowledge Bases sincroniza o novo conteúdo, divide em chunks, gera embeddings e indexa no OpenSearch Serverless.
8. O usuário acessa o portal do Amazon Q Business e faz uma pergunta.
9. A IA recupera os trechos relevantes no OpenSearch, sintetiza uma resposta baseada unicamente no contexto corporativo e entrega ao usuário com links diretos para o documento original.
4. Diagrama textual da arquiteturaCrie um diagrama simples usando texto.Sua resposta:Markdown[Usuário / Fontes] 
       │ (Upload)
       ▼
[Amazon S3 (Bucket Raw)] ──(Event)──> [AWS Step Functions (Orquestrador)]
                                              │
                    ┌───────────────┬─────────┴─────────┐
                    ▼               ▼                   ▼
           [AWS Lambda]     [Amazon Textract]     [AWS Lambda]
           (Arquivos TXT)     (PDFs/Imagens)    (Enriquecimento Bedrock)
                    │               │                   │
                    └───────────────┴─────────┬─────────┘
                                              ▼
                                    [Amazon S3 (Processados + Metadados)]
                                              │ (Sync)
                                              ▼
                               [Amazon Bedrock Knowledge Bases] ──> [Amazon OpenSearch Serverless (Vetores)]
                                              │
                                              ▼
                                     [Amazon Q Business]
                                              │
                                              ▼
                                        [Usuário Final]
5. Riscos e limitaçõesListe possíveis desafios da sua solução.Sua resposta:Markdown- Qualidade de Imagem: Documentos manuscritos ou muito rasgados podem sofrer degradação severa no OCR, introduzindo erros na extração.
- Custos Variáveis: Escalar processamento de LLM e Knowledge Bases para milhões de páginas sem políticas de ciclo de vida rígidas no S3 pode causar picos de faturamento não previstos.
- Limites de Contexto: Documentos extremamente grandes podem ser desafiadores para o chunking, prejudicando a relação causa/efeito entre seções distantes.
- Atualização: O Knowledge Base requer sincronização periódica. Um arquivo recém-adicionado pode não estar disponível imediatamente para buscas antes da próxima sincronização.
6. Melhorias futurasDescreva como a solução poderia evoluir.Sua resposta:Markdown- Adicionar filtragem automática de informações sensíveis via Amazon Comprehend Medical/Macie antes do documento ser indexado na base vetorial.
- Integrar a Wiki Inteligente diretamente nos sistemas que a equipe já usa, como Microsoft Teams ou Slack, usando as APIs do Amazon Q.
- Criar um dashboard no Amazon QuickSight lendo os metadados do DynamoDB para identificar os temas e projetos mais discutidos nas atas e documentos corporativos ao longo do tempo.
- Implementar controle de acesso granular de documentos (ACL) passado diretamente da origem até a resposta final, garantindo acesso apenas a documentos permitidos.
🧠 Checklist FinalAntes de entregar, confirme se sua solução responde:[x] Como transformar documentos escaneados em texto?[x] Como lidar com diferentes formatos dentro da mesma pasta raw/?[x] Como armazenar os documentos originais?[x] Como preservar a rastreabilidade entre resposta e documento fonte?[x] Como organizar metadados?[x] Como criar busca semântica?[x] Como usar Amazon Bedrock na solução?[x] Como proteger documentos sensíveis?[x] Como monitorar falhas?[x] Como a empresa usaria essa Wiki no dia a dia?🏁 ConclusãoEscreva uma breve conclusão defendendo sua solução como se estivesse apresentando para uma liderança técnica ou de negócio.Sua resposta:MarkdownApresento esta solução arquitetada para resolver o problema crítico de passivo operacional gerado por acervos de dados desestruturados. Em vez de depender de uma catalogação manual demorada e suscetível a erros, propomos alavancar a inteligência artificial generativa aliada aos serviços 100% gerenciados e serverless da AWS.

Ao integrar ferramentas como Amazon Textract, Bedrock e Amazon Q Business, estabelecemos um fluxo altamente escalável e de excelente custo-benefício — pagamos estritamente pelo que processamos. O grande diferencial desta arquitetura é transformar repositórios engessados em uma plataforma de descoberta interativa, onde o colaborador interage em linguagem natural e recebe respostas precisas, ancoradas diretamente na documentação original, garantindo rastreabilidade e segurança empresarial. É o passo fundamental para transformar dados brutos em conhecimento acionável.