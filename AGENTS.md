AGENTS.md — Financial
Objetivo

Este arquivo contém as instruções para agentes de IA que trabalham neste projeto.

Antes de criar, alterar, mover ou remover código, o agente deve consultar este arquivo e também:

docs/architecture/ARCHITECTURE.md


A arquitetura existente deve ser preservada.

1. Regra principal

Não introduza uma nova arquitetura ou uma nova organização de diretórios sem necessidade.

O projeto utiliza uma organização por contexto funcional dentro de:

src/main/java/br/com/objetive/financial/usecase/


Atualmente existem os contextos:

usecase/
├── bankaccount/
└── operationtransation/


Novas funcionalidades devem seguir o padrão arquitetural existente.

2. Antes de alterar o código

Antes de implementar qualquer alteração:

Leia este arquivo.

Leia docs/architecture/ARCHITECTURE.md.

Identifique o módulo funcional afetado.

Procure implementações semelhantes no projeto.

Identifique a responsabilidade da nova classe.

Verifique as dependências existentes.

Preserve o padrão utilizado pelo módulo.

Não crie uma nova estrutura apenas porque outro padrão arquitetural parece mais conveniente.

3. Estrutura do projeto

A estrutura principal é:

src/
├── main/
│   ├── java/
│   │   └── br/
│   │       └── com/
│   │           └── objetive/
│   │               └── financial/
│   │                   ├── common/
│   │                   │   └── exception/
│   │                   │
│   │                   └── usecase/
│   │                       ├── bankaccount/
│   │                       └── operationtransation/
│   │
│   └── resources/
│
└── test/
└── java/
└── br/
└── com/
└── objetive/
└── financial/
└── usecase/
├── bankaccount/
└── operationtransation/

4. Organização dos módulos

Um módulo funcional segue, quando aplicável, esta estrutura:

<module>/
├── adapter/
│   ├── driven/
│   └── external/
│       ├── controller/
│       └── dto/
├── converter/
├── entity/
├── enumeration/
├── model/
├── port/
└── service/


Nem todo módulo precisa obrigatoriamente possuir todas essas pastas.

Crie somente o que for necessário.

5. Regras para novos arquivos

Antes de criar uma classe, determine sua responsabilidade.

Use a seguinte orientação:

Responsabilidade	Local
Entrada HTTP/externa	adapter/external
Controller	adapter/external/controller
DTO externo	adapter/external/dto
Comunicação externa de saída	adapter/driven
Contrato/abstração	port
Caso de uso/orquestração	service
Modelo	model
Entidade	entity
Conversão de dados	converter
Enum específico	enumeration
Exceção compartilhada	common/exception

A classificação deve ser baseada na responsabilidade real da classe.

6. Controllers

Controllers pertencem a:

adapter/external/controller/


Controllers são responsáveis por adaptar uma entrada externa para a aplicação.

Um Controller deve:

receber a requisição;

utilizar DTOs quando aplicável;

realizar conversões necessárias;

chamar o contrato/caso de uso apropriado;

transformar o resultado em uma resposta externa.

Controllers não devem implementar regras de negócio.

Evitar:

Controller
├── regra de negócio
├── acesso direto ao banco
├── processamento complexo
└── chamada direta de infraestrutura


Preferir:

Controller
↓
Port / Service
↓
Business Logic

7. DTOs

DTOs externos devem ficar em:

adapter/external/dto/


DTOs representam o contrato externo da aplicação.

Não tratar automaticamente DTO, Model e Entity como o mesmo objeto.

Quando houver transformação entre representações, utilizar o mecanismo de conversão existente.

8. Ports

Ports representam contratos da aplicação.

Antes de criar uma nova Port:

Procure uma Port existente com responsabilidade semelhante.

Verifique se ela pode ser reutilizada.

Crie uma nova Port somente quando existir uma responsabilidade diferente.

Uma Port deve representar uma capacidade ou contrato, e não um detalhe de tecnologia.

Preferir:

AccountRepository
PaymentGateway
TransactionRepository


Evitar contratos fortemente acoplados à tecnologia:

PostgresAccountRepositoryInterface
JpaRepositoryInterface


quando não houver justificativa arquitetural.

9. Driven Adapters

Implementações de comunicação com recursos externos devem ficar em:

adapter/driven/


Exemplos:

Database
External API
Message Broker
Cache
File System


O Service não deve depender diretamente de uma implementação concreta de infraestrutura quando existir uma Port para representar essa dependência.

Preferir:

Service
↓
Port
↓
Driven Adapter
↓
External System

10. Services

Services representam a execução/orquestração dos casos de uso.

Antes de criar um Service:

Identifique o módulo correto.

Procure Services semelhantes.

Preserve o padrão de nomenclatura existente.

Mantenha as regras no nível arquitetural correto.

Evite acessar diretamente detalhes externos quando existir uma Port.

Não transformar Services em classes genéricas para armazenar qualquer tipo de lógica.

11. Converter

Converters devem ser utilizados para transformação entre representações.

Exemplo:

DTO
↓
Converter
↓
Model


ou:

Entity
↓
Converter
↓
Model


Converters não devem ser utilizados para esconder regras de negócio.

12. Entity e Model

A distinção exata entre entity e model deve seguir o padrão já existente no código.

Não assumir que:

Entity = Model = DTO


Antes de criar uma nova classe nesses diretórios:

procure classes semelhantes;

analise como elas são utilizadas;

mantenha a mesma convenção.

Não crie uma nova representação do mesmo conceito sem necessidade.

13. Common

O diretório:

common/


deve conter somente componentes realmente compartilhados.

Atualmente existe:

common/
└── exception/


Não utilizar common como depósito genérico para:

utils
helpers
services
misc


sem uma responsabilidade arquitetural clara.

14. Dependências entre módulos

Os módulos:

bankaccount
operationtransation


devem permanecer isolados sempre que possível.

Não acessar indiscriminadamente classes internas de outro módulo.

Antes de criar uma dependência entre módulos:

verifique se ela é realmente necessária;

procure uma abstração existente;

mantenha a dependência explícita;

evite acessar detalhes internos de outro contexto.

15. Testes

Testes devem acompanhar as alterações realizadas.

A estrutura de testes segue:

src/test/java/br/com/objetive/financial/usecase/


com os respectivos módulos:

bankaccount/
operationtransation/


Ao alterar um Service, Controller ou outra regra relevante:

procure testes existentes;

siga o padrão de testes existente;

adicione ou atualize testes quando necessário;

não remova testes apenas para fazer a implementação passar.

16. Não fazer

O agente não deve:

criar uma arquitetura paralela;

mover arquivos sem necessidade;

criar domain/application/infrastructure paralelamente à estrutura atual;

colocar regra de negócio em Controller;

acessar banco diretamente pelo Controller;

acoplar Services diretamente a infraestrutura quando existir Port;

transformar DTO em Entity sem necessidade;

criar abstrações duplicadas;

criar pastas genéricas sem responsabilidade;

modificar vários módulos quando uma alteração localizada for suficiente.

17. Estratégia para implementação

Para uma nova funcionalidade:

1. Identificar o módulo
   ↓
2. Identificar a entrada
   ↓
3. Identificar o caso de uso
   ↓
4. Identificar as Ports
   ↓
5. Implementar o Service
   ↓
6. Implementar Adapters necessários
   ↓
7. Implementar conversões necessárias
   ↓
8. Criar/atualizar testes
   ↓
9. Executar validações

18. Regra de consistência

Quando existir uma implementação semelhante no projeto, utilize-a como referência.

A consistência com o código existente é preferível à introdução de uma nova abordagem.

Antes de criar:

NovaController
NovoService
NovaPort
NovoAdapter


procure primeiro:

Controller semelhante
Service semelhante
Port semelhante
Adapter semelhante

19. Regra para decisões arquiteturais

Se uma alteração exigir mudança estrutural significativa:

não execute a mudança silenciosamente;

identifique o impacto;

preserve o comportamento existente;

documente a decisão arquitetural quando necessário.

20. Checklist final

Antes de concluir uma tarefa:

[ ] Li AGENTS.md
[ ] Li ARCHITECTURE.md
[ ] Identifiquei o módulo correto
[ ] Procurei implementação semelhante
[ ] Coloquei cada classe no diretório correto
[ ] Não criei arquitetura paralela
[ ] Controller não possui regra de negócio
[ ] DTO não possui regra de negócio
[ ] Converter não possui regra de negócio
[ ] Dependências externas estão adequadamente isoladas
[ ] Não criei dependências desnecessárias entre módulos
[ ] Mantive o padrão existente
[ ] Atualizei/criei testes quando necessário
[ ] Executei os testes/validações disponíveis

21. Fonte arquitetural

A descrição detalhada da arquitetura está em:

docs/architecture/ARCHITECTURE.md


Este arquivo (AGENTS.md) contém as regras operacionais que devem ser seguidas pelo agente durante o desenvolvimento.