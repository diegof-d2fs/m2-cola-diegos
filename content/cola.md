# Roteiro falado: Modelagem de Ameaças no OWASP Juice Shop

O texto entre aspas é a fala completa sugerida. As indicações entre colchetes são orientações e não precisam ser lidas. Ensaiem com cronômetro: o limite é de **15 minutos para a equipe**, seguidos de perguntas.

**Divisão sugerida dos fluxos, conforme o relatório:** Diego apresenta FX1; Leonardo, FX2; Gustavo, FX3; e João Victor, FX4. Os demais slides podem ser divididos entre os quatro. Todos devem participar, e a equipe deve fazer ao menos uma pergunta a outra apresentação.

### Slide 1: Capa

**FALA**

“Boa noite, professor e colegas. Nós somos Diego Fonseca da Silva, Leonardo Pacheco B Dias, Gustavo Gonçalves Trindade e João Victor Rodrigues Santos.

Neste trabalho, apresentamos a modelagem de ameaças do OWASP Juice Shop, a mesma aplicação utilizada no Trabalho 1. OWASP é a Open Worldwide Application Security Project, uma comunidade que produz materiais abertos sobre segurança de aplicações. Nosso objetivo foi representar o funcionamento do sistema, identificar o que poderia dar errado e propor medidas para reduzir os riscos encontrados.”

### Slide 2: O que veremos

**FALA**

“Primeiro, vamos contextualizar a aplicação e explicar o método. Depois, apresentaremos o diagrama de fluxo de dados e as fronteiras de confiança.

Depois, apresentaremos os quatro fluxos, as ameaças prioritárias, as mitigações e a validação. O diagrama será o fio condutor da apresentação.”

### Slide 3: Contexto do ambiente

**FALA**

“O sistema analisado é o OWASP Juice Shop, na versão vinte ponto dois ponto zero. Ele simula uma loja virtual e foi desenvolvido para o ensino de segurança de aplicações.

A interface Angular se comunica com uma API, isto é, uma interface de programação pela qual a aplicação troca solicitações e respostas com o servidor. Essa API usa Node e Express. A aplicação usa SQLite, uma coleção em memória para pedidos e tokens JWT, ou JSON Web Tokens, para representar informações da sessão. Os comprovantes são arquivos PDF, um formato de documento portátil.

O laboratório foi executado em contêiner Docker, no endereço local e porta três mil. Consideramos visitante, cliente, administrador e usuário de contabilidade, além de ativos como contas, credenciais, pedidos e dados pessoais.

Usamos as evidências da M1 para analisar também a estrutura e os caminhos dos dados.”

### Slide 4: Método: DFD, STRIDE e DREAD

**FALA**

“O método segue as quatro perguntas de Shostack: o que estamos construindo, o que pode dar errado, o que faremos a respeito e se fizemos um bom trabalho.

Para responder à primeira, usamos o DFD, diagrama de fluxo de dados, que representa atores, processos, armazenamentos e transferências. Para identificar ameaças, aplicamos STRIDE, método que organiza cenários em seis categorias de ameaça. Em seguida, utilizamos DREAD, método que avalia cinco dimensões para ajudar a priorizar os cenários.

As mitigações foram relacionadas a referências como ASVS, padrão de verificação de segurança de aplicações, e CWE, catálogo de tipos de fraqueza. Também consideramos a LGPD, Lei Geral de Proteção de Dados Pessoais, quando aplicável.”

### Slide 5: Modelo do sistema e fronteiras

[Apontar para o DFD durante a explicação.]

**FALA**

“Este é o nosso DFD de nível um. Ele representa como os atores interagem com a aplicação e como as informações passam pelos processos e armazenamentos.

À esquerda estão os usuários. A interface recebe suas ações e as encaminha aos processos internos.

No centro, estão as responsabilidades de autenticação, cadastro e recuperação, cesta e pedido, funções privilegiadas e arquivos. Elas aparecem separadas para análise, mas fazem parte do mesmo serviço.

À direita estão SQLite, o mapa de sessões, os pedidos, os arquivos e os registros de acesso.

As três fronteiras separam o cliente do servidor, o host do contêiner e a aplicação dos armazenamentos. Elas ajudam a identificar onde validar dados e verificar permissões.

Vamos usar agora recortes desse mesmo diagrama para acompanhar os quatro fluxos.”

### Slide 6: Os quatro fluxos

**FALA**

“Selecionamos quatro fluxos porque eles percorrem partes relevantes da aplicação e envolvem diferentes tipos de dados e permissões.

O primeiro trata do login e da sessão. O segundo cobre cadastro e recuperação de senha. O terceiro acompanha a cesta e o pedido. O quarto reúne as funções de administração e contabilidade.

Registramos 25 cenários, com pelo menos seis por fluxo e cobertura das seis categorias STRIDE. Cada integrante apresentará agora um recorte.”

### Slide 7: FX1 · Login e sessão

**FALA SUGERIDA PARA DIEGO**

“O primeiro fluxo é o de login e uso da sessão. No diagrama, acompanhamos a entrada das credenciais pela interface, a consulta ao banco e a emissão do token que será usado nas chamadas seguintes.

A ameaça mais pontuada foi a T-FX1-02, com média nove vírgula oito. O e-mail do login era incorporado de forma insegura a uma consulta SQL, linguagem usada para consultar dados relacionais. Na M1 e na M2, uma entrada manipulada permitiu obter uma sessão de administrador sem a senha correta.

Outro problema é a exposição do hash MD5 da senha no JWT. O MD5 é uma função de resumo que não é adequada para proteger senhas. Nos tokens examinados, não identificamos o campo de expiração, chamado exp. Observamos essa ausência, mas não testamos a validade temporal em todas as rotas.

Propomos parametrizar a consulta, restringir os dados do token e usar um algoritmo adequado para senhas, como Argon2id. Depois da implementação, essas medidas devem ser testadas.”

### Slide 8: FX2 · Cadastro e recuperação

**FALA SUGERIDA PARA LEONARDO**

“O segundo fluxo trata da criação de contas e da recuperação de senha. O visitante informa os dados de cadastro e, no processo de recuperação, utiliza uma pergunta de segurança associada à conta.

A T-FX2-06 recebeu média nove. O cadastro aceitava do cliente um campo que definia o papel da conta. Na M2, enviamos uma requisição com papel de administrador e a conta foi criada com esse papel.

O outro cenário destacado é a recuperação por resposta previsível. Na M1, a pergunta de segurança pôde ser consultada a partir do e-mail e a senha foi redefinida após uma resposta correta dentro da cota observada.

O papel inicial deve ser definido pelo servidor. Para recuperação, propomos substituir perguntas previsíveis por uma prova de posse de canal com token de uso único e prazo limitado.”

### Slide 9: FX3 · Cesta, checkout e pedido

**FALA SUGERIDA PARA GUSTAVO**

“No terceiro fluxo, acompanhamos o cliente desde a consulta da cesta até o checkout e a geração do pedido e do comprovante.

A T-FX3-06 recebeu média oito vírgula quatro. A aplicação verificava a sessão, mas permitia a um usuário consultar a cesta de outro titular informando seu identificador.

Na M2, a chamada com o token de outra conta recebeu HTTP duzentos, indicando sucesso. HTTP é o protocolo usado nas requisições web. Sem token, recebeu quatrocentos e um, indicando falta de autenticação. A autenticação estava presente, mas faltou verificar quem era o dono da cesta.

Também analisamos o diretório barra FTP. FTP significa File Transfer Protocol, ou protocolo de transferência de arquivos; aqui, esse é o nome do diretório consultado por HTTP, não uma demonstração de uso do protocolo FTP. A listagem e o download de um arquivo de exemplo foram observados na M1. Já o acesso ao comprovante de outra pessoa e o checkout com cesta alheia permaneceram hipóteses, sem confirmação específica no recorte apresentado.

A recomendação é verificar a titularidade em cada operação sobre cesta, pedido e comprovante, e disponibilizar arquivos por uma rota autorizada.”

### Slide 10: FX4 · Administração e contabilidade

**FALA SUGERIDA PARA JOÃO VICTOR**

“O quarto fluxo reúne operações de administração e contabilidade, que podem alcançar dados de vários usuários. No recorte do DFD, vemos os caminhos dessas chamadas até os processos e armazenamentos usados pela aplicação.

Na ameaça T-FX4-04, com média oito vírgula seis, verificamos o acesso à consulta de usuários. Na M2, uma conta com papel de cliente conseguiu chamar a rota de detalhes de autenticação e recebeu HTTP duzentos com uma listagem de contas.

A imagem mostra uma sessão administrativa; o teste com a conta de cliente foi uma requisição HTTP separada.

A T-FX4-06 trata de possível exclusão indevida de feedback. A hipótese veio da análise do código, mas a exclusão por conta sem o papel adequado não foi testada.

Como mitigação, propomos definir as permissões no servidor para cada rota, negar por padrão as ações não autorizadas e registrar as operações relevantes.”

### Slide 11: Top 10 por DREAD

**FALA**

“Neste slide, reunimos as dez ameaças com maior média DREAD. A pontuação considera dano, reprodutibilidade, facilidade de exploração, usuários afetados e facilidade de descoberta.

No conjunto dos 25 cenários, 18 ficaram na faixa de risco alto e sete na faixa de risco médio. A injeção de SQL no login aparece em primeiro lugar, com nove vírgula oito. Em seguida, vem a atribuição de papel privilegiado no cadastro, com nove.

Essa classificação ordena as ações no nosso recorte e depende das justificativas da equipe. Ela não mede a frequência real de incidentes.

O gráfico reúne resultados observados e hipóteses; por isso, é necessário considerar o estado de validação de cada ameaça.”

### Slide 12: Plano de mitigação

**FALA**

“Organizamos o tratamento em seis frentes para que ameaças relacionadas possam ser corrigidas e verificadas em conjunto.

Na primeira frente estão a consulta parametrizada, a redução dos dados no JWT e a proteção das senhas. Na segunda, o servidor deve definir o papel da conta e a recuperação deve usar um mecanismo de uso único.

Na terceira frente, aplicamos autorização por função e por titularidade às rotas e aos recursos. As demais tratam do ciclo de vida da sessão, da contenção de abuso, da integridade dos pedidos, da auditoria e das respostas de erro.

Cada frente tem um critério de aceite. Por exemplo: a entrada usada na injeção deve falhar; o token não deve conter o hash; e duas contas de teste não devem conseguir acessar os recursos uma da outra.

Essas medidas são propostas. Sua eficácia precisa ser verificada depois que forem implementadas.”

### Slide 13: Validação no laboratório

**FALA**

“Para conferir parte da análise, realizamos uma execução complementar no laboratório em oito de novembro de dois mil e vinte e seis, utilizando a versão definida para o trabalho.

Reproduzimos a injeção no login, observamos o hash no JWT, criamos uma conta com papel administrativo pelo cadastro e consultamos a cesta de outro titular. Também verificamos a listagem anônima do diretório FTP e a consulta de usuários por uma conta de cliente.

Esses resultados complementam a M1, mas não confirmam todos os cenários do modelo.

Os ensaios foram de baixo volume. Não medimos esgotamento de recursos, concorrência nem o comportamento temporal dos tokens em todas as rotas. Essas limitações foram registradas no relatório e devem ser consideradas ao interpretar os resultados.”

### Slide 14: Conclusão e lições

**FALA**

“A modelagem mostrou que as falhas aparecem em momentos diferentes do funcionamento da aplicação. A injeção afeta a consulta que determina a identidade; o hash é exposto na composição do token; e a leitura de uma cesta alheia acontece depois que uma sessão já foi aceita.

Por isso, as prioridades incluem corrigir a autenticação, limitar os dados do token, impedir a atribuição de papéis pelo cadastro público e verificar a autorização em cada recurso.

A principal lição para a equipe foi que reconhecer uma sessão não é suficiente para demonstrar que o usuário tem direito a uma cesta, a um pedido ou a um arquivo específico.

O modelo complementa a M1 e oferece um plano para orientar as próximas correções.”

### Slide 15: Obrigado

**FALA**

“Com isso, concluímos a apresentação. O relatório, o modelo e os materiais de apoio estão no repositório indicado no slide.

Obrigado pela atenção. Ficamos à disposição para responder às perguntas.”

## Apêndice · Siglas e conceitos para consulta

Este glossário é apoio para entender e responder perguntas; não precisa ser lido inteiro durante a apresentação. As primeiras ocorrências essenciais já são explicadas nas falas.

- **UNIVALI**: Universidade do Vale do Itajaí. **SC**: Santa Catarina, estado em que está situada a instituição.
- **OWASP**: *Open Worldwide Application Security Project*, comunidade que produz materiais abertos sobre segurança de aplicações. **OWASP Top 10**: lista de categorias de riscos relevantes para aplicações web, usada aqui na edição de 2021; não é uma pontuação da gravidade de cada falha.
- **DFD**: *Data Flow Diagram*, ou diagrama de fluxo de dados. Representa entidades externas, processos, armazenamentos, transferências e fronteiras de confiança.
- **STRIDE**: método de identificação de ameaças. Suas letras significam **S**, *Spoofing*, falsificação de identidade; **T**, *Tampering*, adulteração; **R**, *Repudiation*, repúdio de uma ação; **I**, *Information Disclosure*, exposição de informação; **D**, *Denial of Service*, negação de serviço; e **E**, *Elevation of Privilege*, elevação de privilégio.
- **DREAD**: dimensões de priorização: **D**, *Damage*, dano; **R**, *Reproducibility*, reprodutibilidade; **E**, *Exploitability*, explorabilidade; **A**, *Affected Users*, usuários afetados; e **D**, *Discoverability*, facilidade de descoberta. A média utilizada é uma avaliação documentada da equipe, não uma medição automática.
- **ASVS**: *Application Security Verification Standard*, padrão da OWASP com requisitos para verificar controles de segurança de aplicações.
- **CWE**: *Common Weakness Enumeration*, catálogo de tipos de fraqueza de software. **MITRE**: organização que mantém o catálogo CWE; seu nome é usado como nome próprio.
- **LGPD**: Lei Geral de Proteção de Dados Pessoais, Lei nº 13.709/2018. O artigo 46 trata de medidas de segurança para proteger dados pessoais; a lei também contém outros princípios e obrigações.
- **CVSS**: *Common Vulnerability Scoring System*, sistema de pontuação da severidade de vulnerabilidades, utilizado na M1. A pontuação DREAD da M2 usa outro método e não deve ser apresentada como CVSS.
- **API**: *Application Programming Interface*, interface de programação que define como componentes trocam solicitações e respostas. Na aplicação, a interface web chama rotas da API.
- **SPA**: *Single-Page Application*, aplicação de página única que atualiza partes da interface no navegador sem carregar uma nova página completa a cada interação. **UI**: *User Interface*, interface com que a pessoa interage.
- **HTTP**: *Hypertext Transfer Protocol*, protocolo para requisições e respostas na web. **HTTPS**: HTTP protegido por TLS. **TLS**: *Transport Layer Security*, protocolo que protege a comunicação em trânsito quando configurado corretamente. No ensaio, a aplicação foi acessada por HTTP local.
- **JWT**: *JSON Web Token*, formato de token com informações chamadas *claims*. Sua assinatura ajuda a detectar alterações quando verificada; ela não torna o conteúdo secreto nem substitui a autorização por recurso. **JSON**: *JavaScript Object Notation*, formato textual de representação de dados.
- **RS256**: algoritmo de assinatura que combina RSA com SHA-256. **RSA**: algoritmo de criptografia assimétrica utilizado aqui para assinatura. **SHA-256**: função de resumo da família Secure Hash Algorithm. Assinar um JWT não equivale a cifrar seu conteúdo.
- **SQL**: *Structured Query Language*, linguagem para consultar e manipular dados relacionais. **SQL injection**: manipulação da estrutura de uma consulta quando entradas são incorporadas de modo inseguro.
- **SQLite**: banco de dados relacional embarcado utilizado pela aplicação. O nome combina SQL com “lite”; não é preciso inventar uma expansão para o nome completo. **MarsDB**: biblioteca usada no recorte para a coleção de pedidos em memória; o nome é próprio, não foi tratado como sigla no trabalho.
- **MD5**: *Message Digest Algorithm 5*, função de resumo antiga e rápida, inadequada para armazenamento seguro de senhas. **Argon2id**: algoritmo de derivação de chaves que pode ser usado para armazenar senhas com sal e custo configurável; é uma mitigação proposta, não implementada no ensaio.
- **IDOR**: *Insecure Direct Object Reference*, referência direta insegura a objeto. Ocorre quando uma aplicação permite acessar ou alterar um objeto identificado pelo cliente sem a autorização necessária. **ID** significa *identifier*, identificador.
- **FTP**: *File Transfer Protocol*, protocolo de transferência de arquivos. No trabalho, `/ftp/` é o nome de um diretório acessado por HTTP; o teste não demonstra uso do protocolo FTP.
- **PDF**: *Portable Document Format*, formato de documento portátil. **SVG**: *Scalable Vector Graphics*, formato de imagem vetorial escalável usado nos diagramas dos slides.
- **POST**, **GET** e **DELETE**: métodos HTTP para enviar uma solicitação de criação ou processamento, consultar um recurso e solicitar sua exclusão, respectivamente. Seu significado geral não garante que uma aplicação os implemente com segurança.
- **HTTP 200**, **201** e **401**: códigos que indicam, respectivamente, uma resposta bem-sucedida, criação de recurso e ausência ou falha de autenticação válida. O significado do teste depende também da rota, do papel e do corpo da resposta.
- **M1/T1** e **M2**: identificam, neste trabalho, o primeiro e o segundo trabalhos ou momentos de avaliação. **C1 a C10**: códigos das questões de contextualização do sistema e do ambiente. **V1 a V7**: identificadores de achados da M1; os itens V1 a V3 do questionário de fechamento têm outro significado e não devem ser confundidos com esses achados.
- **FX1 a FX4**: códigos dos quatro fluxos selecionados. **T-FX1-02**, por exemplo, identifica uma ameaça, o fluxo ao qual pertence e seu número sequencial. **E** identifica entidade externa; **P**, processo; **D**, armazenamento; **F**, transferência de dados; e **TB**, *Trust Boundary*, fronteira de confiança. O significado exato de cada número aparece no diagrama e no relatório.
- **A01, A02, A03 e A07**: códigos do OWASP Top 10:2021 para, respectivamente, falhas de controle de acesso, falhas criptográficas, injeção e falhas de identificação e autenticação. Não são notas DREAD nem identificadores CWE. **N/A** significa não aplicável, com justificativa registrada na matriz.
- **SDL**: *Security Development Lifecycle*, abordagem que incorpora atividades de segurança ao desenvolvimento. **IEEE**: *Institute of Electrical and Electronics Engineers*, entidade profissional e editora técnica; **TSE**: *Transactions on Software Engineering*, periódico citado. **MSDN**: *Microsoft Developer Network*, rede de materiais para desenvolvedores da Microsoft, mencionada na referência de STRIDE.

## Apêndice · 20 perguntas que o professor pode fazer

As respostas abaixo estão escritas para serem faladas. Quando a pergunta for sobre um teste não realizado, a resposta preserva essa limitação.

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
