# Angular CLI {#sec:angular-cli}

O **Angular CLI** é uma ferramenta para inicializar, desenvolver, criar conteúdo e manter software desenvolvido em Angular [@angularclihome]. A principal utilização dessa ferramenta começa na criação de um projeto. Você pode fazer isso manualmente, claro, mas lidar com todas as configurações necessárias para o seu software Angular pode não ser algo fácil, mesmo se você for um desenvolvedor com nível de conhecimento médio a avançado.

---

**cê-éle-í?**

Como parte do vocabulário do Angular, geralmente é interessante pronunciar angular cê-ele-i, isso mesmo, CLI é uma sigla para *Command Line Interface* (no português Interface de Linha de Comando). Assim, usar "cli" não é exclusividade  do Angular -- provavelmente você verá isso em outros projetos.

---

As seções a seguir vão apresentar como instalar e utilizar o Angular CLI para desenvolver software Angular.

## Instalando

O Angular CLI é distribuído como um pacote do **Node.JS**, então você não encontrará um instalador, como acontece com software tradicional. Ao invés disso, você precisa de um ambiente de desenvolvimento com o NodeJS instalado (veja [@sec:apendice-1]).

## Comandos

O Angular CLI disponibiliza uma série de comandos, que são fornecidos como parâmetros para o programa ng. Além disso, cada comando possui diversas opções (ou **flags**). Faça o exercício de se acostumar a essas opções para aumentar a sua produtividade.

Os principais comandos serão apresentados nas seções seguintes.

### help

O comando `help` é muito importante porque apresenta uma documentação completa do Angular CLI, ou seja, todos os comandos e todas as suas opções.

**Exemplo: documentação completa**

```{style=nonumber .sh}
$ ng help
```

Essa linha de comando apresenta todos os comandos e todas as suas opções.

**Exemplo: documentação do comando **`new`**:**

```{style=nonumber .sh}
$ ng help new
```

Essa linha de comando apresenta apenas as opções do comando `new`.

As opções de cada comando são sempre precedidas de `--` (dois traços). Algumas opções aceitam um valor e geralmente possuem um valor padrão.

**Exemplo: comando new com duas opções**

```{style=nonumber .sh}
$ ng new escola-app --skip-install --routing true --minimal true
```

Essa linha de comando fornece três opções para o comando `new`: `--skip-install`, `--routing` (que recebe o valor `true`) e `--minimal` (que recebe o valor `true`).

### new

O comando `new` é utilizado para criar um projeto \(um software Angular\). Exemplo:

```{style=nonumber .sh}
$ ng new escola-app
```

Quando a linha de comando do exemplo for executada será criado um software Angular chamado **escola-app** e estará em um diretório chamado `escola-app`, localizado a partir de onde a linha de comando estiver sendo executada.

A parte mais interessante de criar um projeto Angular com esse comando é que ele cria todos os arquivos necessários para um software bastante simples, mas funcional.

Para cada comando do Angular CLI é possível informar opções. As opções mais usadas do comando `new` são:

* `--skip-install`: faz com que as dependências não sejam instaladas
* `--routing`:  se for seguida de `true`, o comando cria um módulo de rotas (isso será visto em outro capítulo)

### generate

O comando `generate` é utilizado para criar elementos em um projeto existente. Para simplificar, é bom que o Angular CLI seja executado a partir do diretório do projeto a partir de agora. Por meio desse comando é possível criar:

* class
* component
* directive
* enum
* guard
* interface
* module
* pipe
* service

Como acontece com outros comandos, o `generate` também pode ser utilizado por meio do atalho `g`.

**Exemplo: criar component ListaDeDisciplinas**

```{style=nonumber .sh}
$ ng generate component ListaDeDisciplinas
```

Essa linha de comando criará o diretório `./src/app/lista-de-disciplinas` e outros quatro arquivos nesse mesmo local:

* `lista-de-disciplinas.component.css`
* `lista-de-disciplinas.component.html`
* `lista-de-disciplinas.component.spec.ts`
* `lista-de-disciplinas.component.ts`

É importante notar que esses arquivos não estão vazios, mas já contêm código que mantém o software funcional e utilizável. Por isso o Angular CLI considera que as opções (class, component etc.) são como **blueprints** (ou plantas, em português). Outra coisa importante é que o Angular CLI também modificará o arquivo `./src/app/app.module.ts` se necessário (ou outro arquivo que represente um módulo que não seja o **root module** -- mais sobre isso depois).

### serve

O comando `serve` compila o projeto e inicia um servidor web local. Por padrão o servidor web executa no modo `--watch` , que reinicia o comando novamente, de forma incremental, sempre que houver uma alteração em algum arquivo do projeto, e `--live-reload`, que recarrega o projeto no browser (se houver uma janela aberta) sempre que houver uma mudança.

### build

O comando `build` compila o projeto, mas não inicia um servidor web local. Ao invés disso, gera os arquivos resultantes da compilação em um diretório indicado.

## Criando e executando um projeto Angular {#sec:angular-cli-criando}

Para iniciar, execute o comando `new` do **Angular CLI** para criar um projeto. Exemplo:

```{style=nonumber .sh}
$ ng new angular-hello-world
```

Aguarde alguns instantes para a conclusão das instalações dos pacotes e configuração inicial do projeto. O comando vai criar a pasta `angular-hello-world`. Execute os comando seguintes de dentro da pasta do rpojeto.

O software **noticias-js** pode ser executado no browser diretamente ao abrir o arquivo `index.html`, disso você já sabe. O que muda em relação a como executar um software angular? 

Por causa da estrutura e da arquitetura do Angular a execução não pode ser feita abrindo o arquivo `src/index.html` no browser, pois será necessário um **servidor web**. O **Angular CLI** já inclui um **servidor web local** ou **servidor web de desenvolvimento**, que pode ser iniciado com o comando:

```{style=nonumber .sh}
$ npm start
```

Esse não é bem um comando do **Angular CLI**, mas um **script** declarado no arquivo `package.json`:

```
{
  "name": "angular-hello-world",
  "version": "0.0.0",
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e"
  },
...
}
```

O script **start** executa o comando `serve` do **Angular CLI**. Então, na prática, o script é um *atalho* e, por isso, há mais de uma maneira de iniciar o software.

Ao executar o script será apresentada uma saída que indica que você deve acessar a URL `http://localhost:4200` no browser. Faça isso para ver uma imagem semelhante à seguinte:

![Versão inicial do software em execução no Browser](./graphics/software-versao-inicial-browser.png){#fig:angular-cli-inicial}

A URL `http://localhost:4200` identifica três elementos importantes:

* uso do protocolo HTTP
* o **host** é identificado por `localhost` (ou seja, a máquina local e, por isso o termo "servidor web local")
* a porta é `4200`

Veremos mais sobre esses e outros comando no decorrer do livro.

## O arquivo package.json

O arquivo `package.json` contém informações do projeto, scripts e dependências. O arquivo criado pelo **Angular CLI** tem mais ou menos o seguinte conteúdo:

```js
{
  "name": "angular-hello-world",
  ...
  "scripts": {
    ...
  },
  "private": true,
  "dependencies": {
    ...
  },
  "devDependencies": {
    ...
  }
}
```

Por ser um conteúdo em formato JSON, o interpretamos da seguinte forma:

* `name`: define o nome do projeto para o **Node.JS** (nenhuma relação com o Angular)
* `scripts`: contém scripts que podem ser executados no prompt (exemplos: `npm start` e `npm run build`)
* `dependencies`: define as dependências do projeto (pacotes, e suas versões, que são incluídos na compilação)
* `devDependencies`: define as dependências de desenvolvimeto (pacotes que não têm código-fonte incluído na compilação)

---

**Espera! Você disse compilação?**

Como já disse, você pode utilizar JavaScript ou TypeScript ao desenvolver projetos Angular. Isso não é bem assim. Na prática, geralmente você encontrará a recomendação (senão uma ordem) de utilizar TypeScript. O problema é que seu Browser não entende TypeScript, por isso é necessário um processo de tradução do código TypeScript para JavaScript. E não é só isso. Há vários recursos utilizados no projeto Angular criado com TypeScript que precisam de ajustes para que funcionem no seu Browser.

Por isso os comandos `serve` e `build` são tão importantes e -- por que não dizer? -- **browser-friendly** =)

---

## Estrutura do projeto {#sec:angular-cli-estrutura}

[@sec:angular-iniciando] apresentou a estrutura de um projeto Angular. De forma visual, a estrutura do projeto criado até o momento é ilustrada pela [@fig:angular-estrutura-projeto].

```{#fig:angular-estrutura-projeto .plantuml caption="Estrutura básica de um projeto Angular" format="eps" width=10cm}
@startuml

class AppModule <<NgModule>> {
}

class AppComponent <<Component>> {
}

AppModule --> angular.platformbrowser.BrowserModule: imports
AppModule -> AppComponent: declarations
AppModule -> AppComponent: bootstrap

@enduml
```

As relações entre as classes merecem destaque, começando por `AppModule`.

### AppModule

A classe `AppModule` representa o **root module** do projeto e isso é especificado no arquivo `src/main.ts`:

```typescript
...
import { AppModule } from './app/app.module';
...
platformBrowserDynamic().bootstrapModule(AppModule)
  .catch(err => console.log(err));
```

A linha 4 contém uma chamada para o método `bootstrapModule()` com argumento `AppModule`, o que indica para o Angular que ele deve usar esse módulo (`AppModule`) na execução inicial (**Bootstrap**). A partir de então os recursos do módulo poderão ser utilizados.

[@lst:noticias-angular-appmodule-inicial] apresenta a classe `AppModule`.

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

: Código-fonte do AppModule no software noticias-angular {#lst:noticias-angular-appmodule-inicial}

A classe `AppModule` é declarada usando o decorator `@NgModule` e entre as linhas 6 e 16 há a utilização desse recurso para informar os metadados:

* `declarations`: a lista dos componentes declarados (ou pertencentes) a o módulo
* `imports`: a lista dos módulos importados 
* `providers`: a lista de serviços declarados (vazia, por enquanto)
* `bootstrap`: define o **root component**

Em resumo, o `AppModule` é um módulo do Angular que declara o componente `AppComponent`, importa o módulo `BrowserModule` (do pacote `@angular/platform-browser`) e usa `AppComponent` como **root component**.

O **Angular** adota uma nomeclatura para o arquivo de módulo: `<modulo>.module.ts`. Assim, podemos usar o termo "módulo App" e saber do que se trata.

### AppComponent

A classe `AppComponent` representa o **root component** do projeto e está declarada no arquivo `src/app/app.component.ts` cujo conteúdo é o seguinte:

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    title = 'Angular';
}
```

A classe `AppComponent` declara o atributo `title` e o código também utiliza um  **decorator**, dessa vez o `@Component` que também adiciona metadados à classe:

* `selector`: representa o seletor CSS para que o Angular identifique onde (em um Template) o componente deve ser apresentado
* `templateUrl`: o caminho do arquivo usado como Template (HTML)
* `styleUrls`: a lista de caminhos de arquivos usados como estilo (CSS)

Os metadados declaram, também, a estrutura básica de um Componente:

* **Controller**: a classe, em si
* **Template**: um arquivo HTML que contém marcação e representa a **View**

O **Angular** adota uma nomenclatura para facilitar a localização desses arquivos:

* `<componente>.component.ts` representa o Controller
* `<componente>.component.html` representa o Template
* `<componente>.component.css` representa o arquivo CSS (complemento para o Template)

Assim podemos usar o termo "componente App" e saber que se trata do conjunto de arquivos que declaram esse componente.

O Template desse componente (o arquivo `src/app.component.html`) tem conteúdo que parece HTML. Veja o trecho:

```html
<h1>
Welcome to {{ title }}!
</h1>
```

Por que eu disse "parece HTML"? Porque isso realmente não é HTML, é a **linguagem de Template do Angular**, que herda características do HTML. Nesse trecho, inclusive, você pode percebe que é realmente semelhante, só diferenciando na linha 2, que traz um conteúdo entre `{{` e `}}`. Essa sintaxe entre chaves duplas é chamada **interpolação** e é utilizada para apresentar valores declarados no Controller por meio do recurso de **data binding**.

Você já viu que a classe `AppComponent` declara um atributo `title`, então o conteúdo do Template instrui o Angular a substituir o trecho `Welcome to {{ title }}!` por `Welcome to Angular!`. Nesse ponto vemos, na prática, mais um elemento da arquitetura MCV, o **Model**. 

O Model não é um arquivo, é mais um conceito que permite o **data biding**, ou seja, passar dados entre Template e Controller. A [@fig:angular-cli-component-template-model] ilustra esse conceito.

![Relação entre Model-Template-Controller no AppComponent](./graphics/angular-inicial-app-component-template-model.png){#fig:angular-cli-component-template-model}

Aqui aprendemos algo um conceito importante: o **Angular interpreta o Template**. Isso significa que todo o seu conteúdo, HTML e CSS, é interpretado pelo Angular antes de ser entregue para o browser. É isso que faz com que o recurso de interpolação funcione. É por isso, também, que Data binding é tão importante no Angular. Ele é responsável por fazer com que o atributo `title`, que compõe o Model, esteja disponível para ser usado no Template. Além disso, qualquer alteração no valor desse atributo representará uma alteração na visualização do componente no browser.

---

**MVC ou MVVM?**

Se você se lembrar da discussão sobre Angular ser MVC ou MVVM é aqui que o entendimento pesa a favor do segundo. O Controller não está desassociado do Model e, por causa do Data binding, sua função inclui informar o Template de que ocorreu uma alteração no Model, de forma que ele seja atualizado.

---

É isso, o Model é um conceito abstrato e está, geralmente, no Controller, representado por atributos e métodos -- sim, também é possível mostrar o valor retornado por um método no Template (mais sobre isso depois).

### index.html

Para finalizar essa seção falta entender como o `AppComponent` é, enfim, apresentado. Para isso, veja o arquivo `src/index.html`:

```html
<!doctype html>
<html lang="en">
<head>
...
</head>
<body>
  <app-root></app-root>
</body>
</html>
```

Esse arquivo também é um Template, mas não está associado a um componente. O arquivo `angular.json` (por volta da linha 17) é que define que esse arquivo é chamado inicialmente durante a execução do software no servidor web local.

A parte HTML é fácil de entender, bem como saber que o elemento `app-root` não faz parte do HTML. Então, de onde vem esse elemento? Voltando um pouco na declaração do `AppComponent` lembramos que há um metadado chamado `selector`, que tem justamente o valor `'app-root'`. Por ser um seletor de elemento a instrução para o Angular, ao processar o `src/index.html` é: onde encontrar o elemento `app-root` apresente o componente `AppComponent`. Assim, as coisas ficam mais ou menos como ilustra a [@fig:angular-cli-index].

![Execução do Template inicial](./graphics/angular-inicial-estrutura-appmodule-appcomponent-index.png){#fig:angular-cli-index}

**Viu? Não é mágica. É software!**

## O que vem a seguir?

Como agora você já deve estar entendendo melhor o funcionamento do Angular e o código-fonte do projeto, é hora de sujar as mãos =) No capítulo seguinte você vai implementar o software **noticias-angular**.
