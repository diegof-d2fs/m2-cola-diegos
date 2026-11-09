# Roteiro falado: Modelagem de Ameaças no OWASP Juice Shop

O texto entre aspas é a fala completa sugerida. As indicações entre colchetes são orientações e não precisam ser lidas. Ensaiem com cronômetro: o limite é de **15 minutos para a equipe**, seguidos de perguntas.

**Divisão sugerida dos fluxos, conforme o relatório:** Diego apresenta FX1; Leonardo, FX2; Gustavo, FX3; e João Victor, FX4. Todos devem participar, e a equipe deve fazer ao menos uma pergunta a outra apresentação.

**Divisão sugerida dos demais slides (equilibra o tempo de fala):** Diego: slides 1 a 4 e 7. Leonardo: slides 5 e 8. Gustavo: slides 6, 9 e 11. João Victor: slides 10 e 12 a 15.

**Tempo por bloco (estrutura sugerida pelo roteiro do professor):** contexto, cerca de 2 min (slides 1 a 3); método e DFD, cerca de 3 min (slides 4 e 5); fluxos e principais ameaças, cerca de 7 min (slides 6 a 10); Top 10 e mitigações, cerca de 2 min (slides 11 e 12); validação, lições e comparação com o Trabalho 1, cerca de 1 min e 30 s (slides 13 a 15). Total previsto: cerca de 14 min e 50 s. As falas completas somam cerca de 12 min lidas em ritmo normal; o resto é folga para apontar o diagrama e respirar.

**Se o professor perguntar qual ferramenta gerou os diagramas:** “Usamos o draw.io, com o modelo descrito como código: um arquivo JSON com todas as peças e um script Python que gera o arquivo .drawio e as imagens.” A resposta completa está no apêndice “Ferramentas usadas e como responder”.

**Como ler os códigos em 30 segundos (decore isto):** a letra diz **o tipo** e o número só **diferencia um do outro**. **E** = Entidade externa (uma pessoa ou sistema de fora: visitante, cliente, admin, contabilidade). **P** = Processo (uma parte do programa que faz algo com os dados). **D** = armazenamento de Dados (onde o dado fica guardado: banco, memória, arquivo, log). **F** = Fluxo de dados (uma seta do diagrama: um dado indo de um lugar para outro). **TB** = *Trust Boundary*, fronteira de confiança (linha tracejada onde o nível de confiança muda). **FX** = um dos quatro fluxos de negócio escolhidos (uma funcionalidade de ponta a ponta, como “login”). **T-FX2-06** = ameaça (*Threat*) número 06 do fluxo FX2. **V1 a V7** = vulnerabilidades encontradas no Trabalho 1 (M1). **CWE-número** = tipo de fraqueza num catálogo mundial. **A01, A02…** = categoria da lista OWASP Top 10 de 2021. O dicionário completo de todos os códigos está no primeiro apêndice, no fim desta cola.

### Slide 1: Capa

[Diego · cerca de 30 s]

**FALA**

“Boa noite, professor e colegas. Nós somos Diego Fonseca da Silva, Leonardo Pacheco B Dias, Gustavo Gonçalves Trindade e João Victor Rodrigues Santos.

Neste trabalho, apresentamos a modelagem de ameaças do OWASP Juice Shop, a mesma aplicação utilizada no Trabalho 1. OWASP é a Open Worldwide Application Security Project, uma comunidade que produz materiais abertos sobre segurança de aplicações. Nosso objetivo foi representar o funcionamento do sistema, identificar o que poderia dar errado e propor medidas para reduzir os riscos encontrados.”

**O QUE SIGNIFICA O QUE APARECE NESTE SLIDE**

- **Modelagem de ameaças**: olhar o desenho do sistema e perguntar, de forma organizada, “onde alguém mal-intencionado poderia causar estrago?”, antes ou independentemente de testar ataques.
- **OWASP Juice Shop**: loja virtual falsa, feita de propósito cheia de falhas, para estudar segurança. É a aplicação que usamos no Trabalho 1.

### Slide 2: O que veremos

[Diego · cerca de 20 s]

**FALA**

“Primeiro, vamos contextualizar a aplicação e explicar o método. Depois, apresentaremos o diagrama de fluxo de dados e as fronteiras de confiança.

Depois, apresentaremos os quatro fluxos, as ameaças prioritárias, as mitigações e a validação. O diagrama será o fio condutor da apresentação.”

**O QUE SIGNIFICA O QUE APARECE NESTE SLIDE**

- **Fio condutor**: o roteiro do professor pede para “começar pelo DFD”, o desenho do sistema; todas as ameaças apontam para pedaços desse desenho.

### Slide 3: Contexto do ambiente

[Diego · cerca de 1 min]

**FALA**

“O sistema analisado é o OWASP Juice Shop, na versão vinte ponto dois ponto zero. Ele simula uma loja virtual e foi desenvolvido para o ensino de segurança de aplicações.

A interface Angular se comunica com uma API, isto é, uma interface de programação pela qual a aplicação troca solicitações e respostas com o servidor. Essa API usa Node e Express. A aplicação usa SQLite, uma coleção em memória para pedidos e tokens JWT, ou JSON Web Tokens, para representar informações da sessão. Os comprovantes são arquivos PDF, um formato de documento portátil.

O laboratório foi executado em contêiner Docker, no endereço local e porta três mil. Consideramos visitante, cliente, administrador e usuário de contabilidade, além de ativos como contas, credenciais, pedidos e dados pessoais.

Usamos as evidências da M1 para analisar também a estrutura e os caminhos dos dados.”

**O QUE SIGNIFICA O QUE APARECE NESTE SLIDE**

- **C1 a C10** (no título): as dez perguntas obrigatórias do roteiro sobre o ambiente (o que é o sistema, arquitetura, implantação, atores, ativos, superfície de ataque, dependências, fluxos, premissas e relação com o Trabalho 1). Foram respondidas no capítulo 3 do relatório.
- **v20.2.0**: número da versão do Juice Shop usada. Fixamos a versão para que o que analisamos seja exatamente o que rodou.
- **Angular**: tecnologia da parte visual que roda no navegador. **SPA**: página única que se atualiza sem recarregar.
- **Node/Express**: tecnologia do servidor (o “back-end”) que responde às chamadas.
- **SQLite**: banco de dados que fica num arquivo dentro da própria aplicação. **MarsDB**: banco simples em memória usado para os pedidos.
- **JWT (RS256)**: o “crachá digital” que o servidor entrega depois do login; o navegador o mostra em cada chamada. RS256 é o tipo de assinatura que impede alterá-lo sem a chave, mas **não esconde** o conteúdo.
- **Tag + digest**: tag é o nome da versão da imagem Docker (`v20.2.0`); digest é a “impressão digital” (sha256) que garante que é exatamente aquela imagem, byte a byte.
- **127.0.0.1:3000**: endereço do próprio computador (loopback), porta 3000. Só quem está na máquina acessa; ninguém da rede.
- **Docker / contêiner / rede bridge**: o Juice Shop roda isolado numa “caixa” (contêiner) com uma rede interna do Docker.
- **admin** e **accounting**: papéis (perfis) de usuário do sistema: administrador e contabilidade. Também existem **customer** (cliente comum) e **deluxe** (cliente pago).
- **LGPD**: Lei Geral de Proteção de Dados (Lei 13.709/2018).
- **M1**: o Trabalho 1. **M2**: este Trabalho 2.

### Slide 4: Método: DFD, STRIDE e DREAD

[Diego · cerca de 1 min]

**FALA**

“O método segue as quatro perguntas de Shostack: o que estamos construindo, o que pode dar errado, o que faremos a respeito e se fizemos um bom trabalho.

Para responder à primeira, usamos o DFD, diagrama de fluxo de dados, que representa atores, processos, armazenamentos e transferências. Para identificar ameaças, aplicamos STRIDE, método que organiza cenários em seis categorias de ameaça. Em seguida, utilizamos DREAD, método que avalia cinco dimensões para ajudar a priorizar os cenários.

As mitigações foram relacionadas a referências como ASVS, padrão de verificação de segurança de aplicações, e CWE, catálogo de tipos de fraqueza. Também consideramos a LGPD, Lei Geral de Proteção de Dados Pessoais, quando aplicável.”

**O QUE SIGNIFICA O QUE APARECE NESTE SLIDE**

- **Shostack (2014)**: Adam Shostack, autor do livro de referência sobre modelagem de ameaças; dele vêm as quatro perguntas.
- **STRIDE por elemento**: cada tipo de peça do desenho é examinado só nas categorias que fazem sentido para ele (regra da Microsoft): entidade externa → S e R; processo → as seis; armazenamento → T, I, D (e R se guardar log); fluxo de dados → T, I, D.
- **N/A sempre justificado**: “não aplicável”; quando uma categoria não gerou ameaça numa peça, escrevemos por quê (códigos N/A 1 a N/A 13 no relatório).
- **DREAD**: cinco notas de 1 a 10 — Dano, Reprodutibilidade, Explorabilidade (facilidade de executar), usuários Afetados e Descoberta (facilidade de achar). O risco é a média das cinco.
- **Escala**: média abaixo de 4 = baixo; de 4 a 6,9 = médio; 7 ou mais = alto (escala definida pelo professor).
- **ASVS 5.0**: lista oficial da OWASP com requisitos testáveis de segurança (por exemplo, “a consulta ao banco deve ser parametrizada”). Cada mitigação aponta para um número de requisito dessa lista.
- **CWE**: catálogo mundial de tipos de fraqueza de software, mantido pela MITRE (ex.: CWE-89 = injeção SQL).
- **Art. 46 da LGPD**: artigo que obriga a adotar medidas de segurança para proteger dados pessoais.
- **SDL / Microsoft SDL**: ciclo de desenvolvimento seguro da Microsoft, de onde vem o STRIDE.
- **Meier et al., 2003**: guia da Microsoft que descreve o DREAD.

### Slide 5: Modelo do sistema e fronteiras

[Leonardo · cerca de 1 min e 40 s · apontar para o DFD durante a explicação; se precisar, clique no diagrama para ampliá-lo e feche no × ou com Esc]

**FALA**

“Este é o nosso DFD de nível um. Ele representa como os atores interagem com a aplicação e como as informações passam pelos processos e armazenamentos.

À esquerda estão os usuários. A interface recebe suas ações e as encaminha aos processos internos.

No centro, estão as responsabilidades de autenticação, cadastro e recuperação, cesta e pedido, funções privilegiadas e arquivos. Elas aparecem separadas para análise, mas fazem parte do mesmo serviço.

À direita estão SQLite, o mapa de sessões, os pedidos, os arquivos e os registros de acesso.

As três fronteiras separam o cliente do servidor, o host do contêiner e a aplicação dos armazenamentos. Elas ajudam a identificar onde validar dados e verificar permissões.

Vamos usar agora recortes desse mesmo diagrama para acompanhar os quatro fluxos.”

**O QUE SIGNIFICA CADA CÓDIGO DESTE SLIDE (e do desenho)**

- **DFD nível 1**: o desenho detalhado, com o sistema “aberto” em partes. (O **nível 0** é o desenho de contexto, com o sistema inteiro como uma bola só, chamada **P0**.)
- **E1** Visitante (sem login) · **E2** Cliente (logado, papel customer ou deluxe) · **E3** Administrador (papel admin) · **E4** Contabilidade (papel accounting).
- **P1** Interface da loja: a parte Angular que roda **no navegador** do usuário.
- **P2** Identidade e sessão: faz o login, cria e confere o JWT.
- **P3** Cadastro e recuperação: cria conta e faz o “esqueci minha senha”.
- **P4** Cesta e pedido: cesta, checkout, registro do pedido e geração do comprovante em PDF.
- **P5** Funções privilegiadas: telas e rotas de administração e de contabilidade.
- **P6** Arquivos HTTP: entrega o conteúdo da pasta `/ftp/` pelo navegador.
- **“P2–P6”** no slide = os processos P2, P3, P4, P5 e P6. São **partes do mesmo servidor Express**, separadas só para análise.
- **D0** Estado no navegador: token e ID da cesta guardados no próprio navegador (localStorage, cookie, sessionStorage).
- **D1** SQLite: o banco principal (contas, hashes de senha, respostas de segurança, cestas, endereços, cartões, feedbacks).
- **D2** Mapa de sessões: lista em memória que liga cada JWT ao usuário.
- **D3** Pedidos: coleção de pedidos (orders) no MarsDB, em memória.
- **D4** Diretório ftp/: pasta de arquivos (exemplos e comprovantes em PDF).
- **D5** Logs de acesso: registro das requisições HTTP (feito pela biblioteca Morgan).
- **TB-01** Cliente | servidor: tudo que vem do navegador pode ter sido alterado pelo usuário; o servidor precisa conferir.
- **TB-02** Host | contêiner: a passagem do computador (Windows) para dentro da caixa Docker.
- **TB-03** Aplicação | armazenamentos: o ponto onde a requisição vira leitura ou gravação nos dados guardados (D1 a D5).
- **F01 a F46**: as setas do desenho. Cada seta é um dado indo de um lugar para outro; a lista completa está no dicionário no fim da cola.

**COMO EXPLICAR A IMAGEM DO DFD NÍVEL 1 (apontando)**

**Como todo recorte de DFD está organizado (vale para os slides 5, 7, 8, 9 e 10):** da esquerda para a direita. Na **coluna da esquerda** ficam as pessoas (caixas brancas E1 a E4) e, embaixo, o D0, o navegador. Logo depois vem a **caixa alta P1**, a interface que roda no navegador. A **primeira linha tracejada vertical, marcada TB-01**, é a fronteira cliente | servidor: tudo à esquerda dela está nas mãos do usuário. O **retângulo tracejado grande, marcado TB-02 “Contêiner Docker / labnet”**, é o que roda dentro do Docker. No meio dele fica o **processo do servidor** (caixa arredondada cinza: P2, P3, P4, P5 ou P6). A **área tracejada da direita, marcada TB-03 “Dados”**, contém os **armazenamentos**, desenhados como caixas com uma linha em cima e outra embaixo (D1 SQLite, D2 mapa de sessões etc.). Cada **seta** tem um código F e um rótulo curto do que passa por ela; a ponta mostra o sentido. O texto pequeno embaixo do desenho diz que TB-01 separa cliente e servidor, TB-02 delimita a implantação, TB-03 indica a passagem lógica aos dados, e que **armazenamentos repetidos** (por exemplo, D1 aparecendo duas vezes) **são o mesmo recurso**, só desenhado de novo para não cruzar setas.

- [Aponte a coluna da esquerda] “Aqui estão os quatro tipos de usuário: visitante, cliente, administrador e contabilidade, e o próprio navegador, o D0, onde fica guardado o token.”
- [Aponte a caixa alta P1] “Todos falam com a interface, o P1, que roda no navegador. Ela fica antes da primeira linha tracejada, a TB-01, porque está do lado do usuário.”
- [Aponte o retângulo tracejado TB-02] “Dentro do contêiner, separamos cinco responsabilidades do mesmo servidor, de cima para baixo: P2 identidade e sessão, P3 cadastro e recuperação, P4 cesta e pedido, P5 funções privilegiadas e P6 arquivos.”
- [Aponte a área TB-03 à direita] “Cada processo lê e grava nos seus armazenamentos: SQLite, mapa de sessões, pedidos, pasta ftp e logs. O SQLite aparece várias vezes, mas é um banco só.”
- [Aponte uma seta, por exemplo F09] “Cada seta tem um código, como F09, que usamos nas tabelas do relatório para rastrear a ameaça até aqui.”
- Se o desenho estiver pequeno, **clique nele** para ampliar e feche no × ou com Esc.

### Slide 6: Os quatro fluxos

[Gustavo · cerca de 40 s]

**FALA**

“Selecionamos quatro fluxos porque eles percorrem partes relevantes da aplicação e envolvem diferentes tipos de dados e permissões.

O primeiro trata do login e da sessão. O segundo cobre cadastro e recuperação de senha. O terceiro acompanha a cesta e o pedido. O quarto reúne as funções de administração e contabilidade.

Registramos 25 cenários, com pelo menos seis por fluxo e cobertura das seis categorias STRIDE. Cada integrante apresentará agora um recorte.”

**O QUE SIGNIFICA CADA CÓDIGO DESTE SLIDE**

- **FX1** Login e uso da sessão · **FX2** Cadastro e recuperação de senha · **FX3** Cesta, checkout e registro do pedido · **FX4** Administração e contabilidade. “FX” é só a sigla que demos para “fluxo”.
- **M1: V1, V2, V6** etc.: quais vulnerabilidades do Trabalho 1 aparecem naquele fluxo (veja V1 a V7 no dicionário).
- **25 cenários**: 25 ameaças registradas no total (6 em FX1, 6 em FX2, 7 em FX3, 6 em FX4).
- **Gráfico**: quantas ameaças caíram em cada letra do STRIDE. Information Disclosure (exposição de informação) tem 5; as outras cinco letras têm 4 cada.
- **Por que esses quatro**: o roteiro exige pelo menos 1 de autenticação (FX1), 1 com dados pessoais (FX2 e FX3) e 1 com função de administração ou integração externa (FX4), todos cruzando uma fronteira e, de preferência, 2 com achados do Trabalho 1 (temos 3).

**COMO EXPLICAR O GRÁFICO**

- Cada barra é uma letra do STRIDE e o número é quantas das 25 ameaças caíram nela. [Aponte a barra maior] “Information Disclosure tem cinco; as outras cinco categorias têm quatro cada. Isso mostra que cobrimos as seis categorias, que era exigência do roteiro.”

### Slide 7: FX1 · Login e sessão

[Diego · cerca de 1 min e 30 s]

**FALA SUGERIDA PARA DIEGO**

“O primeiro fluxo é o de login e uso da sessão. No diagrama, acompanhamos a entrada das credenciais pela interface, a consulta ao banco e a emissão do token que será usado nas chamadas seguintes.

A ameaça mais pontuada foi a T-FX1-02, com média nove vírgula oito. O e-mail do login era incorporado de forma insegura a uma consulta SQL, linguagem usada para consultar dados relacionais. Na M1 e na M2, uma entrada manipulada permitiu obter uma sessão de administrador sem a senha correta.

Outro problema é a exposição do hash MD5 da senha no JWT. O MD5 é uma função de resumo que não é adequada para proteger senhas. Nos tokens examinados, não identificamos o campo de expiração, chamado exp. Observamos essa ausência, mas não testamos a validade temporal em todas as rotas.

Propomos parametrizar a consulta, restringir os dados do token e usar um algoritmo adequado para senhas, como Argon2id. Depois da implementação, essas medidas devem ser testadas.”

**O QUE SIGNIFICA CADA CÓDIGO DESTE SLIDE**

- **Recorte do DFD do FX1**: o pedaço do desenho que o login usa. Caminho: **E1** visitante digita e-mail e senha em **P1** (seta **F01**) → P1 manda ao servidor **P2** pela seta **F09** (cruzando TB-01 e TB-02) → P2 consulta o banco **D1** (**F21**, volta em **F22**) → P2 registra a sessão em **D2** (**F23/F24**) e escreve o log em **D5** (**F41**) → P2 devolve o JWT para P1 (**F10**) → P1 guarda o token no navegador **D0** (**F19**, lido de volta em **F20**). **E2** é o cliente já logado usando a sessão (**F03/F04**).
- **`' OR 1=1--`**: o texto digitado no campo e-mail. O apóstrofo fecha o texto dentro da consulta SQL, o `OR 1=1` torna a condição sempre verdadeira e o `--` transforma o resto (a checagem da senha) em comentário. Resultado: entra como o primeiro usuário do banco, que é o admin.
- **T-FX1-02 · Injeção SQL · DREAD 9,8**: ameaça 02 do fluxo FX1; média DREAD 9,8 (a maior do trabalho). Notas: dano 10, reprodutibilidade 10, explorabilidade 10, usuários afetados 10, descoberta 9.
- **Confirmada no T1 (V1)**: já foi provada no Trabalho 1, achado V1, e reproduzida de novo na M2.
- **CWE-89**: “SQL Injection”, o tipo de fraqueza. **A03**: categoria “Injection” (Injeção) do OWASP Top 10 2021.
- **T-FX1-04 · Hash no JWT · 8,6**: ameaça 04 do FX1. O token carrega dentro dele o campo **`password`** com o hash MD5 da senha.
- **`password`**: nome do campo dentro do JWT que contém o hash da senha. **Hash**: “resumo” da senha; não é a senha, mas dá para tentar descobri-la testando palpites.
- **MD5**: tipo de hash antigo e rápido demais; não serve para guardar senha.
- **`exp`**: campo do JWT que diz quando ele expira. Não estava no token.
- **V2**: achado do Trabalho 1 sobre o hash no JWT.
- **CWE-522** (credenciais insuficientemente protegidas) **/ 916** (hash de senha com esforço computacional insuficiente). **A02**: “Cryptographic Failures”, falhas criptográficas.
- **Parametrizada**: a consulta ao banco é montada com “espaços reservados”; o que o usuário digita vira só dado, nunca comando.
- **Claims restritos**: o token passa a levar só o necessário (ID, papel, expiração), sem a senha.
- **Argon2id**: algoritmo moderno e lento de propósito para guardar senhas com segurança.

**COMO EXPLICAR A IMAGEM DO FX1 (apontando, da esquerda para a direita)**

- [E1 e E2, à esquerda] “O visitante, E1, digita e-mail e senha na interface pela seta F01. Depois do login ele vira o cliente, E2, que usa a sessão pelas setas F03 e F04.”
- [P1 e D0] “A interface, P1, guarda o token no navegador, o D0, pela seta F19, e lê de volta pela F20 em cada chamada.”
- [Seta F09 cruzando a TB-01] “A seta F09, credenciais, é o ponto crítico: ela cruza a fronteira TB-01 e entra no contêiner. É exatamente por ela que entra o texto da injeção SQL.”
- [P2 no meio] “O P2, identidade e sessão, recebe o e-mail e a senha.”
- [F21 e F22 até D1] “Ele consulta o banco SQLite pela F21 e recebe a conta pela F22. É na F21 que a consulta é montada de forma insegura; por isso a T-FX1-02 aponta para P2, F09 e F21.”
- [F23 e F24 até D2] “Depois grava a sessão no mapa de sessões, o D2.”
- [F41 até D5] “E registra o login no log, o D5. A T-FX1-03 diz que esse log não liga a tentativa à conta.”
- [F10 voltando para P1] “Por fim, devolve o JWT pela F10. É nessa seta que vai o hash da senha dentro do token, a T-FX1-04.”
- [Print abaixo do desenho] “Embaixo está a tela de login com o texto da injeção no campo e-mail.”

### Slide 8: FX2 · Cadastro e recuperação

[Leonardo · cerca de 1 min e 30 s]

**FALA SUGERIDA PARA LEONARDO**

“O segundo fluxo trata da criação de contas e da recuperação de senha. O visitante informa os dados de cadastro e, no processo de recuperação, utiliza uma pergunta de segurança associada à conta.

A T-FX2-06 recebeu média nove. O cadastro aceitava do cliente um campo que definia o papel da conta. Na M2, enviamos uma requisição com papel de administrador e a conta foi criada com esse papel.

O outro cenário destacado é a recuperação por resposta previsível. Na M1, a pergunta de segurança pôde ser consultada a partir do e-mail e a senha foi redefinida após uma resposta correta dentro da cota observada.

O papel inicial deve ser definido pelo servidor. Para recuperação, propomos substituir perguntas previsíveis por uma prova de posse de canal com token de uso único e prazo limitado.”

**O QUE SIGNIFICA CADA CÓDIGO DESTE SLIDE**

- **Recorte do DFD do FX2**: **E1** visitante preenche o cadastro ou o “esqueci a senha” em **P1** (**F01/F02**) → P1 envia a **P3** pela seta **F11** e recebe a resposta pela **F12** (cruzando TB-01/TB-02) → P3 grava e lê no banco **D1** (**F25/F26**: conta, pergunta, resposta de segurança e nova senha) → P3 escreve o log em **D5** (**F42**).
- **Caixa “Reprodução M2 · cadastro privilegiado”**: o teste que fizemos em 08/11/2026.
- **POST /api/Users**: o endereço (rota) do servidor que cria usuário; POST = “enviar para criar”.
- **body `{ "role": "admin" }`**: o corpo da requisição; incluímos à mão o campo `role` (papel) com o valor `admin`.
- **→ status 201 Created**: o servidor respondeu “criado com sucesso”. **→ role "admin" (id 30)**: a conta nova (número 30 no banco) foi criada já como administradora.
- **T-FX2-06 · Papel no cadastro · DREAD 9,0**: ameaça 06 do FX2; 2ª maior nota. Notas 10, 8, 9, 9, 9.
- **Mass assignment** (atribuição em massa): o servidor copia para o banco todos os campos que o cliente mandou, inclusive os que ele não deveria poder escolher, como o papel.
- **CWE-915**: modificação descontrolada de atributos de objeto (mass assignment). **A01**: “Broken Access Control”, falha de controle de acesso.
- **T-FX2-01 · Resposta previsível · 8,8**: ameaça 01 do FX2. A pergunta de segurança do usuário aparece para qualquer um que digite o e-mail, e a resposta pode ser adivinhada; no Trabalho 1 a senha foi trocada no 5º palpite.
- **V7**: achado do Trabalho 1 sobre a recuperação de senha. **Cota 100**: o servidor só limita a 100 tentativas por janela de tempo.
- **CWE-640**: mecanismo fraco de recuperação de senha. **A04**: “Insecure Design”, design inseguro — a falha é a ideia de usar pergunta de segurança, não um erro de código.
- **Token de uso único**: link ou código enviado ao e-mail verificado, que vale uma vez e expira rápido.

**COMO EXPLICAR A IMAGEM DO FX2 (apontando)**

- [E1 à esquerda] “Aqui só existe o visitante, E1, porque cadastro e recuperação são feitos sem login.”
- [F01/F02 até P1] “Ele preenche o formulário na interface.”
- [F11 cruzando a TB-01 até P3] “A interface envia o cadastro, ou o pedido de recuperação, para o P3 pela seta F11, cruzando a fronteira. É nessa seta que mandamos o campo role igual a admin, a T-FX2-06.”
- [F12 voltando] “Pela F12 volta a confirmação e, na recuperação, a pergunta de segurança da conta. É essa resposta que expõe a pergunta a qualquer um, a T-FX2-04.”
- [F25/F26 até D1] “O P3 grava a conta, a resposta de segurança e a nova senha no SQLite.”
- [F42 até D5] “E registra no log. Na recuperação, o endereço registrado inclui a query string; por isso tratamos o log como possível ponto de exposição.”
- [Caixa escura abaixo do desenho] “Embaixo está a prova da M2: POST em /api/Users com role admin, resposta 201 e a conta número 30 criada como admin.”

### Slide 9: FX3 · Cesta, checkout e pedido

[Gustavo · cerca de 1 min e 30 s]

**FALA SUGERIDA PARA GUSTAVO**

“No terceiro fluxo, acompanhamos o cliente desde a consulta da cesta até o checkout e a geração do pedido e do comprovante.

A T-FX3-06 recebeu média oito vírgula quatro. A aplicação verificava a sessão, mas permitia a um usuário consultar a cesta de outro titular informando seu identificador.

Na M2, a chamada com o token de outra conta recebeu HTTP duzentos, indicando sucesso. HTTP é o protocolo usado nas requisições web. Sem token, recebeu quatrocentos e um, indicando falta de autenticação. A autenticação estava presente, mas faltou verificar quem era o dono da cesta.

Também analisamos o diretório barra FTP. FTP significa File Transfer Protocol, ou protocolo de transferência de arquivos; aqui, esse é o nome do diretório consultado por HTTP, não uma demonstração de uso do protocolo FTP. A listagem e o download de um arquivo de exemplo foram observados na M1. Já o acesso ao comprovante de outra pessoa e o checkout com cesta alheia permaneceram hipóteses, sem confirmação específica no recorte apresentado.

A recomendação é verificar a titularidade em cada operação sobre cesta, pedido e comprovante, e disponibilizar arquivos por uma rota autorizada.”

**O QUE SIGNIFICA CADA CÓDIGO DESTE SLIDE**

- **Recorte do DFD do FX3**: **E2** cliente usa a loja em **P1** (**F03/F04**), que lê o token e o ID da cesta do navegador **D0** (**F19/F20**) → P1 chama **P4** (cesta e pedido) pela **F13** e recebe a resposta pela **F14** → P4 confere o token no mapa **D2** (**F33/F34**), lê e grava cesta, endereço e cartão em **D1** (**F27/F28**), registra o pedido em **D3** (**F29/F30**) e grava o PDF do comprovante em **D4** (**F37/F38**) → os arquivos de **D4** são servidos por **P6** (**F39/F40**) para P1 (**F17/F18**) → log em **D5** (**F43**).
- **GET /rest/basket/{A}**: pedido de leitura (GET) da cesta número A. **token de B → 200**: usando o crachá do usuário B, o servidor devolveu a cesta do usuário A com sucesso (200 = OK). **sem token → 401**: sem crachá, recusou (401 = não autenticado).
- **T-FX3-06 · Cesta de outro titular · 8,4**: ameaça 06 do FX3. **Titular** = dono do dado.
- **IDOR**: *Insecure Direct Object Reference* — trocar o número do objeto na requisição e acessar o que é de outra pessoa.
- **V3**: achado do Trabalho 1 sobre a cesta alheia. **CWE-639**: burlar autorização trocando a chave controlada pelo usuário. **A01**: falha de controle de acesso.
- **T-FX3-02** (7,8): hipótese de fazer checkout usando a cesta de outra pessoa. **T-FX3-07** (7,6): hipótese de baixar o comprovante de outra pessoa pela pasta /ftp/. Ambas **não testadas** (“hipótese”).
- **V5**: achado do Trabalho 1: a pasta **/ftp/** lista os arquivos e permite baixá-los sem login.
- **Titularidade por objeto**: em toda chamada, conferir se aquela cesta, pedido ou arquivo pertence ao usuário do token.

**COMO EXPLICAR A IMAGEM DO FX3 (apontando)**

- [E1, E2 e D0 à esquerda] “O cliente, E2, monta a cesta na interface; o ID da cesta e o token ficam no navegador, D0.”
- [F13 cruzando a TB-01 até P4] “A interface manda ao P4, cesta e pedido, os itens, o endereço, o cartão de teste e o ID da cesta pela F13. Esse ID vem do cliente: é por ele que se tenta ler a cesta de outra pessoa, a T-FX3-06.”
- [F33/F34 até D2] “O P4 consulta o mapa de sessões para saber quem é o dono do token. O problema é que ele confere a sessão, mas não confere se a cesta pedida é desse dono.”
- [F27/F28 até D1] “Lê e grava cesta, estoque, endereço e cartão no SQLite.”
- [F29/F30 até D3] “Registra o pedido na coleção de pedidos.”
- [F37/F38 até D4] “Gera o comprovante em PDF e grava na pasta ftp, o D4.”
- [P6 embaixo, com F17/F18 e F39/F40] “E aqui embaixo está o P6, que serve essa mesma pasta pelo navegador. Por isso o D4 aparece duas vezes: é a mesma pasta. Como a listagem é pública, levantamos a hipótese de baixar o comprovante de outra pessoa, a T-FX3-07.”
- [F43 até D5] “Tudo é registrado no log.”
- [Caixa escura abaixo] “Embaixo está a prova da M2: a cesta A lida com o token de B deu 200; sem token, 401.”

### Slide 10: FX4 · Administração e contabilidade

[João Victor · cerca de 1 min e 30 s]

**FALA SUGERIDA PARA JOÃO VICTOR**

“O quarto fluxo reúne operações de administração e contabilidade, que podem alcançar dados de vários usuários. No recorte do DFD, vemos os caminhos dessas chamadas até os processos e armazenamentos usados pela aplicação.

Na ameaça T-FX4-04, com média oito vírgula seis, verificamos o acesso à consulta de usuários. Na M2, uma conta com papel de cliente conseguiu chamar a rota de detalhes de autenticação e recebeu HTTP duzentos com uma listagem de contas.

A imagem mostra uma sessão administrativa; o teste com a conta de cliente foi uma requisição HTTP separada.

A T-FX4-06 trata de possível exclusão indevida de feedback. A hipótese veio da análise do código, mas a exclusão por conta sem o papel adequado não foi testada.

Como mitigação, propomos definir as permissões no servidor para cada rota, negar por padrão as ações não autorizadas e registrar as operações relevantes.”

**O QUE SIGNIFICA CADA CÓDIGO DESTE SLIDE**

- **Recorte do DFD do FX4**: **E3** administrador e **E4** contabilidade (e também **E2**, um cliente tentando o mesmo) usam **P1** (**F05 a F08**, **F03/F04**) → P1 chama **P5** (funções privilegiadas) pela **F15** e recebe pela **F16** → P5 lê contas e feedbacks em **D1** (**F31/F32**), pedidos em **D3** (**F35/F36**), sessões em **D2** (**F45/F46**) e grava log em **D5** (**F44**).
- **T-FX4-04 · Consulta de usuários · 8,6**: ameaça 04 do FX4. Uma conta comum (**customer**) chamou a rota `authentication-details` e recebeu **HTTP 200** com a lista de contas. Reproduzida na M2 (08/11/2026).
- **A01**: falha de controle de acesso.
- **T-FX4-06 · Exclusão de feedback · 8,2**: ameaça 06 do FX4. **DELETE** é o método HTTP de apagar; pelo código, a rota que apaga comentários (feedbacks) parece exigir só estar logado, não ser admin. **Não testamos.**
- **Print do painel**: tela de administração vista com login de admin (é ilustração, não a prova do teste com customer).
- **Matriz de papéis no servidor**: tabela “qual papel pode chamar qual rota”, conferida no servidor. **Negar por padrão**: se não está explicitamente permitido, é proibido.

**COMO EXPLICAR A IMAGEM DO FX4 E O PRINT (apontando)**

- [Desenho pequeno no alto; clique para ampliar] “Aqui aparecem o administrador, E3, a contabilidade, E4, e também o cliente comum, E2, porque a ameaça principal é justamente um cliente chamando funções que deveriam ser só do admin.”
- [F15 cruzando a TB-01 até P5] “Todas as chamadas chegam ao P5, funções privilegiadas, pela F15, com o JWT.”
- [F31/F32 até D1] “O P5 lê contas e feedbacks e apaga feedbacks no SQLite. Apagar feedback é a T-FX4-06.”
- [F45/F46 até D2] “Para montar a lista de usuários, consulta a sessão de cada conta no mapa de sessões.”
- [F16 voltando] “E devolve a lista pela F16. É essa seta que expõe os dados das contas a quem não é admin, a T-FX4-04.”
- [F35/F36 até D3] “A contabilidade consulta pedidos e muda o estado de entrega na coleção de pedidos, a T-FX4-02.”
- [F44 até D5] “Tudo vai para o log; a T-FX4-03 trata de alguém negar uma ação administrativa.”
- [Print à direita] “O print é o painel de administração, com a lista de usuários registrados e os feedbacks. Ele foi tirado com a conta admin; a consulta pela conta comum foi testada à parte, por HTTP, e retornou 200.”

### Slide 11: Top 10 por DREAD

[Gustavo · cerca de 1 min]

**FALA**

“Neste slide, reunimos as dez ameaças com maior média DREAD. A pontuação considera dano, reprodutibilidade, facilidade de exploração, usuários afetados e facilidade de descoberta.

No conjunto dos 25 cenários, 18 ficaram na faixa de risco alto e sete na faixa de risco médio. A injeção de SQL no login aparece em primeiro lugar, com nove vírgula oito. Em seguida, vem a atribuição de papel privilegiado no cadastro, com nove.

Essa classificação ordena as ações no nosso recorte e depende das justificativas da equipe. Ela não mede a frequência real de incidentes.

O gráfico reúne resultados observados e hipóteses; por isso, é necessário considerar o estado de validação de cada ameaça.”

**O QUE SIGNIFICA CADA CÓDIGO DESTE SLIDE (o ranking, em português)**

1. **T-FX1-02** (9,8) Injeção SQL no login dá acesso de admin — confirmada no T1 (V1) e na M2.
2. **T-FX2-06** (9,0) Cadastro aceita papel de admin enviado pelo cliente — reproduzida na M2.
3. **T-FX2-01** (8,8) Recuperação de senha por resposta previsível — confirmada no T1 (V7).
4. **T-FX1-04** (8,6) Hash da senha dentro do JWT — confirmada no T1 (V2).
5. **T-FX4-04** (8,6) Conta comum consegue listar usuários — reproduzida na M2.
6. **T-FX3-06** (8,4) Leitura da cesta de outra pessoa — confirmada no T1 (V3).
7. **T-FX1-06** (8,4) Chave que assina os JWT está no código público; quem a tiver pode fabricar token de admin — hipótese.
8. **T-FX4-06** (8,2) Apagar feedback sem ser admin — hipótese.
9. **T-FX1-01** (8,0) Tentativas ilimitadas de senha (força bruta) — confirmada no T1 (V6).
10. **T-FX3-02** (7,8) Fechar compra com cesta de outra pessoa — hipótese.

- **Empates** (8,6 e 8,4): desempata quem foi confirmado em teste, depois o maior dano, depois o ID.
- **“7 apoiados em achados da M1”**: T-FX1-01, T-FX1-02, T-FX1-04, T-FX2-01, T-FX2-04, T-FX3-04 e T-FX3-06.

**COMO EXPLICAR O GRÁFICO**

- Cada barra é uma ameaça; o número no fim é a média DREAD; embaixo do código está o nome curto. [Aponte as duas primeiras] “As duas primeiras são as que corrigiríamos primeiro: a injeção no login e o papel no cadastro.” [Aponte os empates de 8,6 e 8,4] “Onde há empate, fica na frente a que foi confirmada em teste.”

### Slide 12: Plano de mitigação

[João Victor · cerca de 1 min]

**FALA**

“Organizamos o tratamento em seis frentes para que ameaças relacionadas possam ser corrigidas e verificadas em conjunto.

Na primeira frente estão a consulta parametrizada, a redução dos dados no JWT e a proteção das senhas. Na segunda, o servidor deve definir o papel da conta e a recuperação deve usar um mecanismo de uso único.

Na terceira frente, aplicamos autorização por função e por titularidade às rotas e aos recursos. As demais tratam do ciclo de vida da sessão, da contenção de abuso, da integridade dos pedidos, da auditoria e das respostas de erro.

Cada frente tem um critério de aceite. Por exemplo: a entrada usada na injeção deve falhar; o token não deve conter o hash; e duas contas de teste não devem conseguir acessar os recursos uma da outra.

Essas medidas são propostas. Sua eficácia precisa ser verificada depois que forem implementadas.”

**O QUE SIGNIFICA CADA CÓDIGO DESTE SLIDE**

- **Frente**: grupo de correções que resolvem ameaças parecidas juntas.
- **Rotação de chave**: trocar a chave que assina os JWT e invalidar os tokens antigos (trata T-FX1-06).
- **“Aceite: V1 falha”**: depois de corrigir, repetir o ataque do achado V1 (`' OR 1=1--`) e ele tem de falhar.
- **“Cadastro força customer”**: toda conta nova nasce como cliente comum, não importa o que o cliente mande.
- **P4 e P5**: os processos de cesta/pedido e de funções privilegiadas (veja o slide 5).
- **Expiração efetiva e revogação**: o token tem de vencer e poder ser cancelado (por exemplo, no logout).
- **Idempotência**: mandar o mesmo pedido duas vezes não cria dois pedidos. **Versão**: detectar quando dois alteram o mesmo pedido ao mesmo tempo.
- **Quota de PDF e paginação**: limite de comprovantes gerados e listas entregues em páginas, não tudo de uma vez.
- **Trilha protegida e correlacionável**: log que ninguém da aplicação consegue apagar e que liga quem, quando, o quê e de onde.
- **Critério de aceite**: o teste objetivo que prova que a correção funcionou.

### Slide 13: Validação no laboratório

[João Victor · cerca de 50 s]

**FALA**

“Para conferir parte da análise, realizamos uma execução complementar no laboratório em oito de novembro de dois mil e vinte e seis, utilizando a versão definida para o trabalho.

Reproduzimos a injeção no login, observamos o hash no JWT, criamos uma conta com papel administrativo pelo cadastro e consultamos a cesta de outro titular. Também verificamos a listagem anônima do diretório FTP e a consulta de usuários por uma conta de cliente.

Esses resultados complementam a M1, mas não confirmam todos os cenários do modelo.

Os ensaios foram de baixo volume. Não medimos esgotamento de recursos, concorrência nem o comportamento temporal dos tokens em todas as rotas. Essas limitações foram registradas no relatório e devem ser consideradas ao interpretar os resultados.”

**O QUE SIGNIFICA CADA CÓDIGO DESTE SLIDE**

- **digest sha256:8739…526b**: a “impressão digital” da imagem Docker usada; prova que é a mesma versão do Trabalho 1.
- **Sessão admin (id 1)**: a injeção logou na conta número 1 do banco, que é o administrador.
- **Cadastro role:admin aceito**: o teste da T-FX2-06 (slide 8).
- **(200) · sem token (401)**: com token de outra conta, deu certo (200); sem token, foi recusado (401).
- **Listagem anônima de /ftp/**: a pasta /ftp/ mostrou os arquivos sem login.
- **Reset com cota 100**: a recuperação de senha permite até 100 tentativas por janela.
- **Baixo volume**: poucas requisições; não fizemos teste de carga (negação de serviço).
- **Prints**: clique em cada print para ampliá-lo; feche no × ou com Esc.

**COMO EXPLICAR OS DOIS PRINTS (apontando)**

- [Print de cima] “Este é o Juice Shop depois da injeção SQL: no menu da conta aparece o e-mail do administrador, admin@juice-sh.op, e as faixas verdes são o próprio Juice Shop avisando que os desafios foram resolvidos: Login Admin, que é a injeção; Admin Registration, que é o cadastro como admin; e View Basket, que é a cesta de outra pessoa.”
- [Print de baixo] “Este é o diretório /ftp/ aberto sem login, listando os arquivos. Ele confirma a listagem pública, mas não prova o acesso a um comprovante de outra pessoa.”
- Clique em cada print para ampliá-lo.

### Slide 14: Conclusão e lições

[João Victor · cerca de 40 s]

**FALA**

“A modelagem mostrou que as falhas aparecem em momentos diferentes do funcionamento da aplicação. A injeção afeta a consulta que determina a identidade; o hash é exposto na composição do token; e a leitura de uma cesta alheia acontece depois que uma sessão já foi aceita.

Por isso, as prioridades incluem corrigir a autenticação, limitar os dados do token, impedir a atribuição de papéis pelo cadastro público e verificar a autorização em cada recurso.

A principal lição para a equipe foi que reconhecer uma sessão não é suficiente para demonstrar que o usuário tem direito a uma cesta, a um pedido ou a um arquivo específico.

O modelo complementa a M1 e oferece um plano para orientar as próximas correções.”

**O QUE SIGNIFICA O QUE APARECE NESTE SLIDE**

- **Autenticação**: provar **quem** você é (login). **Autorização**: verificar **o que** você pode fazer ou acessar. A lição central do trabalho é que ter a primeira não garante a segunda.
- **Resultados observados × hipóteses**: separamos o que foi testado de verdade (M1 e M2) do que é cenário plausível ainda não testado.

### Slide 15: Obrigado

[João Victor · cerca de 10 s]

**FALA**

“Com isso, concluímos a apresentação. O relatório, o modelo e os materiais de apoio estão no repositório indicado no slide.

Obrigado pela atenção. Ficamos à disposição para responder às perguntas.”

**O QUE SIGNIFICA O QUE APARECE NESTE SLIDE**

- **.drawio/JSON**: os arquivos-fonte do modelo. `DFD_Juice_Shop.drawio` abre no diagrams.net; `modelo_ameacas.json` tem todos os elementos, setas, ameaças e notas. Ficam na pasta `trabalho2/modelo/` do repositório **github.com/diegof-d2fs/m2-seg-system-diegos**.

## Apêndice · Ferramentas usadas e como responder

#### Resposta curta para “qual ferramenta vocês usaram para gerar os fluxos / os diagramas?”

“Usamos o draw.io, que hoje se chama diagrams.net, com o modelo descrito como código. Todas as peças do diagrama, as setas, as fronteiras e as ameaças estão num arquivo JSON, o modelo_ameacas.json. Um script em Python, o gerar_diagramas.py, lê esse JSON e gera o arquivo DFD_Juice_Shop.drawio, que abre e edita no draw.io, e as imagens SVG que estão no relatório e nos slides. Assim o desenho e as tabelas do relatório saem da mesma fonte e os IDs nunca ficam diferentes. O roteiro aceita tanto o draw.io quanto modelo de ameaças como código, e o arquivo-fonte está no repositório, na pasta trabalho2/modelo.”

#### Lista completa, se perguntarem detalhes

- **Diagramas (DFD)**: draw.io / diagrams.net (arquivo `DFD_Juice_Shop.drawio`, com 6 páginas: nível 0, nível 1 e os recortes FX1 a FX4), gerado pelo script `gerar_diagramas.py` a partir do `modelo_ameacas.json`. As imagens são SVG (vetoriais, não perdem qualidade ao ampliar).
- **Identificação das ameaças (STRIDE)**: feita **pela equipe, à mão**, elemento por elemento, seguindo a tabela STRIDE por elemento. **Não** usamos sugestão automática de ameaças (Threat Dragon ou Microsoft Threat Modeling Tool). O próprio roteiro alerta que a sugestão automática gera ameaças genéricas.
- **Validação do modelo**: script `validar_modelo.py`. Confere se todo ID existe, se cada fluxo cobre as seis categorias, se cada N/A tem justificativa, recalcula as médias DREAD e gera o ranking `priorizacao.csv`.
- **Observação do laboratório**: Juice Shop v20.2.0 no Docker Desktop, em 127.0.0.1:3000; navegador com DevTools (aba Network) para ver as requisições; na M1, proxy e sqlmap para confirmar a injeção.
- **Testes HTTP da M2 (08/11/2026)**: script `testar_laboratorio.py`, em Python, que faz poucas requisições só para a instância local e grava os resultados em `REVISAO_HTTP_FINAL.json`.
- **Relatório**: montado em Word (DOCX) com o script `gerar_relatorio.py` (biblioteca python-docx), usando o Inkscape para converter os SVG em imagem; depois exportado para PDF.
- **Slides e cola**: página HTML própria publicada no GitHub Pages; os gráficos são desenhados direto no código (SVG).

#### Perguntas de acompanhamento

- **“Por que não usaram o OWASP Threat Dragon, que é o recomendado?”** — “O roteiro deixa a ferramenta livre e recomenda o Threat Dragon. Preferimos o draw.io com modelo como código porque o mesmo arquivo alimenta o diagrama, as fichas, as matrizes e o ranking, então o ID de uma seta no desenho nunca fica diferente do ID na tabela. E como fizemos o STRIDE à mão, não precisávamos do motor de sugestão do Threat Dragon.”
- **“O diagrama foi feito à mão ou gerado?”** — “Os elementos, as setas e as fronteiras foram definidos pela equipe no JSON, a partir da leitura do código do Juice Shop e da observação no navegador. O script só desenha o que está no JSON; a decisão de quais peças existem e onde ficam as fronteiras é nossa.”
- **“Dá para abrir e editar?”** — “Sim. O DFD_Juice_Shop.drawio abre no site diagrams.net ou no aplicativo do draw.io; as 6 páginas aparecem como abas.” [Se pedirem, abra app.diagrams.net e o arquivo da pasta trabalho2/modelo.]
- **“O que é modelo de ameaças como código?”** — “É descrever o sistema e as ameaças num arquivo de texto estruturado, em vez de só desenhar. Ferramentas como o OWASP pytm e o Threagile fazem isso; nós fizemos algo parecido com JSON e Python. A vantagem é poder versionar no Git e validar automaticamente.”

## Apêndice · Dicionário completo de códigos

Use este apêndice se alguém perguntar sobre qualquer código que aparece no relatório ou nos desenhos.

#### Entidades externas (E): quem está fora do sistema

- **E1 Visitante**: pessoa sem login. Cadastra, recupera senha, faz login e vê o catálogo.
- **E2 Cliente**: pessoa logada com papel customer ou deluxe. Usa a própria cesta e compra.
- **E3 Administrador**: papel admin. Consulta contas e modera (apaga) feedbacks.
- **E4 Contabilidade**: papel accounting. Consulta pedidos e altera o estado de entrega.

#### Processos (P): partes do programa

- **P0 OWASP Juice Shop**: o sistema inteiro visto como uma bola só (só no DFD de nível 0).
- **P1 Interface da loja**: Angular, roda no navegador.
- **P2 Identidade e sessão**: login, emissão e conferência do JWT.
- **P3 Cadastro e recuperação**: criação de conta e “esqueci a senha”.
- **P4 Cesta e pedido**: cesta, checkout, pedido e comprovante PDF.
- **P5 Funções privilegiadas**: administração e contabilidade.
- **P6 Arquivos HTTP**: entrega da pasta /ftp/.

#### Armazenamentos (D): onde os dados ficam guardados

- **D0 Estado no navegador**: token, cookie e ID da cesta no navegador do usuário.
- **D1 SQLite**: banco principal (contas, hashes, respostas de segurança, cestas, endereços, cartões, estoque, feedbacks).
- **D2 Mapa de sessões**: memória do servidor que liga cada JWT ao usuário.
- **D3 Pedidos**: coleção orders no MarsDB, em memória.
- **D4 Diretório ftp/**: arquivos de exemplo e comprovantes.
- **D5 Logs de acesso**: registro HTTP (Morgan), rotação diária.

#### Fronteiras de confiança (TB)

- **TB-01 Cliente | servidor**: o que vem do navegador não é confiável e precisa ser validado e autorizado no servidor. Cruzada pelas setas F09 a F18.
- **TB-02 Host | contêiner**: do Windows para dentro do Docker; publicação só em 127.0.0.1. Cruzada pelas mesmas setas F09 a F18, vistas pelo lado da rede.
- **TB-03 Aplicação | armazenamentos**: da lógica da aplicação para os dados guardados. Cruzada pelas setas F21 a F46. É uma separação lógica (D2 e D3 estão na memória do próprio processo).

#### Fluxos de dados (F): as setas do desenho

- **F01 / F02**: visitante ↔ interface (formulários e respostas públicas).
- **F03 / F04**: cliente ↔ interface (ações de sessão, cesta e compra; perfil e comprovante).
- **F05 / F06**: administrador ↔ interface (consulta de usuários, moderação).
- **F07 / F08**: contabilidade ↔ interface (pedidos e estado de entrega).
- **F09 / F10**: interface ↔ P2 (e-mail e senha ou token; volta o JWT e o perfil).
- **F11 / F12**: interface ↔ P3 (cadastro, resposta de segurança, nova senha; volta a pergunta e o resultado).
- **F13 / F14**: interface ↔ P4 (itens, endereço, cartão de teste; volta cesta e confirmação).
- **F15 / F16**: interface ↔ P5 (comandos de admin e contabilidade; volta contas, feedbacks, pedidos).
- **F17 / F18**: interface ↔ P6 (nome do arquivo; volta a listagem ou o arquivo).
- **F19 / F20**: interface ↔ D0 (grava e lê o token e o ID da cesta no navegador).
- **F21 / F22**: P2 ↔ D1 (busca da conta para o login).
- **F23 / F24**: P2 ↔ D2 (grava e consulta a sessão).
- **F25 / F26**: P3 ↔ D1 (grava conta, pergunta, resposta e nova senha).
- **F27 / F28**: P4 ↔ D1 (cesta, estoque, endereços, cartões, carteira).
- **F29 / F30**: P4 ↔ D3 (registra e consulta pedidos).
- **F31 / F32**: P5 ↔ D1 (consulta contas e feedbacks; apaga feedback).
- **F33 / F34**: P4 ↔ D2 (descobre o dono do token e a cesta).
- **F35 / F36**: P5 ↔ D3 (consulta pedidos e altera a entrega).
- **F37 / F38**: P4 ↔ D4 (grava o PDF do comprovante).
- **F39 / F40**: P6 ↔ D4 (lê a pasta e os arquivos).
- **F41, F42, F43, F44**: P2, P3, P4 e P5 escrevendo no log D5.
- **F45 / F46**: P5 ↔ D2 (consulta a sessão de cada conta listada).

#### Fluxos de negócio (FX)

- **FX1 Login e uso da sessão** (responsável Diego, revisor Gustavo): E1, E2, P1, P2, D0, D1, D2, D5.
- **FX2 Cadastro e recuperação de senha** (responsável Leonardo, revisor Diego): E1, P1, P3, D1, D5.
- **FX3 Cesta, checkout e registro do pedido** (responsável Gustavo, revisor João Victor): E1, E2, P1, P4, P6, D0 a D5.
- **FX4 Administração e contabilidade** (responsável João Victor, revisor Leonardo): E2, E3, E4, P1, P5, D0, D1, D2, D3, D5.
- **FX5 Login com Google (OAuth)**: candidato reserva, não analisado.

#### As 25 ameaças (T-FXn-nn) em português simples

Formato do código: **T** de *threat* (ameaça) · **FX1** = fluxo · **-02** = número da ameaça dentro do fluxo. A letra entre parênteses é a categoria STRIDE; depois vem a nota DREAD e o estado.

- **T-FX1-01** (S, 8,0, confirmada V6): tentar senhas sem limite até acertar e entrar como a vítima.
- **T-FX1-02** (T, 9,8, confirmada V1): injeção SQL no e-mail do login altera a consulta e dá sessão de admin.
- **T-FX1-03** (R, 6,4, hipótese): o log não liga tentativa, conta e sessão; alguém pode negar que fez login.
- **T-FX1-04** (I, 8,6, confirmada V2): o JWT carrega o hash MD5 da senha, legível por quem tem o token.
- **T-FX1-05** (D, 6,8, hipótese): logins repetidos enchem o mapa de sessões D2 e deixam o servidor lento.
- **T-FX1-06** (E, 8,4, hipótese): a chave de assinatura está no código; quem a tem pode fabricar um token de admin.
- **T-FX2-01** (S, 8,8, confirmada V7): adivinhar a resposta de segurança e trocar a senha de outra pessoa.
- **T-FX2-02** (T, 6,6, hipótese): associar a resposta de segurança a uma conta que não é sua, trocando o ID.
- **T-FX2-03** (R, 6,6, hipótese): a pessoa nega ter trocado a senha, porque o log não prova quem foi.
- **T-FX2-04** (I, 7,6, confirmada V7): qualquer um vê a pergunta de segurança de um e-mail (e descobre que a conta existe).
- **T-FX2-05** (D, 7,0, hipótese): cadastros automáticos em massa consomem recursos.
- **T-FX2-06** (E, 9,0, reproduzida na M2): mandar `role: admin` no cadastro e nascer administrador.
- **T-FX3-01** (S, 7,0, hipótese): reutilizar a sessão roubada de um comprador.
- **T-FX3-02** (T, 7,8, hipótese): fechar compra com a cesta ou referências de outra pessoa.
- **T-FX3-03** (R, 6,4, hipótese): o cliente nega ter feito o pedido; faltam eventos ligados entre si.
- **T-FX3-04** (I, 7,2, confirmada V4): mensagem de erro mostra detalhes internos do servidor.
- **T-FX3-05** (D, 7,0, hipótese): gerar muitos comprovantes PDF e encher disco ou CPU.
- **T-FX3-06** (E, 8,4, confirmada V3): ler a cesta de outro titular trocando o número (IDOR).
- **T-FX3-07** (I, 7,6, hipótese apoiada em V5): baixar o comprovante de outra pessoa pela pasta /ftp/.
- **T-FX4-01** (S, 7,0, hipótese): funcionário de contabilidade desligado continua usando a sessão antiga.
- **T-FX4-02** (T, 7,2, hipótese): alterar o estado de entrega confiando em dado mandado pelo cliente.
- **T-FX4-03** (R, 6,8, hipótese): admin ou contabilidade nega ter apagado feedback ou mudado entrega.
- **T-FX4-04** (I, 8,6, reproduzida na M2): conta comum lista os usuários do sistema.
- **T-FX4-05** (D, 6,8, hipótese): listagens administrativas sem limite de tamanho travam o servidor.
- **T-FX4-06** (E, 8,2, hipótese): apagar feedback de outras pessoas sem ser admin.

#### Achados do Trabalho 1 (V1 a V7)

- **V1** (A03, CVSS 9,1): injeção SQL no login, entrada como admin → T-FX1-02.
- **V2** (A02, CVSS 6,5): hash MD5 da senha dentro do JWT → T-FX1-04.
- **V3** (A01, CVSS 6,5): cesta da conta A lida com o token da conta B → T-FX3-06.
- **V4** (A05, CVSS 5,3): erro detalhado na rota de cesta → T-FX3-04.
- **V5** (A05, CVSS 7,5): listagem e download anônimos em /ftp/ → apoia T-FX3-07.
- **V6** (A07, CVSS 6,5): 15 senhas erradas seguidas sem bloqueio → T-FX1-01.
- **V7** (A04, CVSS 9,1): pergunta de segurança consultável e senha trocada no 5º palpite → T-FX2-01 e T-FX2-04.
- **Atenção**: no questionário de cada fluxo, **V1, V2 e V3** também são as perguntas de fechamento (“qual a ameaça mais grave”, “o que foi confirmado”, “quais limitações”). É outra coisa, mesma sigla.
- **CVSS**: nota de gravidade usada no Trabalho 1 (0 a 10). Não comparar com DREAD.

#### Estados das ameaças

- **Confirmada no T1**: o ataque foi feito e registrado no Trabalho 1.
- **Reproduzida na M2**: começou como hipótese e testamos em 08/11/2026 (T-FX2-06 e T-FX4-04). No registro aparece “Plausível na análise inicial; reproduzida na M2”.
- **Plausível**: cenário coerente com o código e o desenho, mas não testado.

#### OWASP Top 10 2021 (códigos A0x)

- **A01** Broken Access Control: falha de controle de acesso (acessar o que não deveria).
- **A02** Cryptographic Failures: falhas de criptografia (hash fraco, chave exposta).
- **A03** Injection: injeção (SQL injection e parecidos).
- **A04** Insecure Design: design inseguro (a ideia em si é ruim, como pergunta de segurança).
- **A05** Security Misconfiguration: configuração errada (erro detalhado, pasta listável).
- **A07** Identification and Authentication Failures: falhas de identificação e autenticação (força bruta, sessão que não expira).
- **A09** Security Logging and Monitoring Failures: falhas de log e monitoramento (não dá para provar quem fez).

#### CWE usados (tipo de fraqueza)

- **CWE-89** injeção SQL · **CWE-200** exposição de informação sensível · **CWE-203** diferença observável na resposta (revela se a conta existe) · **CWE-209** mensagem de erro com informação sensível · **CWE-269** gestão imprópria de privilégios · **CWE-285** autorização imprópria · **CWE-294** reaproveitamento de credencial capturada (replay).
- **CWE-307** tentativas de autenticação sem limite · **CWE-321** chave criptográfica fixa no código · **CWE-345** autenticidade dos dados não verificada · **CWE-400** consumo de recursos sem controle · **CWE-522** credenciais mal protegidas · **CWE-548** listagem de diretório · **CWE-598** dado sensível na URL (GET).
- **CWE-613** sessão que não expira direito · **CWE-639** acesso a objeto trocando a chave (IDOR) · **CWE-640** recuperação de senha fraca · **CWE-778** log insuficiente · **CWE-841** fluxo de negócio não imposto · **CWE-862** falta de verificação de autorização · **CWE-915** mass assignment · **CWE-916** hash de senha fraco.

#### Outros códigos do relatório

- **N/A 1 a N/A 13**: justificativas padronizadas de “não aplicável” na matriz STRIDE (ex.: N/A 13 = “é só a ação do usuário no próprio navegador; o efeito é analisado na chamada ao servidor”).
- **ASVS 5.0.0: 6.1.1, 1.2.4…**: número do requisito do padrão ASVS ligado a cada mitigação.
- **Guias AUTH, SQLI, LOG, PASS, SESS, SECRETS, RESET, AUTHZ, MASS, ERR, IDOR, DOS, BIZ**: apelidos das OWASP Cheat Sheets (guias práticos) de autenticação, injeção SQL, log, armazenamento de senha, sessão, segredos, recuperação de senha, autorização, mass assignment, tratamento de erro, IDOR, negação de serviço e lógica de negócio.
- **Preventivo / detectivo / corretivo**: o controle impede, detecta ou conserta. **Camada**: aplicação, dados, rede ou processo/organização.
- **Mitigar / aceitar / transferir / eliminar**: as quatro decisões de tratamento previstas pelo roteiro.
- **HMAC**: resumo com chave usado para guardar a resposta de segurança. **TOTP**: código de 6 dígitos que muda a cada 30 s (segundo fator); existe no código mas ficou fora do recorte.
- **Quadro de participação e revisor**: cada fluxo teve um responsável e um revisor de outro integrante (lista em “Fluxos de negócio” acima).

## Apêndice · Siglas e conceitos para consulta

Este glossário é apoio para entender e responder perguntas; não precisa ser lido inteiro durante a apresentação. As primeiras ocorrências essenciais já são explicadas nas falas.

- **UNIVALI**: Universidade do Vale do Itajaí. **SC**: Santa Catarina, estado em que está situada a instituição.
- **OWASP**: *Open Worldwide Application Security Project*, comunidade que produz materiais abertos sobre segurança de aplicações. **OWASP Top 10**: lista de categorias de riscos relevantes para aplicações web, usada aqui na edição de 2021; não é uma pontuação da gravidade de cada falha.
- **DFD**: *Data Flow Diagram*, ou diagrama de fluxo de dados. Representa entidades externas, processos, armazenamentos, transferências e fronteiras de confiança.
- **STRIDE**: método de identificação de ameaças. Suas letras significam **S**, *Spoofing*, falsificação de identidade; **T**, *Tampering*, adulteração; **R**, *Repudiation*, repúdio de uma ação; **I**, *Information Disclosure*, exposição de informação; **D**, *Denial of Service*, negação de serviço; e **E**, *Elevation of Privilege*, elevação de privilégio.
- **Propriedade violada por cada letra**: S → autenticidade; T → integridade; R → não repúdio; I → confidencialidade; D → disponibilidade; E → autorização.
- **DREAD**: dimensões de priorização: **D**, *Damage*, dano; **R**, *Reproducibility*, reprodutibilidade; **E**, *Exploitability*, explorabilidade; **A**, *Affected Users*, usuários afetados; e **D**, *Discoverability*, facilidade de descoberta. A média utilizada é uma avaliação documentada da equipe, não uma medição automática.
- **ASVS**: *Application Security Verification Standard*, padrão da OWASP com requisitos para verificar controles de segurança de aplicações.
- **CWE**: *Common Weakness Enumeration*, catálogo de tipos de fraqueza de software. **MITRE**: organização que mantém o catálogo CWE; seu nome é usado como nome próprio.
- **LGPD**: Lei Geral de Proteção de Dados Pessoais, Lei nº 13.709/2018. O artigo 46 trata de medidas de segurança para proteger dados pessoais; a lei também contém outros princípios e obrigações. O art. 6º, III é o princípio da necessidade (minimização: não guardar nem trafegar dado além do necessário). O art. 5º, II define dado pessoal sensível (saúde, religião, biometria etc.); não encontramos dado sensível nos fluxos analisados.
- **CVSS**: *Common Vulnerability Scoring System*, sistema de pontuação da severidade de vulnerabilidades, utilizado na M1. A pontuação DREAD da M2 usa outro método e não deve ser apresentada como CVSS.
- **API**: *Application Programming Interface*, interface de programação que define como componentes trocam solicitações e respostas. Na aplicação, a interface web chama rotas da API.
- **SPA**: *Single-Page Application*, aplicação de página única que atualiza partes da interface no navegador sem carregar uma nova página completa a cada interação. **UI**: *User Interface*, interface com que a pessoa interage.
- **HTTP**: *Hypertext Transfer Protocol*, protocolo para requisições e respostas na web. **HTTPS**: HTTP protegido por TLS. **TLS**: *Transport Layer Security*, protocolo que protege a comunicação em trânsito quando configurado corretamente. No ensaio, a aplicação foi acessada por HTTP local.
- **JWT**: *JSON Web Token*, formato de token com informações chamadas *claims*. Sua assinatura ajuda a detectar alterações quando verificada; ela não torna o conteúdo secreto nem substitui a autorização por recurso. **JSON**: *JavaScript Object Notation*, formato textual de representação de dados.
- **RS256**: algoritmo de assinatura que combina RSA com SHA-256. **RSA**: algoritmo de criptografia assimétrica utilizado aqui para assinatura. **SHA-256**: função de resumo da família Secure Hash Algorithm. Assinar um JWT não equivale a cifrar seu conteúdo.
- **SQL**: *Structured Query Language*, linguagem para consultar e manipular dados relacionais. **SQL injection**: manipulação da estrutura de uma consulta quando entradas são incorporadas de modo inseguro.
- **SQLite**: banco de dados relacional embarcado utilizado pela aplicação. O nome combina SQL com “lite”; não é preciso inventar uma expansão para o nome completo. **MarsDB**: biblioteca usada no recorte para a coleção de pedidos em memória; o nome é próprio, não foi tratado como sigla no trabalho.
- **Sequelize**: biblioteca que o Juice Shop usa para falar com o SQLite (ORM). A consulta do login foi escrita “na mão”, sem a proteção do Sequelize, por isso a injeção funciona.
- **MD5**: *Message Digest Algorithm 5*, função de resumo antiga e rápida, inadequada para armazenamento seguro de senhas. **Argon2id**: algoritmo de derivação de chaves que pode ser usado para armazenar senhas com sal e custo configurável; é uma mitigação proposta, não implementada no ensaio. **Sal**: valor aleatório misturado a cada senha antes do hash, para que senhas iguais não tenham hashes iguais.
- **IDOR**: *Insecure Direct Object Reference*, referência direta insegura a objeto. Ocorre quando uma aplicação permite acessar ou alterar um objeto identificado pelo cliente sem a autorização necessária. **ID** significa *identifier*, identificador.
- **FTP**: *File Transfer Protocol*, protocolo de transferência de arquivos. No trabalho, `/ftp/` é o nome de um diretório acessado por HTTP; o teste não demonstra uso do protocolo FTP.
- **PDF**: *Portable Document Format*, formato de documento portátil. **SVG**: *Scalable Vector Graphics*, formato de imagem vetorial escalável usado nos diagramas dos slides.
- **POST**, **GET** e **DELETE**: métodos HTTP para enviar uma solicitação de criação ou processamento, consultar um recurso e solicitar sua exclusão, respectivamente. Seu significado geral não garante que uma aplicação os implemente com segurança.
- **HTTP 200**, **201** e **401**: códigos que indicam, respectivamente, uma resposta bem-sucedida, criação de recurso e ausência ou falha de autenticação válida. O significado do teste depende também da rota, do papel e do corpo da resposta.
- **M1/T1** e **M2**: identificam, neste trabalho, o primeiro e o segundo trabalhos ou momentos de avaliação. **C1 a C10**: códigos das questões de contextualização do sistema e do ambiente. **V1 a V7**: identificadores de achados da M1; os itens V1 a V3 do questionário de fechamento têm outro significado e não devem ser confundidos com esses achados.
- **FX1 a FX4**: códigos dos quatro fluxos selecionados. **T-FX1-02**, por exemplo, identifica uma ameaça, o fluxo ao qual pertence e seu número sequencial. **E** identifica entidade externa; **P**, processo; **D**, armazenamento; **F**, transferência de dados; e **TB**, *Trust Boundary*, fronteira de confiança. O significado exato de cada número está no apêndice “Dicionário completo de códigos”.
- **A01, A02, A03, A04, A05, A07 e A09**: códigos do OWASP Top 10:2021 (lista completa no dicionário). Não são notas DREAD nem identificadores CWE. **N/A** significa não aplicável, com justificativa registrada na matriz.
- **SDL**: *Security Development Lifecycle*, abordagem que incorpora atividades de segurança ao desenvolvimento. **SSDLC**: ciclo de desenvolvimento seguro em geral. **SSDF**: *Secure Software Development Framework*, guia do NIST (SP 800-218) citado na fundamentação. **NIST**: instituto de padrões dos EUA. **IEEE**: *Institute of Electrical and Electronics Engineers*, entidade profissional e editora técnica; **TSE**: *Transactions on Software Engineering*, periódico citado. **MSDN**: *Microsoft Developer Network*, rede de materiais para desenvolvedores da Microsoft, mencionada na referência de STRIDE.
- **Superfície de ataque**: todos os pontos por onde alguém pode interagir com o sistema (rotas, campos, cabeçalhos, cookies, arquivos).
- **Risco residual**: o risco que sobra mesmo depois de aplicar as correções (por exemplo, senha já vazada antes da correção).

## Apêndice · Perguntas que o professor pode fazer

As respostas abaixo estão escritas para serem faladas. Quando a pergunta for sobre um teste não realizado, a resposta preserva essa limitação. As perguntas 21 a 38 cobrem os pontos mais prováveis de questionamento técnico.

#### 1. Qual a diferença entre o Trabalho 1 e o Trabalho 2?

“Na M1, observamos vulnerabilidades por meio de testes na aplicação. Na M2, representamos os caminhos internos dos dados e modelamos ameaças por elemento, aproveitando evidências da M1 e uma reprodução complementar. A modelagem também inclui hipóteses que ainda precisam de testes próprios.”

#### 2. Por que começar pelo DFD?

“Porque ele mostra o que estamos analisando: atores, processos, armazenamentos, fluxos e fronteiras. Assim, conseguimos associar cada ameaça a um ponto concreto, em vez de apenas enumerar problemas genéricos. O DFD orienta a análise, mas não comprova sozinho que uma falha é explorável.”

#### 3. Se o desenho separa processos, eles são necessariamente serviços diferentes?

“Não. A separação no DFD representa responsabilidades para análise. No nosso recorte, os processos P2 a P6 são responsabilidades do mesmo serviço da aplicação. Não podemos concluir, apenas pelo desenho, que cada processo tem contêiner ou permissões próprias.”

#### 4. O que é uma fronteira de confiança? Dê um exemplo.

“É um limite entre partes que não devem receber automaticamente o mesmo nível de confiança. Um exemplo é a passagem do navegador para o servidor. Como o cliente pode alterar os dados enviados, o servidor precisa validá-los e verificar permissões antes de consultar ou modificar informações.”

#### 5. Por que escolheram esses quatro fluxos?

“Eles cobrem a identidade e a sessão, o cadastro e a recuperação, uma operação de compra e funções privilegiadas. Assim, analisamos dados pessoais, acessos por titular e diferenças de papel. Os quatro cruzam fronteiras e três têm relação direta com achados da M1.”

#### 6. Qual a diferença entre ameaça, vulnerabilidade e risco?

“Ameaça é um cenário do que pode dar errado, envolvendo uma ação e um efeito sobre um ativo. Vulnerabilidade é uma fraqueza que pode permitir ou facilitar esse cenário. Risco é a avaliação da relevância do cenário; aqui, usamos DREAD para priorizá-lo conforme os critérios documentados.”

#### 7. O que significa cada letra de STRIDE? Uma ameaça pode ter mais de uma propriedade afetada?

“São falsificação de identidade, adulteração, repúdio, divulgação de informação, negação de serviço e elevação de privilégio. Um cenário pode ter consequências em mais de uma propriedade. No registro, atribuímos uma categoria principal para organizar a análise e evitar contagem duplicada.”

#### 8. Por que algumas células da matriz estão como N/A?

“Porque a aplicação de uma categoria depende do tipo de elemento e do contexto analisado. N/A significa não aplicável naquele recorte, e a matriz aponta para uma justificativa. Isso não prova que o componente inteiro seja seguro nem elimina ameaças que estejam representadas em outro processo ou fluxo.”

#### 9. Por que usar DREAD e não OWASP Risk Rating ou CVSS?

“O roteiro permitia DREAD ou OWASP Risk Rating. Escolhemos DREAD e aplicamos a mesma escala aos cenários, com cinco notas justificadas. CVSS foi usado na M1 para classificar vulnerabilidades; aqui, DREAD serviu para priorizar os cenários modelados. Uma comparação numérica direta entre os métodos não seria válida.”

#### 10. A maior nota DREAD garante que a ameaça acontecerá primeiro?

“Não. A média ordena os cenários segundo o julgamento e os critérios adotados. Ela não é uma probabilidade estatística nem uma previsão do próximo incidente. Por isso, registramos as cinco notas, suas justificativas e o estado da evidência.”

#### 11. Como a injeção de SQL levou à sessão administrativa?

“A entrada do campo de e-mail foi incorporada à consulta de autenticação de modo inseguro. No laboratório, uma entrada manipulada alterou a lógica dessa consulta e permitiu obter uma sessão de administrador sem a senha correta. A correção proposta é usar consulta parametrizada, separando os dados recebidos da estrutura SQL.”

#### 12. Se a consulta for parametrizada, todas as falhas do login estarão resolvidas?

“Não. A parametrização trata o mecanismo da injeção naquela consulta. Ainda é necessário tratar, por exemplo, dados desnecessários no JWT, armazenamento de senha, ciclo de vida da sessão e controles contra abuso. Cada correção precisa de um critério de aceite próprio.”

#### 13. O JWT é criptografado só por estar assinado? O hash da senha dentro dele está protegido?

“Não. A assinatura permite verificar integridade e origem quando a validação é correta, mas o conteúdo de um JWT assinado pode ser lido por quem possui o token. O hash não é a senha em texto puro, mas sua exposição ainda é indevida e pode permitir tentativas de descoberta da senha fora da aplicação.”

#### 14. Por que propor Argon2id em vez de MD5?

“MD5 é rápido e não foi projetado para dificultar tentativas de descoberta de senha. Argon2id permite configurar custo computacional e de memória e deve ser usado com sal e parâmetros adequados. É uma recomendação do plano; não afirmamos que a troca já foi implementada.”

#### 15. Um token sem o campo exp nunca expira?

“Não podemos afirmar isso só a partir do token. Nos exemplares examinados, não havia o campo exp, mas a aplicação poderia ter outras verificações de validade. Não testamos o comportamento temporal em todas as rotas; portanto, registramos a ausência do campo e a limitação separadamente.”

#### 16. Como uma conta foi criada como administradora no teste? Qual é o risco?

“O cadastro aceitou do cliente um campo que definia o papel da conta. Na M2, uma requisição com papel administrativo resultou em conta criada com esse papel. O risco é conceder acesso privilegiado por uma operação pública. A mitigação é o servidor aceitar somente os campos permitidos e definir o papel inicial.”

#### 17. Se o usuário precisa estar autenticado, por que a cesta de outra pessoa ainda é um problema?

“Porque autenticação e autorização respondem a perguntas diferentes. Uma sessão pode ser válida, mas o usuário ainda precisa ter permissão sobre a cesta solicitada. No ensaio, uma conta autenticada consultou a cesta de outra e recebeu HTTP 200; sem token, a rota respondeu 401. Em uma loja real, uma falha assim poderia expor a outra pessoa os itens escolhidos pelo titular; esse exemplo ilustra o risco, não é um incidente que investigamos.”

#### 18. Vocês provaram que era possível baixar um comprovante de outra pessoa?

“Não. Observamos a listagem do diretório `/ftp/` e o download de um arquivo de exemplo na M1. O acesso a um comprovante de terceiro permaneceu como hipótese baseada no caminho e no código analisados. Por isso, propusemos um teste específico de titularidade para essa situação.”

#### 19. A captura do painel administrativo prova que um cliente comum consultou usuários?

“Não sozinha. A captura mostra o painel em uma sessão administrativa. A consulta por uma conta customer foi verificada separadamente por HTTP na M2 e recebeu status 200. A imagem e o resultado da requisição demonstram situações diferentes.”

#### 20. As mitigações já foram aplicadas? Como vocês comprovariam que funcionam?

“Não. O relatório traz propostas e critérios de aceite. Depois da implementação, repetiríamos casos positivos e negativos: a entrada de injeção deve falhar, o JWT não deve conter o hash, o cadastro público deve criar somente o papel permitido e duas contas não devem acessar recursos uma da outra. Também testaríamos hipóteses ainda abertas, como concorrência e validade temporal dos tokens.”

#### 21. A injeção SQL dá acesso de admin. Por que vocês classificaram a T-FX1-02 como Tampering (adulteração) e não como Spoofing ou Elevation of Privilege?

“No STRIDE por elemento, classificamos pela ação sobre o elemento atacado. O que o agente faz é adulterar a lógica da consulta dentro do processo P2 e do fluxo F21; isso é Tampering, que viola a integridade. A sessão de administrador é a consequência dessa adulteração, e ela aparece no impacto descrito na ameaça. Escolhemos uma categoria principal para não contar o mesmo cenário duas vezes; as outras ameaças do fluxo cobrem Spoofing, com a força bruta, e Elevation, com a chave de assinatura.”

#### 22. Por que existe a fronteira TB-03 entre o código e o mapa de sessões, se ele está na memória do próprio servidor?

“A TB-03 é uma fronteira lógica, e deixamos isso escrito no relatório. Ela marca o momento em que uma requisição deixa de ser entrada e passa a ler ou gravar ativos internos, que é onde a consulta precisa ser parametrizada e a autorização precisa já ter sido feita. Para o D2 e o D3, que estão em memória, reconhecemos que não há mudança real de máquina ou de conta; por isso, as ameaças desses armazenamentos foram tratadas nos processos que os usam, e não no transporte até eles.”

#### 23. Por que todas as 25 ameaças têm a decisão “Mitigar”? Nenhuma poderia ser aceita, transferida ou eliminada?

“O roteiro prevê as quatro decisões. Como todas as ameaças ficaram com risco médio ou alto e cada uma tem uma correção viável e verificável no próprio código, mitigar foi a decisão adequada em todas. Aceitar faria sentido para um risco baixo ou de custo de correção desproporcional, o que não apareceu. Eliminar a funcionalidade seria uma opção para a recuperação por pergunta de segurança, mas propusemos substituí-la por token de uso único, o que é uma mitigação.”

#### 24. O STRIDE protege só confidencialidade, integridade e disponibilidade?

“Não. Além dessas três, o STRIDE cobre autenticidade, com Spoofing, não repúdio, com Repudiation, e autorização, com Elevation of Privilege. São seis propriedades, uma para cada letra, como mostra o Quadro 1 do relatório.”

#### 25. A própria Microsoft deixou de usar o DREAD por ser subjetivo. Por que vocês usaram?

“O roteiro da disciplina adota o DREAD e permite como alternativa o OWASP Risk Rating. Sabemos que o DREAD é subjetivo; por isso, cada uma das cinco notas de cada ameaça tem uma frase de justificativa, aplicamos a mesma escala a todas e declaramos um critério de desempate. Também separamos o estado da evidência, para que uma hipótese com nota alta não seja confundida com algo confirmado.”

#### 26. Se a T-FX2-06 e a T-FX4-04 foram reproduzidas, por que o registro ainda diz “Plausível”?

“Os estados previstos no roteiro são confirmada no T1, plausível ou tratada. Essas duas ameaças foram levantadas como hipóteses na análise e só depois as reproduzimos na M2, em 8 de novembro. Para manter o histórico, o registro diz ‘plausível na análise inicial; reproduzida na M2’, e o Top 10 mostra ‘reproduzida na M2’. Não as chamamos de ‘confirmadas no T1’ porque isso seria falso.”

#### 27. Por que o login com Google, o OAuth, ficou de fora?

“Ele foi listado como candidato reserva, o FX5. O roteiro pede pelo menos um fluxo com função privilegiada ou integração externa, e o FX4, de administração e contabilidade, já atende a esse critério com evidência concreta. O OAuth depende do provedor externo e não foi exercitado na M1, então teríamos pouca evidência. Fica como trabalho futuro.”

#### 28. Por que usaram o OWASP Top 10 de 2021, se existe edição mais nova?

“O roteiro do professor indica o OWASP Top 10:2021, e o Trabalho 1 classificou os achados nessa edição. Mantivemos a mesma edição para que os achados V1 a V7 e as ameaças da M2 sejam comparáveis diretamente.”

#### 29. Por que o relatório tem mais de 70 páginas?

“O roteiro não limita o tamanho do relatório final; o limite de 3 a 5 páginas era só da Etapa 1. Para cada um dos quatro fluxos, o roteiro exige o template completo: ficha, tabela de transferências, classificação dos dados, controles existentes, matriz STRIDE com justificativa de cada N/A, as 28 perguntas do questionário, o registro de ameaças em duas partes e a justificativa de cada nota DREAD. Isso dá cerca de doze páginas por fluxo, quase cinquenta no total. O resto do relatório tem tamanho normal.”

#### 30. Como foi feita a revisão cruzada entre os integrantes?

“Cada fluxo teve um responsável e um revisor de outro integrante, registrados na ficha do fluxo: Diego fez o FX1 e Gustavo revisou; Leonardo fez o FX2 e Diego revisou; Gustavo fez o FX3 e João Victor revisou; João Victor fez o FX4 e Leonardo revisou. O papel do revisor era conferir a análise do colega: matriz, notas e coerência com o código e as evidências. No relatório isso está nas fichas e na conciliação com o arquivo-fonte, e não em um quadro separado.”

#### 31. Onde está o arquivo-fonte do modelo?

“No repositório da M2, na pasta trabalho2/modelo: o DFD_Juice_Shop.drawio, que abre no diagrams.net, e o modelo_ameacas.json, com todos os elementos, setas, fronteiras, ameaças e notas. Também há um script que valida o modelo e recalcula a ordenação DREAD. O endereço está no último slide.”

#### 32. Qual a diferença entre a TB-01 e a TB-02, se as mesmas setas cruzam as duas?

“São a mesma passagem vista por perspectivas diferentes. A TB-01 é a da aplicação: dados controlados pelo cliente chegam a rotas que precisam autenticar, autorizar e validar. A TB-02 é a da infraestrutura: o tráfego sai do Windows e entra no contêiner Docker, publicado só em 127.0.0.1. Separamos as duas porque os controles são diferentes: validação e autorização na primeira, isolamento de rede na segunda.”

#### 33. O que é risco residual no caso de vocês?

“É o risco que continua depois das correções. Por exemplo, após parametrizar o login e tirar o hash do token, ainda sobram senhas que já vazaram ou que o usuário reutiliza em outros sites, ataques distribuídos de tentativa de senha e roubo de sessão antes de ela expirar. Por isso o plano inclui revogar tokens antigos e exigir troca de credenciais expostas.”

#### 34. Por que o navegador, o D0, aparece como armazenamento?

“Porque a SPA guarda o token e o ID da cesta no localStorage, no cookie e no sessionStorage do navegador, e lê esses dados de volta em cada chamada. É um lugar onde dado fica guardado, então é um armazenamento. Ele fica do lado do cliente, fora da TB-01, porque o próprio usuário pode ler e alterar esse conteúdo; por isso o servidor nunca pode confiar no que vem dele.”

#### 35. Por que a interface P1 fica do lado do cliente da fronteira, e não do lado do servidor?

“Porque o Angular roda no navegador do usuário. Qualquer verificação feita só em P1 pode ser contornada, por exemplo chamando a API direto com uma ferramenta. Por isso todas as verificações de identidade e permissão que propomos ficam em P2 a P6, do lado do servidor.”

#### 36. Por que a recuperação por pergunta de segurança é A04, design inseguro, e não um erro de código?

“Porque o código faz exatamente o que foi projetado: mostra a pergunta e aceita a resposta certa. O problema é a ideia, já que respostas de perguntas pessoais são fáceis de adivinhar ou descobrir. Corrigir uma linha não resolve; é preciso trocar o mecanismo por um token de uso único enviado a um canal verificado.”

#### 37. Vocês usaram LINDDUN, CAPEC ou árvores de ataque?

“Não. O roteiro coloca esses frameworks como opcionais. Usamos STRIDE para identificar, DREAD para priorizar, CWE e OWASP Top 10 para classificar, ASVS e as Cheat Sheets para as mitigações e a LGPD para os dados pessoais. Uma árvore de ataque sobre a injeção no login ou um mapeamento CAPEC seriam bons complementos em uma próxima versão.”

#### 38. No DFD de nível 0 as setas F01 a F08 cruzam a TB-01, mas no nível 1 elas não cruzam fronteira nenhuma. Não é contraditório?

“Não. No nível 0, o sistema inteiro é uma bola só, o P0, que inclui também a interface que roda no navegador; por isso a fronteira aparece condensada na borda do sistema. No nível 1, abrimos o P0 e separamos a interface, o P1, que fica no navegador, do lado do usuário. A fronteira cliente–servidor passa então a ficar entre o P1 e os processos do servidor, nas setas F09 a F18. É a mesma fronteira, desenhada com mais detalhe.”

#### Pergunta sugerida para outra equipe

“Qual ameaça identificada por vocês foi considerada mais prioritária e como a equipe verificaria, na prática, se a mitigação proposta funciona?”

## Apêndice · Referências principais

- SHOSTACK, A. *Threat Modeling: Designing for Security*. Wiley, 2014.
- HERNAN, S. et al. *Uncover security design flaws using the STRIDE approach*. MSDN, 2006.
- MEIER, J. D. et al. *Improving Web Application Security*. Microsoft, 2003.
- MANADHATA, P.; WING, J. *An attack surface metric*. IEEE TSE, 2011.
- OWASP ASVS 5.0.0; OWASP Top 10:2021; MITRE CWE.
- BRASIL. Lei nº 13.709/2018 (LGPD), arts. 5º, 6º e 46.
- SILVA, D. F. et al. *Case prático 1* (Trabalho 1). UNIVALI, 2026.
