# Roteiro falado: Modelagem de Ameaças no OWASP Juice Shop

O texto entre aspas é a fala completa sugerida. As indicações entre colchetes são orientações e não precisam ser lidas. Ensaiem com cronômetro: o limite é de **15 minutos para a equipe**, seguidos de perguntas.

**Divisão sugerida dos fluxos, conforme o relatório:** Diego apresenta FX1; Leonardo, FX2; Gustavo, FX3; e João Victor, FX4. Os demais slides podem ser divididos entre os quatro. Todos devem participar, e a equipe deve fazer ao menos uma pergunta a outra apresentação.

### Slide 1: Capa

**FALA**

“Boa noite, professor e colegas. Nós somos Diego Fonseca da Silva, Leonardo Pacheco B Dias, Gustavo Gonçalves Trindade e João Victor Rodrigues Santos.

Neste trabalho, apresentamos a modelagem de ameaças do OWASP Juice Shop, a mesma aplicação utilizada no Trabalho 1. Nosso objetivo foi representar o funcionamento do sistema, identificar o que poderia dar errado e propor medidas para reduzir os riscos encontrados.”

### Slide 2: O que veremos

**FALA**

“Primeiro, vamos contextualizar a aplicação e explicar o método. Depois, apresentaremos o diagrama de fluxo de dados e as fronteiras de confiança.

Em seguida, cada integrante explicará um dos quatro fluxos analisados. Por fim, mostraremos as ameaças prioritárias, as mitigações, os resultados da validação e as principais conclusões. O diagrama será o fio condutor da apresentação.”

### Slide 3: Contexto do ambiente

**FALA**

“O sistema analisado é o OWASP Juice Shop, na versão vinte ponto dois ponto zero. Ele simula uma loja virtual e foi desenvolvido para o ensino de segurança de aplicações.

A interface Angular se comunica com uma API em Node e Express. A aplicação usa SQLite, uma coleção em memória para pedidos, tokens JWT nas sessões e comprovantes em PDF.

O laboratório foi executado em contêiner Docker, no endereço local e porta três mil. Consideramos visitante, cliente, administrador e usuário de contabilidade, além de ativos como contas, credenciais, pedidos e dados pessoais.

No Trabalho 1, avaliamos a aplicação a partir de testes. Agora, usamos essas evidências para entender também a estrutura interna e os caminhos dos dados.”

### Slide 4: Método: DFD, STRIDE e DREAD

**FALA**

“O método segue as quatro perguntas de Shostack: o que estamos construindo, o que pode dar errado, o que faremos a respeito e se fizemos um bom trabalho.

Para responder à primeira, usamos o DFD, o diagrama de fluxo de dados. Para identificar ameaças, aplicamos STRIDE aos elementos do diagrama. Em seguida, utilizamos DREAD para atribuir notas e priorizar os cenários.

As mitigações foram relacionadas a referências como ASVS e CWE, com atenção à proteção dos dados pessoais quando aplicável.”

### Slide 5: Modelo do sistema e fronteiras

[Apontar para o DFD durante a explicação.]

**FALA**

“Este é o nosso DFD de nível um. Ele representa como os atores interagem com a aplicação e como as informações passam pelos processos e armazenamentos.

À esquerda estão os usuários. A interface recebe suas ações e as encaminha aos processos internos.

No centro, estão as responsabilidades de autenticação, cadastro e recuperação, cesta e pedido, funções privilegiadas e arquivos. Elas aparecem separadas para análise, mas fazem parte do mesmo serviço.

À direita estão os armazenamentos: o banco SQLite, o mapa de sessões, a coleção de pedidos, o diretório de arquivos e os registros de acesso.

As três fronteiras separam o cliente do servidor, o host do contêiner e a aplicação dos armazenamentos. Elas ajudam a identificar onde validar dados e verificar permissões.

Vamos usar agora recortes desse mesmo diagrama para acompanhar os quatro fluxos.”

### Slide 6: Os quatro fluxos

**FALA**

“Selecionamos quatro fluxos porque eles percorrem partes relevantes da aplicação e envolvem diferentes tipos de dados e permissões.

O primeiro trata do login e da sessão. O segundo cobre cadastro e recuperação de senha. O terceiro acompanha a cesta e o pedido. O quarto reúne as funções de administração e contabilidade.

Ao todo, registramos 25 cenários de ameaça, com pelo menos seis por fluxo e cobertura das seis categorias STRIDE. A partir daqui, cada integrante apresenta um recorte e as ameaças que consideramos mais relevantes.”

### Slide 7: FX1 · Login e sessão

**FALA SUGERIDA PARA DIEGO**

“O primeiro fluxo é o de login e uso da sessão. No diagrama, acompanhamos a entrada das credenciais pela interface, a consulta ao banco e a emissão do token que será usado nas chamadas seguintes.

A ameaça mais pontuada foi a T-FX1-02, com média nove vírgula oito. O e-mail do login era incorporado à consulta SQL de forma insegura. Na M1 e na M2, uma entrada manipulada permitiu obter uma sessão de administrador sem a senha correta.

Outro problema é a exposição do hash MD5 da senha no JWT. Nos tokens examinados, não identificamos o campo de expiração, chamado exp. Observamos essa ausência, mas não testamos a validade temporal em todas as rotas.

Como tratamento, propomos parametrizar a consulta de autenticação, restringir os dados incluídos no token e substituir o armazenamento de senhas por um algoritmo adequado, como Argon2id. Essas medidas devem ser verificadas com novos testes após a implementação.”

### Slide 8: FX2 · Cadastro e recuperação

**FALA SUGERIDA PARA LEONARDO**

“O segundo fluxo trata da criação de contas e da recuperação de senha. O visitante informa os dados de cadastro e, no processo de recuperação, utiliza uma pergunta de segurança associada à conta.

A T-FX2-06 recebeu média nove. O cadastro aceitava do cliente um campo que definia o papel da conta. Na M2, enviamos uma requisição com papel de administrador e a conta foi criada com esse papel.

O outro cenário destacado é a recuperação por resposta previsível. Na M1, a pergunta de segurança pôde ser consultada a partir do e-mail e a senha foi redefinida após uma resposta correta dentro da cota observada.

As duas situações exigem correções diferentes. Para o cadastro, o papel inicial deve ser definido pelo servidor. Para a recuperação, propomos abandonar perguntas previsíveis e utilizar uma prova de posse de canal com token de uso único e prazo limitado.”

### Slide 9: FX3 · Cesta, checkout e pedido

**FALA SUGERIDA PARA GUSTAVO**

“No terceiro fluxo, acompanhamos o cliente desde a consulta da cesta até o checkout e a geração do pedido e do comprovante.

A T-FX3-06 recebeu média oito vírgula quatro. A aplicação verificava a sessão, mas permitia a um usuário consultar a cesta de outro titular informando seu identificador.

Na M2, a chamada com o token de outra conta recebeu HTTP duzentos. Sem token, recebeu quatrocentos e um. A autenticação estava presente, mas faltou verificar quem era o dono da cesta.

Também analisamos o diretório barra FTP. A listagem e o download de um arquivo de exemplo foram observados na M1. Já o acesso ao comprovante de outra pessoa e o checkout com cesta alheia permaneceram hipóteses, sem confirmação específica no recorte apresentado.

A recomendação é verificar a titularidade em cada operação sobre cesta, pedido e comprovante, e disponibilizar arquivos por uma rota autorizada.”

### Slide 10: FX4 · Administração e contabilidade

**FALA SUGERIDA PARA JOÃO VICTOR**

“O quarto fluxo reúne operações de administração e contabilidade, que podem alcançar dados de vários usuários. No recorte do DFD, vemos os caminhos dessas chamadas até os processos e armazenamentos usados pela aplicação.

Na ameaça T-FX4-04, com média oito vírgula seis, verificamos o acesso à consulta de usuários. Na M2, uma conta com papel de cliente conseguiu chamar a rota de detalhes de autenticação e recebeu HTTP duzentos com uma listagem de contas.

A imagem do slide mostra o painel acessado como administrador. O resultado com a conta de cliente foi obtido separadamente por uma requisição HTTP; a imagem, sozinha, não demonstra esse teste.

Também registramos a T-FX4-06, sobre uma possível exclusão indevida de feedback. A condição foi identificada na análise do código, mas a exclusão por uma conta sem o papel adequado não foi testada nesse ensaio.

Como mitigação, propomos definir as permissões no servidor para cada rota, negar por padrão as ações não autorizadas e registrar as operações relevantes.”

### Slide 11: Top 10 por DREAD

**FALA**

“Neste slide, reunimos as dez ameaças com maior média DREAD. A pontuação considera dano, reprodutibilidade, facilidade de exploração, usuários afetados e facilidade de descoberta.

No conjunto dos 25 cenários, 18 ficaram na faixa de risco alto e sete na faixa de risco médio. A injeção de SQL no login aparece em primeiro lugar, com nove vírgula oito. Em seguida, vem a atribuição de papel privilegiado no cadastro, com nove.

Essa classificação ajuda a ordenar as ações dentro do nosso recorte. Ela depende dos critérios e das justificativas documentadas pela equipe e não mede, por si só, a frequência real de incidentes.

O gráfico também reúne resultados observados e hipóteses. Por isso, a pontuação deve ser lida junto com o estado de validação de cada ameaça.”

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

Esses resultados complementam as evidências da M1. Eles não confirmam automaticamente todos os outros cenários do modelo.

Os ensaios foram de baixo volume. Não medimos esgotamento de recursos, concorrência nem o comportamento temporal dos tokens em todas as rotas. Essas limitações foram registradas no relatório e devem ser consideradas ao interpretar os resultados.”

### Slide 14: Conclusão e lições

**FALA**

“A modelagem mostrou que as falhas aparecem em momentos diferentes do funcionamento da aplicação. A injeção afeta a consulta que determina a identidade; o hash é exposto na composição do token; e a leitura de uma cesta alheia acontece depois que uma sessão já foi aceita.

Por isso, as prioridades incluem corrigir a autenticação, limitar os dados do token, impedir a atribuição de papéis pelo cadastro público e verificar a autorização em cada recurso.

A principal lição para a equipe foi que reconhecer uma sessão não é suficiente para demonstrar que o usuário tem direito a uma cesta, a um pedido ou a um arquivo específico.

Ao representar os caminhos dos dados e associar as ameaças às evidências e aos controles propostos, o trabalho complementa a avaliação realizada na M1 e oferece um plano para orientar as próximas correções.”

### Slide 15: Obrigado

**FALA**

“Com isso, concluímos a apresentação. O relatório, o modelo e os materiais de apoio estão no repositório indicado no slide.

Obrigado pela atenção. Ficamos à disposição para responder às perguntas.”

## Apêndice · Respostas para perguntas prováveis

#### Por que utilizaram DREAD?

“O roteiro permitia o uso de DREAD ou OWASP Risk Rating. Escolhemos DREAD para aplicar a mesma escala aos cenários e documentamos uma justificativa para cada uma das cinco dimensões. As notas envolvem julgamento e servem para priorizar as ameaças dentro do recorte do trabalho.”

#### O que é uma fronteira de confiança?

“É um limite entre partes que não devem receber o mesmo nível de confiança automaticamente. Por exemplo, os dados enviados pelo navegador vêm de um ambiente controlado pelo cliente. Ao chegar ao servidor, precisam ser validados antes de serem usados para consultar ou alterar informações.”

#### Qual a diferença entre autenticação e autorização?

“Autenticação verifica a identidade ou a validade da sessão apresentada. Autorização verifica se aquela identidade pode realizar uma ação sobre um recurso. No teste da cesta, a sessão era válida, mas a aplicação também precisava verificar se a cesta pertencia àquela conta.”

#### O token sem o campo exp nunca expira?

“Nos tokens analisados, não encontramos o campo exp. Isso não prova, por si só, que todas as rotas aceitarão o token indefinidamente, pois podem existir outras verificações. O comportamento temporal por rota não foi testado; por isso, registramos a ausência do campo e essa limitação separadamente.”

#### O que foi confirmado e o que continua como hipótese?

“Chamamos de confirmados os comportamentos sustentados pelas evidências da M1 ou reproduzidos nas verificações da M2, sempre indicando qual ensaio os observou. Outros cenários foram identificados por código ou documentação e continuam como hipóteses até que um teste específico confirme seu efeito. A leitura da cesta alheia foi reproduzida; o acesso ao comprovante de um terceiro não foi exercitado.”

#### Por que considerar a LGPD?

“Os fluxos envolvem dados que podem identificar usuários, como e-mail e informações vinculadas a contas e pedidos. Por isso, relacionamos medidas de segurança à proteção desses dados, incluindo o controle de quem pode acessá-los e a redução de informações desnecessárias nas respostas e nos tokens.”

#### As mitigações já foram aplicadas?

“Não. O relatório apresenta as medidas propostas e critérios para testá-las. Para afirmar que uma mitigação funciona, seria necessário implementá-la e repetir os casos positivos e negativos indicados no plano.”

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
