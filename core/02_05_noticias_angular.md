# Gerenciador de notícias com Angular {#sec:noticias-angular}

O **Capítulo [-@sec:introducao]** apresentou conceitos básicos de desenvolvimento web e o software **noticias-js**, um projeto de gerenciador de notícias que é implementado utilizando tecnologias front-end (HTML, CSS e JavaScript). Este capítulo apresenta o **noticias-angular**, basicamente uma outra versão do **noticias-js**, mas que é implementado utilizando **Angular**, com o objetivo de apresentar os recursos desta plataforma de desenvolvimento. 

Este capítulo apresenta:

* estrutura de um componente (Controller e Template)
* estrutura completa de um projeto integrando módulo e componente
* linguagem de template, variável de template e data biding
* diretivas estruturais: `NgIf` e `NgForOf`
* diretivas de atributos para lidar com propriedades (data binding)
* diretivas de atributos para lidar com eventos (data binding)
* diretivas de atributos para lidar com formulários (two-way data binding)

Os próximos capítulos apresentarão outras versões deste software ou outros softwares, quando necessário, para que seja possível conhecer os recursos do Angular.

## Clonar e executar localmente

Este capítulo está diretamente relacionado com o software **noticias-angular**, branch "inicio"^[O repositório do branch "iniciando" está disponível em <https://github.com/jacksongomesbr/webdevbook-noticias-angular/tree/inicio> e você pode vê-lo em execução acessando <https://stackblitz.com/github/jacksongomesbr/webdevbook-noticias-angular/tree/inicio>]. Antes de prosseguir, clone e execute o projeto localmente. A interface do software é idêntica à do **noticias-js**. Verifique.

## O projeto

O **noticias-angular** foi criado com o comando `new` do **Angular CLI**:

```{style=nonumber .sh}
$ ng new webdevbook-noticias-angular
```

A estrutura do projeto muda em relação ao projeto básico criado pelo **Angular CLI** ([@sec:angular-cli-criando]) em alguns aspectos. Assim, o software será apresentado a partir da [@fig:noticias-angular-estrutura], que apresenta a sua estrutura.

```{#fig:noticias-angular-estrutura .plantuml caption="Estrutura do projeto angular-noticias" format="eps" width=10cm}
@startuml

class AppModule <<NgModule>> {
}

class AppComponent <<Component>> {
    noticias: Array
    titulo: string
    conteudo: string
    salvar()
    mostrar(noticia: any)
    fechar(noticia: any)
}

AppModule --> angular.platformbrowser.BrowserModule: imports
AppModule --> angular.forms.FormsModule: imports
AppModule -> AppComponent: declarations
AppModule -> AppComponent: bootstrap

@enduml
```

As relações entre os componentes são as mesmas apresentadas na [@sec:angular-cli-estrutura], com o acréscimo da importação do módulo `FormsModule`, que é necessário para que o projeto possa utilizar os recursos do Angular que permitem o usuário entrar dados por meio de formulários.

### AppComponent

As maiores modificações estão no `AppComponent` e detalhes são apresentados nas seções seguintes.

#### Controller

Um trecho do Controller do `AppComponent` é apresentado a seguir:

```typescript
...
@Component({
    ...
})
export class AppComponent {
  noticias = [];
  titulo = null;
  conteudo = null;

  salvar() {
    ...
  }

  mostrar(noticia) {
    ...
  }

  fechar(noticia) {
    ...
  }
}
```

A classe declara três atributos: 

* `noticias`: um `Array` que contém a lista de notícias
* `titulo`: representa o título da notícia cadastrada (vinculada ao formulário)
* `conteudo`: representa o conteúdo da notícia cadastrada (vinculada ao formulário)

Há também três métodos:

* `salvar()`: obtém os dados do formulário (título e conteúdo da notícia) e insere na lista de notícias
* `mostrar()`: apresenta o conteúdo de uma notícia (informada como parâmetro `noticia`)
* `fechar()`: oculta o conteúdo de uma notícia (informada como parâmetro `noticia`)

Em detalhes, o método `salvar()`:

```typescript
salvar() {
    const noticia = {
        id: this.noticias.length,
        titulo: this.titulo,
        conteudo: this.conteudo,
        visivel: false
    };
    this.noticias.push(noticia);
    this.titulo = null;
    this.conteudo = null;
}
```

O método `salvar()` contém código que realiza o seguinte:

* cria um objeto (`noticia`) com quatro atributos: `id`, `titulo`, `conteudo` e `visivel`. Os valores dos atributos `titulo` e `conteudo` são obtidos dos respectivos atributos (note o uso de `this`) e o valor do atributo `visivel` é `false`
* adiciona o objeto `noticia` na lista `noticias` por meio do método `push()`
* redefine valores dos atributos `titulo` e `conteudo` (atribuindo o valor `null`)

Os métodos `mostrar()` e `fechar()` são bem semelhantes:

```typescript
mostrar(noticia) {
    noticia.visivel = true;
}

fechar(noticia) {
    noticia.visivel = false;
}
```

Os métodos recebem o parâmetro `noticia`, um objeto que representa uma notícia, e alteram o valor do atributo `visivel`:

* `true`: mostrar o conteúdo
* `false`: ocultar o conteúdo

#### Template

O Template do `AppComponent` contém três partes:

* cabeçalho
* área da lista de notícias
* área do cadastro de notícias

O seguinte trecho do Template mostra onde estão essas partes:

```html
<h1>Gerenciador de notícias</h1>
<h2>Notícias recentes</h2>
...
<h2>Cadastrar notícia</h2>
...
```

Primeiro, a área da lista de notícias:

```html
<h2>Notícias recentes</h2>
<p>Clique no título da notícia para expandir</p>
<div>
  <ul>
    <li *ngFor="let noticia of noticias">
      <p (click)="mostrar(noticia)">{{ noticia.titulo }}</p>
      <p *ngIf="noticia.visivel">
        {{ noticia.conteudo }}
        <br> -------------
        <br>
        <button (click)="fechar(noticia)">Fechar</button>
      </p>
    </li>
  </ul>
</div>
```

O elemento `li` da linha 5 tem o atributo `*ngFor`, que representa a **diretiva estrutural** `NgForOf`, uma **diretiva de repetição** que insere elementos no DOM conforme uma expressão de iteração com a sintaxe: `let variavel of lista`. Nesse caso `variavel` é chamada de **variável de template** porque só existe no contexto do Template. A diretiva `NgForOf` instrui o Angular a repetir uma parte do template para da item de uma iteração. O valor `let noticia of noticias` é interpretado assim:

> Para cada notícia na lista `noticias`, crie uma variável de template chamada `noticia` e torne-a disponível para cada iteração (repetição) do template contido em `li`.

Assim, o conteúdo do `li` é repetido para cada item da lista `noticias` (atributo do Controller). O `li` contém dois elementos `p` que são usados para propósitos diferentes. O primeiro `p`, com atributo `(click)`, apresenta o título da notícia usando **interpolação**. Perceba que o Angular interpreta a variável de template `noticia` seguindo o determinado no Controller (notícia tem o atributo `titulo`).

Outra parte importante tem relação com o atributo `(click)`, que usa a **sintaxe de evento** ([@sec:angular-data-binding]). Nesse caso, o evento é `click` e o conteúdo do atributo, `mostrar(noticia)`, instrui o Angular a chamar o método `mostrar()` (definido no Controller) passando como argumento a variável de template `noticia`, para que seu conteúdo seja apresentado. Perceba a interação com os métodos declarados no Controller e a utilização do **paradigma da Orientação a Objetos**.

O outro elemento `p` tem o atributo `*ngIf`, que representa a diretiva estrutural `NgIf`, uma **diretiva condicional**, que insere e remove elementos no DOM conforme uma expressão booleana. O valor `noticia.visible` é interpretado assim:

> Se o valor do atributo `visivel` da variável de template `noticia` for `true`, então insere o elemento `p` no DOM, caso contrário, remove o elemento `p`.

Assim, se o atributo `visivel` da variável de template `noticia` for `true`, seu conteúdo será visível. 

Para finalizar, há um elemento `button` que também tem um atributo `(click)` com valor `fechar(noticia)`, o que instrui o Angular a chamar o método `fechar()` passando como argumento a variável de template `noticia`, para que seu conteúdo seja ocultado.

--- 

**Diretivas manipulam DOM?**

O projeto **noticias-js** usa DOM (diretamente ou por meio do jQuery) para manipulação do DOM. De forma simplificada o Angular faz a mesma coisa usando diretivas, como `NgForOf` e `NgIf`.

---

A outra parte importante do Template tem a ver com o formulário de cadastro:

```html
<h2>Cadastrar notícia</h2>
<form #cadastro="ngForm" (submit)="salvar()">
  <div>
    <label for="titulo">Título</label>
    <input type="text" id="titulo" name="titulo" [(ngModel)]="titulo" required>
  </div>
  <div>
    <label for="conteudo">Conteúdo</label>
    <textarea id="conteudo" name="conteudo" cols="80" rows="5" [(ngModel)]="conteudo" required></textarea>
  </div>
  <div>
    <button type="submit" [disabled]="!cadastro.form.valid">Salvar</button>
    <button type="reset" formnovalidate>Limpar</button>
  </div>
</form>
```

Primeiro, ao utilizar o elemento `form` o Angular cria automaticamente uma diretiva `NgForm`, que determina o funcionamento do formulário a partir de então (campos, validação etc.). Se você precisar acessar informações do formulário poderá usar uma variável de template. Por isso o `form` tem o atributo `#cadastro` com valor `ngForm`. 

Além disso o `form` também usa a sintaxe de evento para chamar o método `salvar()` no `submit` (também pode ser usado o nome `ngSubmit`). O comportamento tradicional de um formulário HTML é que seja submetido quando um `button` for clicado. No Angular, por causa da diretiva `NgForm`, o processo é modificado para permitir a interação com o Controller.

No final do `form` há o `button` "Salvar", que tem o atributo `[disabled]`, uma **diretiva de atributo** usada para desabilitá-lo com base em uma expressão booleana. Assim, a expressão `!cadastro.form.valid` acessa a informação de validação global do formulário e, se for `true` (formulário não está válido), desabilita o botão (não permite que ele seja clicado). A validação continua utilizando os recursos do HTML (atributo `required`, por exemplo, em cada campo de preenchimento obrigatório).

Os campos do formulário continuam utilizando elementos do HTML (`input` e `textarea`), mas agora também utilizam **two-way data binding**:

```
...
<input type="text" id="titulo" name="titulo" [(ngModel)]="titulo" required>
...
<textarea id="conteudo" name="conteudo" cols="80" rows="5" [(ngModel)]="conteudo" required></textarea>
...
```

A sintaxe de two-way data binding é utilizada para vincular um campo de formulário a uma expressão (geralmente um atributo declarado no Controller). Isso acontece no `input` por meio de `[(ngModel)]="titulo"`, vinculando o seu valor ao atributo `titulo`, e no `textarea`, por meio de `[(ngModel)]="conteudo"`, vinculando seu valor ao atributo `conteudo`. Isso significa que qualquer alteração em um dos campos gera uma alteração no atributo vinculado, e vice-versa.

Este capítulo apresentou o software **noticias-angular** e sua estrutura. Os destaques são:

* importar o módulo `FormsModule` no **root module**
* uso das diretivas estruturais `NgForOf` e `NgIf`
* uso de data-binding (interpolação, evento, propriedade e vinculação de campo do formulário com atributo do Controller)

