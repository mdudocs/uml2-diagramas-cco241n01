# Diagrama de Implantação (Deployment)

**Grupo 4:** Maria Eduarda, João e Lucas

## Definição

O diagrama de implantação é um diagrama **estrutural** da UML que mostra a **arquitetura física** de um sistema: em que máquinas o software é instalado, em que ambientes de execução ele roda e por quais conexões essas máquinas se comunicam.

É o único diagrama da UML que trata de hardware. Os demais descrevem o software; este descreve onde o software mora depois de pronto. Ele responde a quatro perguntas:

1. **Onde** cada parte do sistema é instalada? (os nós)
2. **O que** exatamente é implantado em cada nó? (os artefatos)
3. **Como** os nós se comunicam? (os caminhos de comunicação e seus protocolos)
4. **Quantas** instâncias existem de cada nó? (a multiplicidade, que revela escala, redundância e pontos únicos de falha)

## Quando usar

O diagrama entra mais tarde no projeto — na fase de **projeto (design)**, junto com o de Componentes — e permanece útil durante a **operação**, como documentação.

- **Ao definir a arquitetura:** antes de codificar, para decidir o que roda onde e dimensionar a infraestrutura a ser contratada.
- **Ao documentar o sistema:** para que novos membros do time entendam a topologia sem precisar acessar o servidor.
- **Ao planejar o deploy:** para alinhar ambientes, escala e redundância com a equipe de operação.

É especialmente útil em sistemas distribuídos, web, mobile e em nuvem. Em um programa que roda em uma única máquina, agrega pouco.

## Elementos da notação

| Símbolo | Nome | O que representa |
|---|---|---|
| Caixa 3D com `«device»` | Nó (dispositivo) | Recurso de computação físico: servidor, VM na nuvem, smartphone, tablet, roteador |
| Caixa 3D com `«executionEnvironment»` | Ambiente de execução | Software que hospeda e executa outros: JVM, Docker, Tomcat, Node.js, SGBD, navegador, SO |
| Caixa dentro de caixa | Aninhamento | Um nó roda dentro de outro (ex.: Docker dentro do servidor) |
| Retângulo com `«artifact»` | Artefato | Arquivo resultante do build instalado no nó: `.jar`, `.apk`, `.sql`, imagem de container, `.yml` |
| Linha sólida, sem seta, com estereótipo | Caminho de comunicação | Conexão bidirecional entre nós; o estereótipo indica o protocolo (`«HTTPS»`, `«JDBC»`, `«TCP/IP»`) |
| `1`, `0..1`, `1..*` nas pontas da linha | Multiplicidade | Quantas instâncias de cada nó participam da conexão |
| Seta tracejada com `«deploy»` | Dependência de implantação | Liga um artefato ao nó em que é instalado (alternativa a desenhá-lo dentro da caixa) |
| Seta tracejada com `«manifest»` | Manifestação | Indica que um artefato é a forma física de um componente lógico |

## Exemplo prático

O grupo criou o **FametroFood**, um app fictício de delivery com quatro papéis: o **cliente** faz pedidos pelo celular, o **restaurante** recebe os pedidos em tempo real num tablet, a **plataforma** (API central) orquestra pedidos e autenticação, e o **pagamento** é feito por um serviço externo (PIX ou cartão).

O diagrama mostra cinco nós:

- **Smartphone do cliente** (`1..*`) — Android 14 executando `FametroFood.apk`
- **Tablet do restaurante** (`1..*`) — Navegador Chrome executando `painel-web.js`
- **Servidor de aplicação** (`1`) — AWS EC2 t3.medium com JVM 17 + Spring Boot, executando `api-pedidos.jar` e `auth-service.jar`
- **Servidor de banco** (`1`) — PostgreSQL 16 com `schema-pedidos.sql`
- **Gateway de pagamento** (`1`) — serviço externo de terceiros

As conexões usam `«HTTPS/REST»` (smartphone ↔ servidor), `«WebSocket»` (tablet ↔ servidor), `«JDBC/TCP»` (servidor ↔ banco) e `«HTTPS»` (servidor ↔ gateway). Nenhum cliente acessa o banco diretamente: toda comunicação passa pelo servidor de aplicação. A multiplicidade `1..*` clientes para `1` servidor evidencia um **ponto único de falha** — se o servidor cair, o app inteiro para —, o que já abre a discussão sobre redundância ainda na fase de projeto.

## Comparação com Caso de Uso, Classe e Sequência

| Diagrama | Pergunta que responde | Tipo | O que aparece nele |
|---|---|---|---|
| Caso de Uso | Quem usa o sistema e para quê? | Comportamental | Atores e funcionalidades |
| Classe | Como os dados se organizam? | Estrutural (lógico) | Classes, atributos, relações |
| Sequência | Em que ordem as mensagens acontecem? | Comportamental | Objetos e mensagens no tempo |
| **Implantação** | **Onde o software roda de verdade?** | **Estrutural (físico)** | **Nós, artefatos e conexões** |

**Semelhanças:** assim como o diagrama de Classe, é estrutural e estático — mostra uma "fotografia" do sistema, não uma linha do tempo como o de Sequência. Também usa a mesma gramática de caixas, linhas, estereótipos e multiplicidade.

**Diferenças:** suas caixas não são conceitos do software, e sim máquinas e arquivos. Enquanto Caso de Uso, Classe e Sequência ficam no mundo lógico, o diagrama de implantação é o único que trata de hardware, rede e infraestrutura.

> **Atenção:** não confundir com o diagrama de **Componentes**, que mostra as peças lógicas do software (de que módulos o sistema é feito). O de Implantação mostra onde esses módulos rodam. Os dois se encontram no artefato: `api-pedidos.jar` `«manifest»` o componente *Pedidos* e é `«deploy»` no *Servidor de Aplicação*.

## Apresentação

[Apresentação (PowerPoint)]
(apresentacao.pptx) — o exemplo prático está no slide 10.

## Referências

- OBJECT MANAGEMENT GROUP. *OMG Unified Modeling Language (UML), Version 2.5.1*. 2017. Disponível em: https://www.omg.org/spec/UML/2.5.1
- BOOCH, G.; RUMBAUGH, J.; JACOBSON, I. *UML: Guia do Usuário*. 2. ed. Rio de Janeiro: Elsevier, 2012.
- FOWLER, M. *UML Essencial: um breve guia para a linguagem-padrão de modelagem de objetos*. 3. ed. Porto Alegre: Bookman, 2005.
- GUEDES, G. T. A. *UML 2: uma abordagem prática*. 3. ed. São Paulo: Novatec, 2018.
