Financial — Architecture
1. Objetivo

Este documento descreve a arquitetura do projeto financial e deve ser utilizado como referência durante o desenvolvimento e manutenção da aplicação.

A aplicação utiliza conceitos de Hexagonal Architecture / Ports and Adapters, organizados por módulos funcionais.

A arquitetura deve proteger as regras da aplicação dos detalhes de infraestrutura e das tecnologias externas.

2. Estrutura arquitetural

A estrutura principal da aplicação é:

financial
│
├── common
│   └── exception
│
└── usecase
├── bankaccount
└── operationtransation


Os módulos funcionais ficam dentro de:

usecase/


Cada módulo possui suas próprias estruturas internas.

3. Módulos

Atualmente existem:

usecase/
├── bankaccount/
└── operationtransation/

3.1 Bank Account

Local:

usecase/bankaccount/


Estrutura:

bankaccount/
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
└── factory/

3.2 Operation Transaction

Local:

usecase/operationtransation/


Estrutura:

operationtransation/
├── adapter/
│   ├── driven/
│   └── external/
│       ├── controller/
│       └── dto/
├── entity/
├── enumeration/
├── model/
├── port/
└── service/

4. Modelo Hexagonal

A arquitetura pode ser representada conceitualmente como:

                    EXTERNAL WORLD
                          │
                          ▼
               ┌──────────────────┐
               │ External Adapter │
               │    Controller    │
               └────────┬─────────┘
                        │
                        ▼
                  ┌───────────┐
                  │   Port    │
                  └─────┬─────┘
                        │
                        ▼
               ┌─────────────────┐
               │     Service     │
               │    Use Case     │
               └────────┬────────┘
                        │
                ┌───────┴────────┐
                ▼                ▼
             Model            Entity
                │                │
                └───────┬────────┘
                        │
                        ▼
                     Port
                        │
                        ▼
               ┌─────────────────┐
               │ Driven Adapter  │
               └────────┬────────┘
                        │
                        ▼
                EXTERNAL SYSTEM


A implementação concreta dos adapters deve permanecer isolada do núcleo da aplicação.

5. External Adapter

Local:

adapter/external/


Responsabilidade:

receber dados externos;

adaptar protocolos externos;

converter dados externos;

chamar os contratos da aplicação;

retornar respostas ao sistema externo.

Estrutura atual:

adapter/external/
├── controller/
└── dto/


Exemplo conceitual:

HTTP Request
│
▼
Controller
│
▼
DTO
│
▼
Port / Service

6. Controller

Local:

adapter/external/controller/


O Controller representa uma porta de entrada da aplicação.

Sua responsabilidade é adaptar o protocolo externo para o modelo interno.

Responsabilidades

receber requisições;

interpretar parâmetros;

utilizar DTOs;

realizar conversões;

chamar o caso de uso;

construir a resposta.

Não é responsabilidade do Controller

implementar regras de negócio;

executar consultas diretamente no banco;

realizar operações complexas de domínio;

implementar lógica de persistência;

implementar integrações externas diretamente.

7. DTO

Local:

adapter/external/dto/


DTOs representam estruturas de dados utilizadas na comunicação externa.

Exemplo:

External Request
│
▼
DTO
│
▼
Converter
│
▼
Model


DTOs não devem ser utilizados como substitutos automáticos de Models ou Entities.

8. Driven Adapter

Local:

adapter/driven/


Driven Adapters representam implementações de comunicação com recursos externos.

Podem representar, dependendo da aplicação:

Database
External APIs
Messaging
Cache
File System


Fluxo:

Service
│
▼
Port
│
▼
Driven Adapter
│
▼
External System


O Service deve conhecer o contrato, não necessariamente a implementação concreta.

9. Ports

Local:

port/


Ports representam contratos utilizados para comunicação entre o núcleo da aplicação e componentes externos.

Uma Port deve representar uma capacidade da aplicação.

Exemplo:

AccountRepository
TransactionRepository
PaymentGateway


A implementação concreta pertence ao Adapter.

Modelo:

             ┌───────────────┐
             │    Service    │
             └───────┬───────┘
                     │
                     ▼
             ┌───────────────┐
             │     Port      │
             └───────▲───────┘
                     │
             ┌───────┴───────┐
             │    Adapter    │
             └───────────────┘

10. Service

Local:

service/


Services representam a execução e orquestração dos casos de uso.

Responsabilidades:

coordenar a execução da operação;

aplicar ou acionar regras de negócio;

utilizar Models/Entities;

utilizar Ports;

coordenar dependências necessárias.

Services não devem possuir dependência desnecessária diretamente com infraestrutura.

11. Model

Local:

model/


Models representam estruturas utilizadas pelo contexto funcional.

A distinção exata entre Model e Entity deve seguir as convenções já existentes no código.

Não assumir:

Model == Entity


nem:

Model == DTO


Conversões entre representações devem utilizar os mecanismos definidos pelo módulo.

12. Entity

Local:

entity/


Entities representam conceitos persistentes ou de negócio conforme o padrão adotado pelo módulo.

Regra importante

Antes de criar uma nova Entity, consultar as classes existentes para determinar como o projeto diferencia:

Entity
Model
DTO


Essa distinção deve ser preservada.

13. Converter

Local:

converter/


Converters são responsáveis pela transformação entre representações.

Exemplos:

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


Converters não devem conter regras de negócio.

14. Enumeration

Local:

enumeration/


Contém enums específicos do módulo.

Enums devem permanecer próximos ao contexto funcional ao qual pertencem.

Somente mover um enum para common quando existir uma necessidade real de compartilhamento.

15. Factory

Local:

service/factory/


Factories são utilizadas para criação controlada de objetos quando isso for necessário.

Não utilizar Factory apenas para esconder lógica que pertence ao Service ou ao domínio.

16. Common

Local:

common/


O diretório common contém componentes compartilhados entre diferentes partes da aplicação.

Atualmente:

common/
└── exception/


Somente componentes realmente compartilhados devem ser colocados nesse diretório.

17. Direção das dependências

A direção arquitetural esperada é:

External World
│
▼
External Adapter
│
▼
Port
│
▼
Service
│
▼
Model / Entity


Para comunicação de saída:

Model / Entity
│
▼
Service
│
▼
Port
│
▼
Driven Adapter
│
▼
External System


A implementação externa não deve contaminar o núcleo da aplicação.

18. Dependências proibidas
    Controller → Database

Não permitido:

Controller
│
▼
Database

Controller → Infrastructure

Evitar:

Controller
│
▼
Infrastructure


O Controller deve utilizar os contratos da aplicação.

Service → Concrete Infrastructure

Evitar:

Service
│
▼
PostgresRepository


Preferir:

Service
│
▼
Repository Port
│
▼
PostgresRepository

19. Isolamento dos módulos

Os módulos:

bankaccount
operationtransation


representam contextos funcionais diferentes.

Evitar dependências diretas sobre detalhes internos de outro módulo.

Não fazer:

bankaccount
│
└──> operationtransation.InternalClass


sem uma justificativa arquitetural.

Quando dois módulos precisarem se comunicar, deve existir um contrato claramente definido.

20. Criação de um novo módulo

Um novo módulo somente deve ser criado quando existir uma nova responsabilidade funcional suficientemente independente.

Estrutura inicial:

usecase/
└── newmodule/
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


Não criar todas as pastas obrigatoriamente.

Criar somente as estruturas necessárias.

21. Criação de um novo caso de uso

O fluxo arquitetural recomendado é:

External Request
│
▼
Controller
│
▼
DTO
│
▼
Converter
│
▼
Port / Service
│
▼
Business Logic
│
▼
Port
│
▼
Driven Adapter
│
▼
External System


O fluxo real deve seguir os padrões encontrados nas implementações existentes.

22. Testes

Os testes estão organizados em:

src/test/java/br/com/objetive/financial/usecase/


com os contextos:

bankaccount/
operationtransation/


A estrutura dos testes deve acompanhar a estrutura dos módulos.

Ao modificar um componente, procurar primeiro testes existentes do mesmo tipo.

23. Evolução da arquitetura

Mudanças arquiteturais devem ser feitas de maneira incremental.

Antes de introduzir uma nova abordagem:

verificar se a arquitetura existente já resolve o problema;

procurar implementações semelhantes;

avaliar impacto nos módulos existentes;

manter compatibilidade quando possível;

documentar mudanças relevantes.

24. Regra para agentes de IA

Um agente de IA deve interpretar este projeto como uma arquitetura existente, não como um projeto novo.

Portanto:

NÃO:
"Qual arquitetura eu escolheria para este projeto?"

SIM:
"Como implemento esta funcionalidade respeitando a arquitetura existente?"


Antes de criar código:

1. Identificar o módulo.
2. Identificar a responsabilidade.
3. Procurar implementação semelhante.
4. Identificar a camada correta.
5. Verificar Ports e Adapters envolvidos.
6. Implementar seguindo o padrão existente.
7. Criar/alterar testes.

25. Princípio arquitetural

A principal preocupação da arquitetura é separar:

REGRAS DA APLICAÇÃO


de:

DETALHES EXTERNOS


Representação:

                 ┌───────────────────────┐
                 │    External World     │
                 │                       │
                 │ HTTP / DB / API / MQ  │
                 └───────────┬───────────┘
                             │
                          Adapter
                             │
                             ▼
                           Port
                             │
                             ▼
                         Service
                             │
                             ▼
                      Model / Entity


Tecnologias externas são detalhes de implementação.

As regras da aplicação devem permanecer protegidas dessas dependências.

26. Regra de consistência

A arquitetura existente possui valor por sua consistência.

Ao implementar uma nova funcionalidade, o padrão já utilizado no projeto deve ser preferido a uma abordagem nova.

Se houver dúvida:

Procurar implementação semelhante
↓
Entender o padrão utilizado
↓
Reutilizar o padrão
↓
Implementar a nova funcionalidade


Não introduzir complexidade arquitetural sem necessidade.

27. Estado atual e pontos a validar

A estrutura de diretórios permite identificar a organização arquitetural, porém alguns papéis precisam ser confirmados através do código-fonte.

Especialmente:

entity/
model/
port/
adapter/driven/
service/


A distinção definitiva entre Entity, Model e outros componentes deve ser baseada nas implementações existentes.

Este documento deve ser atualizado quando essas responsabilidades forem confirmadas ou quando a arquitetura evoluir.

## Tecnologia

- Java: 25
- Arquitetura: Hexagonal / Ports and Adapters
- Build: Maven/Gradle

- A descrição orientada da liguagem está em:

docs/architecture/JAVA.md