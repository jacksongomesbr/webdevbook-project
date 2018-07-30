# CRUD completo do noticias-angular {#sec:noticias-angular-crud-telas}

O Capítulo [-@sec:noticias-angular] apresenta uma implementação do **noticias-angular**, um software de gerenciamento de notícias desenvolvido como um projeto Angular. Este capítulo tem o objetivo de apresentar uma nova versão deste projeto que traz novas funcionalidades:

* listar notícias recentes de forma ordenada
* apresentar notícia para leitura
* listar todas as notícias (com pesquisa)
* cadastrar notícia
* editar notícia
* excluir notícia

Este capítulo apresenta:

* como criar uma interface CRUD baseada em lógica de troca de visibilidade de telas
* como não utilizar apenas o Controller para lógica de negócio
* como representar a navegação de telas usando um Diagrama de Estados da UML (como um diagrama de navegação)
* o software **compodoc**, utilizado para gerar uma documentação do código
* como utilizar métodos importantes da classe `Array`
* como funciona a classe `Date`, alguns dos seus métodos importantes e como o browser representa uma data no campo de formulário
* recurso de **Pipes** do Angular


## Clonar e executar localmente

Este capítulo está relacionado com o software **noticias-angular**, branch "crud-telas"^[O repositório do branch "crud-telas" está disponível em <https://github.com/jacksongomesbr/webdevbook-noticias-angular/tree/crud-telas> e você pode vê-lo em execução acessando <https://stackblitz.com/github/jacksongomesbr/webdevbook-noticias-angular/tree/crud-telas>]. Antes de prosseguir, clone e execute o projeto localmente. 


## CRUD

**CRUD** é um acrônimo bastante conhecido em desenvolvimento de software e significa:

* **C - CREATE**: funcionalidade de cadastrar
* **R - RETRIEVE**: recuperar (consultar)
* **U - UPDATE**: atualizar
* **D - DELETE**: deletar (excluir)

Um software que fornece funcionalidades CRUD, geralmente, proporciona uma interface padronizada, que contém:

* uma tela que apresenta a lista de todos os registros de dados (elementos ou objetos) contendo, geralmente, funcionalidade de pesquisa/busca
* uma tela que permite cadastrar
* uma tela que permite editar (geralmente semelhante à tela de cadastro ou usando a mesma tela para um propósito diferente)
* e, opcionalmente, uma tela que permite acessar os detalhes do registro de dados de forma apenas para leitura

Esses recursos são implementados no branch "crud-telas" do **noticias-angular** e são apresentados neste capítulo.


## Documentação do projeto

O capítulo atual utiliza o software **compodoc**, que interpreta o código-fonte do projeto e gera uma documentação que pode ser disponibilizada on-line [@compodochome]. Há, inclusive, uma versão on-line da documentação do projeto **noticias-angular**^[A versão on-line da documentação do **noticias-angular** pode ser acessada em <https://jacksongomesbr.github.io/webdevbook-noticias-angular/>. Entretanto, importante notar, a versão on-line é sincronizada com o branch "master", o que pode torná-la diferente do exposto neste capítulo].

A documentação de um projeto é um passo importante na maturidade do código e do programador (senão de todos os envolvidos no projeto). Documentar código é uma boa prática de desenvolvimento de software porque o desenvolvedor precisa estabelecer uma descrição clara e concisa de cada pequeno elemento do software, desde atributos a métodos. O **compodoc** adiciona recursos nesse sentido porque ele é voltado para projetos Angular e, por isso, gera uma documentação com base nessa estrutura, por exemplo, interpretando módulos e componentes.

Procure manter essa prática de documentar seus códigos-fontes.


## O projeto

O branch "crud-telas" do **noticias-angular** não é muito diferente do branch "inicio" em termos de estrutura, como ilustra a [@fig:noticias-angular-crud-telas-estrutura], mas muda bastante em termos de funcionalidades.

```{#fig:noticias-angular-crud-telas-estrutura .plantuml caption="Estrutura do projeto angular-noticias, branch crud-telas" format="eps"}
@startuml

class AppModule <<NgModule>> {
}

class Noticia <<Model>> {
    id: number
    titulo: string
    conteudo: string
    autor: string
    emailDoAutor: string
    data: Date
    Noticia(id, titulo, conteudo, autor, emailDoAutor, data)
}

class AppComponent <<Component>> {
    tela: string
    noticias: Array
    titulo: string
    conteudo: string
    autor: string
    emailDoAutor: string
    data: Date
    leituraNoticia: Noticia
    editarNoticia: Noticia
    listaPesquisa: string
    ngOnInit()
    salvar()
    mostrar(noticia: Noticia)
    fechar()
    noticiasPublicadas()
    irPara(nome: string)
    noticiasParaLista()
    date2str(d: Date)
    editar(noticia: Noticia)
    excluir(noticia: Noticia)
    cancelarEdicao()
}

AppModule --> angular.platformbrowser.BrowserModule: imports
AppModule --> angular.forms.FormsModule: imports
AppModule -> AppComponent: declarations
AppModule -> AppComponent: bootstrap
AppComponent -> Noticia: usa

@enduml
```

A estrutura inclui a classe `Noticia`, que é utilizada no `AppComponent`. As seções seguintes continuam apresentando os elementos dessa estrutura.

## Classe `Noticia`

Uma boa prática de programação TypeScript é utilizar organizar o código, declarar tipos de dados e, para isso, o branch "crud-telas" implementa a classe `Noticia`, no arquivo `src/app/noticia.model.ts`. Não há algo de especial em o nome do arquivo conter "model" e também não é uma recomendação do Angular. Entretanto, escolhi manter o nome dessa forma para seguir a mesma lógica de organização do projeto que já vem sendo utilizada.

Assim, a classe `Noticia` implementa a classe que representa o Model de Notícia e é utilizada no `AppComponent`. A implementação demonstra algumas práticas interessantes, como tratar os dados no construtor e fornecer um método que verifica se a notícia está ou não publicada (ao invés de fazer isso no `AppComponent`). Essa é uma prática interessante para organizar o código-fonte do projeto.

Duas partes importantes do código estão no construtor e no método `estahPublicada()`. Primeiro, o construtor:

```typescript
constructor(id: number, titulo: string, conteudo: string, autor: string, emailDoAutor: string, data: Date) {
    this.id = id;
    this.titulo = titulo;
    this.conteudo = conteudo;
    this.autor = autor;
    this.emailDoAutor = emailDoAutor;
    if (data) {
        if (typeof data === 'string') {
            this.data = new Date(data);
        } else {
            this.data = data;
        }
    } else {
        this.data = null;
    }
}
```

O código do construtor realiza um tratamento dos valores dos parâmetros a partir da linha 7, mais especificamente, verificando se o tipo de dados é `string` (quando chama o construtor da classe `Date` para fazer um parser da entrada e criar um objeto) e se o valor é `null`. Voltaremos a falar desse tratamento na seção que apresenta a funcionalidade de cadastrar notícia.

Outra parte importante da classe `Noticia` é o método `estahPublicada()`:

```typescript
estahPublicada(): boolean {
     if (!this.data) {
        return false;
    }
     const dataAtual = new Date();
    if (this.data > dataAtual) {
        return false;
    }
    return true;
}
```

O código do método `estahPublicada()` considera que:

* se a data da notícia não está definida, então não está publicada (linhas 2 e 3)
* se a data da notícia é maior que a data atual, então a notícia não está publicada (linhas 5-8)
* caso contrário, a notícia está publicada

É importante notar a utilização da classe `Date` nesses códigos. `Date` faz parte da referência de objetos do JavaScript [@w3sjsandhtmldomref] e também está disponível no TypeScript.


## AppComponent

O Controller do `AppComponent` possui um número mais extenso de atributos e métodos para implementar as funcionalidades necessárias, que são apresentadas detalhadamente a seguir. 

### Exibir telas diferentes

Por ter muitas funcionalidades, é necessário adotar alguma organização viusal, ou seja, trabalhar a interface gráfica com o usuário. Para esse projeto, achei interessante utilizar o conceito de "telas" e trocar sua visibilidade conforme determinadas situações. 

As telas são:

* **home**: apresenta as notícias recentes de forma ordenada
* **leitura**: apresenta o conteúdo de uma notícia
* **lista**: apresenta a lista de todas as notícias, com pesquisa
* **cadastro**: apresenta o formulário de cadastro
* **edicao**: apresenta o formulário de cadastro no modo de edição

A implementação desse recurso começa pelo atributo `tela` e do método `irPara()` do Controller:

```typescript
irPara(nome) {
    this.tela = nome;
    if (nome === 'cadastro') {
        this.editarNoticia = null;
        this.titulo = null;
        this.conteudo = null;
        this.autor = null;
        this.emailDoAutor = null;
        this.data = null;
    }
}
```

O código do método `irPara()` usa o parâmetro `tela` para trocar o valor do atributo `tela` e, se for a tela "cadastro", realiza um código adicional, redefinindo para `null` os valores dos atributos `editarNoticia`, `titulo`, `conteudo`, `autor`, `emailDoAutor` e `data`.

No Template há uma estrutura composta por cabeçalho (`nav`) e de vários elementos `div`. No cabeçalho há botões que, ao serem clicados, chamam o método `irPara()` conforme cada necessidade de mudança de tela. Aos `div` foram aplicadas diretivas `NgIf` com expressões baseadas no atributo `tela`:

```html
<h1>Notícias::Angular</h1>
<hr>
<nav>
  <button (click)="irPara('home')">Home</button> |
  <button (click)="irPara('lista')">Todas as notícias</button> |
  <button (click)="irPara('cadastro')">Cadastrar</button>
</nav>
<hr>

<div *ngIf="tela == 'home'">
  <h2>Notícias recentes</h2>
...
</div>

<div *ngIf="tela == 'leitura' && leituraNoticia">
...
</div>

<div *ngIf="tela == 'lista'">
  <h2>Todas as notícias</h2>
...
</div>

<div *ngIf="tela == 'cadastro' || tela == 'edicao'">
  <h2>Cadastrar notícia</h2>
...
</div>
```

Assim, o Template tem uma estrutura modular, baseada em elementos `div` que são inseridos ou removidos do DOM utilizando a diretiva `NgIf`. A [@fig:noticias-angular-crud-telas-navegacao] ilustra a navegação entre as telas.

```{#fig:noticias-angular-crud-telas-navegacao .plantuml caption="Diagrama de estados demonstrando navegação das telas do projeto angular-noticias, branch crud-telas" format="eps" width="10cm"}
@startuml

[*] --> home
home : * navegação
home : * lista de notícias recentes

lista : lista de todas as notícias
cadastro : formulário de cadastro
cadastro --> lista : cadastro ok
edicao : formulário de edição
edicao --> lista : cadastro ok
lista --> edicao : notícia da lista

home --> lista : navegação
home --> cadastro : navegação
home --> leitura : notícia da lista
leitura : conteúdo da notícia

leitura --> [*]
lista -->  [*]

@enduml
```

A [@fig:noticias-angular-crud-telas-navegacao] apresenta um **Diagrama de Estados da UML** como forma de demonstrar os caminhos de navegação entre as telas do software. A idéia principal é demonstrar que o caminho passa pela tela **home** e pode prosseguir para **lista**, **cadastro** e **leitura**. Da **lista** pode-se ir para a **edicao** ou terminar (final da navegação). Do **cadastro** volta para **lista** quando um cadastro for concluído com sucesso e mesmo acontece a partir de **edicao**. Por fim, da **leitura** pode terminar. [@lieberman2004uml] demonstra como utilizar Diagrama de Atividade da UML para demonstrar a mesma ideia. Dá uma olhada.

---

**Diagrama de navegação do site ou Sitegrama**

A necessidade de entender os caminhos de navegação no site aumenta na mesma proporção da sua quantidade de páginas (links) ou seções. Para auxiliar o entendimento de um projeto de site é uma prática utilizar um diagrama de navegação (sitegrama), que demonstra, a partir da página inicial, quais as seções do site e a quais páginas elas dão acesso. 

Recomendo leitura adicional de [@amstel2004arquitetura; @corais-sitegrama].

---

### Listar notícias recentes de forma ordenada

O Controller declara o método `noticiasPublicadas()`:

```typescript
noticiasPublicadas() {
    return this.noticias
        .filter(n => n.estahPublicada())
        .sort((a: Noticia, b: Noticia) => {
            if (b.data < a.data) {
                return -1;
            } else if (b.data > a.data) {
                return 1;
            } else {
                return 0;
            }
        });
}
```

O código do método `noticiasPublicadas()` utiliza dois métodos importantes da classe `Array`: `filter()` e `sort()`. No TypeScript esses métodos aceitam como parâmetro uma função que usa uma sintaxe diferente, a **arrow function** (ou **lâmbda**). A sintaxe:

```{style=nonumber}
parâmetros => corpo
```

é interpretada assim:

* `parâmetros` contém uma lista de parâmetros, com tipos ou não
* `corpo` contém o corpo da função, que geralmente retorna um valor

Quando o corpo pode ser reduzido a uma linha não é necessário utilizar chaves duplas e nem a palavra `return`.

O método `filter()` aplica um filtro (**predicado**) aos elementos do `Array`, retornando apenas aqueles para os quais o filtro é verdadeiro. No código da função `noticiasPublicadas()` o predicado é a função: `n => n.estahPublicada()`, ou seja, usa o método `estahPublicada()` de cada `n` notícia da lista para saber se ela deve passar pelo filtro. Por isso é uma boa prática implementar essa lógica na classe `Noticia`.

O outro método é `sort()`, que aplica uma função de ordenação ao `Array`. A função de ordenação utilizada no método `noticiasPublicadas()` tem dois parâmetros: `a` e `b`, ambos do tipo `Noticia`. A função é aplicada a cada par de notícias da lista e retorna:

* `-1`: se a data da notícia `b` for menor que a da notícia `a`
* `1`: se a data da notícia `b` for maior que a da notícia `a`
* `0`: caso contrário

O resultado disso é que o Template utiliza o método `noticiasPublicadas()` para apresentar apenas as notícias publicadas e ordenada de forma decrescente pela data da notícia.

O trecho do Template é apresentado a seguir:

```html
<div *ngIf="tela == 'home'">
  <h2>Notícias recentes</h2>
  <p>Clique no título da notícia para ler o conteúdo</p>
  <div id="lista-noticias">
    <div class="lista-noticias-item" *ngFor="let noticia of noticiasPublicadas()" (click)="mostrar(noticia)">
      <div class="lista-noticias-titulo">{{ noticia.titulo }}</div>
      <div class="lista-noticias-info">Por {{ noticia.autor }} em {{ noticia.data | date }}</div>
    </div>
  </div>
</div>
```

A diretiva `NgForOf` aplicada ao `div` da linha 5 é responsável por criar os elementos da lista das notícias recentes. Perceba o uso da diretiva `(click)` para chamar o método `mostrar()` assim que o usuário clicar em cada item da lista, conduzindo a uma navegação para a tela  **leitura** e apresentando o conteúdo da notícia em questão. 

Há um recurso novo em ação: o **Pipe**. Na linha 7 é apresentada a data notícia (atributo `data`). Se nenhum tratamento for realizado, por ser um objeto `Date`, será apresentada uma data no formato padrão do JavaScript. Para lidar com isso de forma diferente é usado `DatePipe`:

```{style=nonumber}
{{ noticia.data | date }}
```

Um **pipe** é aplicado ao valor da esquerda (usando essa sintaxe que adota a barra vertical como separador). Assim, `DatePipe` converte `noticia.data` para um formato mais amigável. O Angular disponibiliza o `DatePipe`, `UpperCasePipe`, `LowerCasePipe`, `CurrencyPipe` e `PercentPipe`.

### Apresentar conteúdo da notícia para leitura

A tela **leitura** é responsável por apresentar o conteúdo da notícia para leitura. No Controller, o método `mostrar()` tem o conteúdo:

```typescript
mostrar(noticia) {
    this.leituraNoticia = noticia;
    this.irPara('leitura');
}
```

O método `mostrar()` recebe o parâmetro `noticia` (a notícia que terá seu conteúdo apresentado) e o atribui para `leituraNoticia`, um atributo que é usado no Template para indicar a notícia que é apresentada. Por fim o método chama `irPara()`, para mudar para a tela **leitura**.

No Template há o código a seguir:

```html
<div *ngIf="tela == 'leitura' && leituraNoticia">
  <h2>{{ leituraNoticia.titulo }}</h2>
  <div class="leitura-noticia-info">
    Por {{ leituraNoticia.autor }}
    <span *ngIf="leituraNoticia.emailDoAutor">({{ leituraNoticia.emailDoAutor }})</span>
    em {{ leituraNoticia.data | date }}
  </div>
  <hr>
  <div class="leitura-noticia-conteudo">
    {{ leituraNoticia.conteudo }}
  </div>
  <div class="leitura-noticia-rodape">
    <button (click)="fechar()">Fechar</button>
  </div>
</div>
```

O código do Template para tela **leitura** usa o atributo `leituraNoticia` para apresentar as informações da notícia (do título à data). Por fim, um `button` permite chamar o método `fechar()`, que oculta a tela **leitura** e apresenta novamente a tela **home**:

```typescript
fechar() {
    this.leituraNoticia = null;
    this.irPara('home');
}
```

### Listar todas as notícias com pesquisa

A tela **lista** apresenta a lista de todas as notícias e fornece uma funcionalidade de pesquisa. Para implementar essas funcionalidades o Controller declara o método `noticiasParaLista()`:

```typescript
noticiasParaLista() {
    if (this.listaPesquisa) {
        return this.noticias.filter(n =>
            n.titulo.indexOf(this.listaPesquisa) !== -1
            || n.conteudo.indexOf(this.listaPesquisa) !== -1
            || n.autor.indexOf(this.listaPesquisa) !== -1
        );
    } else {
        return this.noticias;
    }
}
```

O método `noticiasParaLista()` verifica o valor do atributo `listaPesquisa`, que está vinculado a um `input` no Template para aplicar um filtro no `Array` `noticias`, utilizando o método `filter()`. A função predicado informada como parâmetro para o método `filter()` retorna `true` quando um dos atributos `titulo`, `conteudo` ou `autor` da notícia `n` tiver parte do conteúdo de `listaPesquisa`, utilizando o método `indexOf()` de `String`.

No Template o código apresenta o `input` para fornecer o campo de pesquisa e um `table` para apresentar a lista de notícias com base no método `noticiasParaLista()`:

```html
<div *ngIf="tela == 'lista'">
  <h2>Todas as notícias</h2>
  <div id="lista-pesquisar">
    <input type="search" placeholder="Digite para pesquisar..." [(ngModel)]="listaPesquisa">
  </div>
  <table>
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Título</th>
        <th scope="col">Autor</th>
        <th scope="col">Publicada</th>
        <th scope="col">Ações</th>
      </tr>
    </thead>
    <tbody>
      <tr *ngFor="let noticia of noticiasParaLista()">
        <td>{{ noticia.id }}</td>
        <td>{{ noticia.titulo }}</td>
        <td>{{ noticia.autor }}</td>
        <td>{{ noticia.data }}</td>
        <td>
          <button (click)="editar(noticia)">Editar</button> | 
          <button (click)="excluir(noticia)">Excluir</button>
        </td>
      </tr>
    </tbody>
  </table>
</div>
```

No `input` usado para a pesquisa está sendo utilizado o recurso de **two-way data binding**, para vinculá-lo ao atributo `listaPesquisa`. As linhas da tabela dentro do elemento `tbody` são criadas usando a diretiva `NgForOf` e a expressão de iteração chama o método `noticiasParaLista()`. A última coluna apresenta dois `button` que dão acesso, respectivamente, à funcionalidade de editar e excluir, chamando os métodos `editar()` e `excluir()` do Controller.

### Cadastrar e editar notícia

Em termos de **two-way data binding** não há novidade quanto ao branch "inicio" além de haver um número maior de campos e atributos vinculados (`titulo`, `conteudo`, `autor`, `emailDoAutor` e `data`). Além disso, a lógica de salvar os dados do cadastro está adaptada para executar duas funções: cadastrar e editar. A seguir, o código do método `salvar()`:

```typescript
salvar(form) {
    if (!this.editarNoticia) {
        const noticia = new Noticia(
            this.noticias.length,
            this.titulo,
            this.conteudo,
            this.autor,
            this.emailDoAutor,
            this.data
        );
        this.noticias.push(noticia);
    } else {
        const noticia = this.noticias.find(n => n.id === this.editarNoticia.id);
        noticia.titulo = this.titulo;
        noticia.conteudo = this.conteudo;
        noticia.autor = this.autor;
        noticia.emailDoAutor = this.emailDoAutor;
        if (this.data) {
            noticia.data = new Date(this.data);
        } else {
            this.data = null;
        }
        this.editarNoticia = null;
    }
    form.reset();
    this.irPara('lista');
}
```

O método `salvar()` recebe o parâmetro `form`, que deve ser informado no Template e é utilizado para redefinir os valores dos campos do formulário (linha 25). O método identifica qual operação realizar (cadastro ou edição) com base no atributo `editarNoticia`: se não estiver definido (não está editando) então opera no modo de cadastro, caso contrário, edição.

A primeira parte do método (linhas 2-10) trata do cadastro: cria uma instância de `Noticia` e a insere no `Array` `noticias`. A segunda parte (linhas 11-24) tem mais passos: 

1. encontra o elemento de `noticias` com base em `editarNoticia.id` (usa o método `find()`)
2. atualiza os dados dessa notícia com base nos atributos vinculados ao formluário de edição

Uma parte importante do formulário (cadastro e edição) tem relação com o campo "data". O software utiliza o campo "datetime-local" do HTML5 para que o usuário possa informar uma data e hora. Embora a interface apresente a data no formato do usuário (ex: **DD/MM/AAAA hh:mm**) o código recebe esse valor em outro formato **YYYY-MM-DDThh:mm** onde:

* **YYYY**: ano com quatro dígitos
* **MM**: número do mês do ano com dois dígitos
* **DD**: número do dia do mês com dois dígitos
* **hh**: horas com dois dígitos
* **mm**: minutos com dois dígitos

Assim a data informada pelo usuário "30/07/2018 18:00" se torna "2018-07-30T18:00". O construtor da classe `Date` faz o parser dessa string no formato correto e cria um objeto da forma adequada. 

A tela **editar** é apresentada quando o usuário clica no botão "Editar" na linha da tabela de notícias correspondente e chama o método `editar()`:

```typescript
editar(noticia) {
    this.editarNoticia = noticia;
    this.titulo = noticia.titulo;
    this.conteudo = noticia.conteudo;
    this.autor = noticia.autor;
    this.emailDoAutor = noticia.emailDoAutor;
    this.data = this.date2str(noticia.data);
    this.irPara('edicao');
}
```

O método `editar()` recebe o parâmetro `noticia` e atualiza os atributos vinculados ao formulário para que este, no modo de edição, apresente os dados da notícia. Uma ressalva trata do atributo `data` de `noticia`: por ser do tipo `Date` não pode ser atribuído diretamente ao campo "data", por questões do formato. Portanto, a linha 7 chama o método `date2str()` que recebe um objeto `Date` e retorna uma string no formato **YYYY-MM-DDThh:mm**.

O Controller contém também o método `cancelarEdicao()`, que sai do modo de edição de notícia sem editar os dados e retorna para a tela **lista**.

O Template para as telas **cadastro** e **edicao** é apresentado a seguir:

```html
<div *ngIf="tela == 'cadastro' || tela == 'edicao'">
  <h2>Cadastrar notícia</h2>
  <form #cadastro="ngForm" (submit)="salvar(cadastro)">
    <div>
      <label for="titulo">Título</label>
      <input type="text" id="titulo" name="titulo" [(ngModel)]="titulo" required>
    </div>
    <div>
      <label for="conteudo">Conteúdo</label>
      <textarea id="conteudo" name="conteudo" cols="80" rows="5" [(ngModel)]="conteudo" required></textarea>
    </div>
    <div>
      <label for="autor">Autor</label>
      <input type="text" name="autor" id="autor" [(ngModel)]="autor" required>
    </div>
    <div>
      <label for="emailDoAutor">E-mail do autor</label>
      <input type="email" name="emailDoAutor" id="emailDoAutor" [(ngModel)]="emailDoAutor">
    </div>
    <div>
      <label for="data">Data</label>
      <input type="datetime-local" name="data" id="data" [(ngModel)]="data">
    </div>
    <div>
      <button type="submit" [disabled]="!cadastro.form.valid">Salvar</button>
      <button type="reset" *ngIf="tela == 'cadastro'" formnovalidate>Limpar</button>
      <button *ngIf="tela == 'edicao'" (click)="cancelarEdicao()" formnovalidate>Cancelar</button>
    </div>
  </form>
</div>
```

A diretiva `(submit)` chama o método `salvar()` passando como argumento a variável local de template `cadastro` (que representa o formulário). A sequência do código realiza **two-way data binding** e, por fim, apresenta três `button` que aparecem em situações diferentes:

* "Salvar" e "Limpar" aparecem quando a tela é **cadastro**
* "Salvar" e "Cancelar" aparecem quando a tela é **edicao**


### Excluir notícia

A funcionalidade de excluir notícia não tem uma tela, mas faz parte da tela **lista**. Um `button` permite chamar o método `excluir()`:

```typescript
excluir(noticia) {
    if (confirm(`Tem certeza que deseja excluir a notícia: ${noticia.titulo} ?`)) {
        this.noticias.splice(this.noticias.findIndex(n => n.id === noticia.id), 1);
    }
}
```

O método `excluir()` recebe o parâmetro `noticia`, que representa a notícia para ser excluída. Antes de, efetivamente, excluir, o código chama a função `confirm()` para solicitar uma confirmação ao usuário. Se o usuário confirmar a exclusão, o código chama o método `splice()` do Array `noticias`, que recebe dois argumentos:

* a posição (índice) a partir de onde deve excluir elementos de `noticias`
* a quantidade de elementos para excluir

O índice da notícia a ser excluída é obtido usando o método `findIndex()` de `Array`, que recebe como argumento uma função predicado que encontra um elemento `n` da lista se seu atributo `id` for igual ao mesmo atributo do objeto `noticia` (parâmetro). Assim, se o usuário confirmar a exclusão, um elemento é removido de `noticias` e a lista de notícias é atualizada (por causa do data binding).

## Conclusão

Este capítulo apresentou o branch "crud-telas" do software **noticas-angular**. Esse branch destaca a implementação de funcionalidades adicionais que o tornam mais próximo de um software real para o propósito de visualizar e gerenciar notícias, principalmente implementando uma interface CRUD. 

A partir daqui serão utilizados mais recursos do Angular, como serviços. Sugiro fortemente que não avance a leitura e seus estudos se não tiver entendido claramente como funciona data binding e o uso de diretivas para as situações apresentadas até aqui.
