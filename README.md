# Resumo: Arquitetura Limpa

## Índice
- [Princípios de Design](#princípios-de-design)
- [SRP: O Princípio da Responsabilidade Única](#srp-o-princípio-da-responsabilidade-única)
- [OCP: Princípio Aberto-Fechado](#ocp-princípio-aberto-fechado)
- [LSP: Princípio da Substituição de Liskov](#lsp-princípio-da-substituição-de-liskov)
- [ISP: Princípio da Segregação de Interfaces](#isp-princípio-da-segregação-de-interfaces)
- [DIP: Princípio da Inversão de Dependência](#dip-princípio-da-inversão-de-dependência)

---

## Princípios de Design

O design de software deve seguir princípios que garantem código limpo, flexível e de fácil manutenção. Os princípios SOLID ajudam a estruturar sistemas modulares e desacoplados:

- **S — Princípio da Responsabilidade Única (SRP)**: uma classe deve ter apenas uma razão para mudar, ou seja, deve ter uma única responsabilidade bem definida.
- **O — Princípio Aberto-Fechado (OCP)**: classes e módulos devem estar abertos para extensão, mas fechados para modificação, evitando mudanças diretas no código existente ao adicionar novas funcionalidades.
- **L — Princípio da Substituição de Liskov (LSP)**: subtipos devem poder substituir seus tipos base sem alterar o comportamento esperado, garantindo herança correta e sem efeitos colaterais inesperados.
- **I — Princípio da Segregação de Interfaces (ISP)**: interfaces devem ser específicas e coesas, evitando que sejam forçadas a implementar métodos que não utilizam.
- **D — Princípio da Inversão de Dependência (DIP)**: o código deve depender de abstrações e não de implementações concretas, promovendo o desacoplamento entre módulos.

Esses princípios garantem código organizado, facilitando testes e manutenção, e tornam o sistema mais resiliente a mudanças ao longo do tempo.

---

## SRP: O Princípio da Responsabilidade Única

O Princípio da Responsabilidade Única (SRP) diz que um módulo ou classe deve ser responsável por um único ator ou papel. Isso significa que ele deve cuidar apenas de uma coisa específica, sem misturar responsabilidades de diferentes partes do sistema.

**Exemplo:**

Imagine um sistema que tem uma classe para gerenciar usuários e outra para notificações:

- A **classe de usuários** deve ser responsável apenas por lidar com a criação, edição e exclusão de usuários. Ela não deve se preocupar em enviar e-mails ou notificar alguém.
- A **classe de notificações** deve ser responsável apenas por enviar notificações, como e-mails ou mensagens.

Se a classe de usuários também tivesse que enviar notificações, ela estaria misturando responsabilidades, o que violaria o SRP. Cada classe ou módulo deve ter um único foco, relacionado a um único ator — seja o usuário ou o sistema de notificações — tornando o código mais claro e fácil de modificar.

---

## OCP: Princípio Aberto-Fechado

O Princípio Aberto-Fechado (Open-Closed Principle) estabelece que **as classes devem estar abertas para extensão, mas fechadas para modificação**. Ou seja, devemos ser capazes de adicionar novas funcionalidades ao sistema sem alterar o código existente.

Esse princípio ajuda a evitar efeitos colaterais inesperados ao modificar o comportamento de classes já existentes, promovendo maior estabilidade e manutenibilidade no sistema.

**Como aplicar?**

- Use **interfaces** ou **classes abstratas** para definir comportamentos genéricos.
- Crie **novas implementações** para adicionar funcionalidades, sem modificar o código base.

**Exemplo:**

Imagine que você tem um sistema de envio de mensagens:

```ts
interface Notificador {
  enviar(mensagem: string): void;
}

class EmailNotificador implements Notificador {
  enviar(mensagem: string): void {
    console.log(`Enviando e-mail: ${mensagem}`);
  }
}

class Mensageiro {
  constructor(private notificador: Notificador) {}

  processar(mensagem: string) {
    this.notificador.enviar(mensagem);
  }
}
```

Se quiser adicionar um **SMSNotificador**, basta criar uma nova classe implementando a interface, sem alterar o `Mensageiro`:

```ts
class SMSNotificador implements Notificador {
  enviar(mensagem: string): void {
    console.log(`Enviando SMS: ${mensagem}`);
  }
}
```

Isso segue o OCP: o código existente não foi modificado, apenas **estendido** com novos comportamentos.

---
## LSP: Princípio da Substituição de Liskov
O Princípio da Substituição de Liskov (Liskov Substitution Principle) afirma que uma subclasse deve ser substituível por sua superclasse sem alterar o comportamento esperado do sistema. Ou seja, os objetos das subclasses devem poder ser usados no lugar de objetos da classe base sem causar erros ou comportamentos inesperados.

Esse princípio garante que o sistema continue funcionando corretamente mesmo quando subclasses são usadas em vez das classes pai. Violá-lo geralmente significa que há um problema na hierarquia de herança ou que a subclasse está alterando regras importantes da superclasse.

Exemplo clássico:

Considere as classes Retângulo e Quadrado.

class Retangulo {
  constructor(public largura: number, public altura: number) {}

  setLargura(l: number) {
    this.largura = l;
  }

  setAltura(h: number) {
    this.altura = h;
  }

  getArea(): number {
    return this.largura * this.altura;
  }
}

class Quadrado extends Retangulo {
  setLargura(l: number) {
    this.largura = l;
    this.altura = l;
  }

  setAltura(h: number) {
    this.largura = h;
    this.altura = h;
  }
}
Ao usar um Quadrado onde se espera um Retângulo, o comportamento pode ser alterado de forma inesperada, pois as operações setLargura e setAltura afetam ambos os lados. Isso viola o LSP, pois o Quadrado não mantém as expectativas de comportamento do Retângulo.

Como aplicar corretamente:

Evite sobrescrever métodos de forma que mudem o comportamento da classe base.

Prefira composição em vez de herança quando as restrições entre as classes são muito diferentes.

Crie abstrações mais genéricas se necessário, garantindo que todas as implementações sigam o mesmo contrato.

Conclusão:
O LSP é essencial para garantir reutilização segura de código e evitar bugs difíceis de detectar. Ele promove heranças corretas e interfaces consistentes, mantendo a integridade do sistema ao longo do tempo.

---

## ISP: Princípio da Segregação de Interfaces

O Princípio da Segregação de Interfaces afirma que **nenhuma classe deve ser forçada a depender de métodos que não utiliza**. Em outras palavras, uma classe não deve ser obrigada a implementar interfaces extensas e genéricas que a forcem a lidar com responsabilidades que não lhe dizem respeito.

Quando uma classe depende de outra que possui muitos métodos irrelevantes para seu contexto, isso pode gerar **acoplamento desnecessário**, tornando o sistema mais frágil. Pequenas alterações em uma interface podem afetar várias classes, mesmo aquelas que não utilizam os métodos alterados, aumentando o risco de **erros inesperados**.

Ao aplicar o ISP corretamente, quebramos interfaces grandes em **interfaces menores e mais específicas**, promovendo um código mais coeso, testável e fácil de manter. Dessa forma, cada classe depende apenas do que realmente precisa, reduzindo o impacto de mudanças e melhorando a modularidade do sistema.

---

---

## DIP: Princípio da Inversão de Dependência

O Princípio da Inversão de Dependência afirma que **módulos de alto nível (regras de negócio)** não devem depender de **módulos de baixo nível (detalhes de implementação)**. Ambos devem depender de **abstrações** (interfaces, por exemplo).

### Mas o que isso significa na prática?

Imagine que você está desenvolvendo um aplicativo de envio de mensagens. Sua regra de negócio diz que “ao acontecer determinado evento, deve ser enviado um aviso ao usuário”.

Sem seguir o DIP, você poderia escrever algo assim:

```ts
class Notificador {
  enviarEmail(usuario: Usuario) {
    // lógica para enviar e-mail
  }
}
```

Nesse caso, a lógica depende diretamente de um serviço de e-mail. Se um dia quiser enviar por SMS ou WhatsApp, terá que modificar o código da regra — o que quebra o princípio de separação de responsabilidades e aumenta o risco de erros.

Aplicando o DIP:

```ts
interface INotificador {
  notificar(usuario: Usuario): void;
}

class NotificadorEmail implements INotificador {
  notificar(usuario: Usuario) {
    // envia por e-mail
  }
}

class ServicoDeAviso {
  constructor(private notificador: INotificador) {}

  executar(usuario: Usuario) {
    this.notificador.notificar(usuario);
  }
}
```
Com isso, o ServicoDeAviso não sabe como a notificação é enviada — ele apenas sabe que precisa chamar o método notificar. Isso torna o sistema mais flexível e desacoplado.

Na prática, não é possível evitar totalmente a dependência de detalhes, em algum ponto do sistema, será necessário usar uma implementação concreta (por exemplo, criar uma instância de NotificadorEmail). No entanto, a ideia é concentrar esses detalhes nas bordas da aplicação, como em uma camada de infraestrutura. Assim, a parte central do sistema continua limpa, estável e fácil de testar ou modificar.


