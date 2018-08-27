# Rotas e Serviços {#sec:rotas-e-servicos}

O capítulo [-@sec:noticias-angular-crud-telas] apresentou como utilizar componentes para implementar um software de maneira a organizar o código do software em partes e utilizar recurso de interface semelhante a telas. Este capítulo apresenta uma solução mais robusta: utilizar serviços e rotas, bem como o recurso de injeção de dependência.

Este capítulo apresenta:

* serviços
* injeção de dependência
* rotas, URL, URI
* rotas no Angular
* parâmetros de rota
* shell component
* navegação em rotas


## Serviços

Conforme a Arquitetura do Angular a utilização de **Serviços** tem o propósito de organizar o projeto de software Angular, isolando lógica de negócio e separando-a dos Controllers. Não é possível afirmar que seja obrigatório utilizar serviços, mas é muito desejável.

Na prática não há diferença para o usuário porque, provavelmente, utilizar serviços não afetará diretamente o comportamento da interface. Assim, os benefícios ficam por conta da melhor organização do projeto e da Engenharia de Software para o projeto que está sendo desenvolvido.

Um serviço é uma classe que pode ser utilizada por outros componentes do projeto. Dois detalhes importantes são: o que diferencia um serviço de outro tipo de componente e como um componente utiliza um serviço. Veremos isso a seguir.

Até o momento a lógica do CRUD de notícias está nos Controllers dos componentes. Isso não é uma boa prática, porque é indicado que a lógica dos Controllers seja voltada para gerenciar o comportamento da interface, ou seja, para controlar a interação entre o usuário e o software. Vamos começar criando o serviço `NoticiasService` usando Angular CLI e o comando **generate**  e a opção **service**:

```{style=nonumber}
ng g s Noticias 
```

A linha de comando cria o arquivo `src/app/noticias.service.ts`, cujo trecho de conteúdo é apresentado a seguir:

```typescript
import { Injectable } from '@angular/core';
import { Noticia } from './noticia.model';

@Injectable({
  providedIn: 'root'
})
export class NoticiasService {
  lista: Array<Noticia> = [];

  constructor() {
    this.lista.push(new Noticia(1, 'Teste de notícia',
      'Conteúdo da noíticia de teste',
      'José da Silva', 'jose@gmail.com',
      new Date()));
  }

  public publicadas() {
      ...
  }
...
}
```

O código utiliza a função de anotação `Injectable()` (fornecida pelo pacote `@angular/core`) para adicionar metadados à classe). Na prática, essa função de anotação é que determina que a classe é realmente um serviço.

O parâmetro da função de anotação tem um atributo `providedIn` com valor `root`, o que indifica que o serviço está disponível para ser utilizado em qualquer componente do software. Há outras formas de modificar esse comportamento, mas isso não será visto agora.

Para que uma classe utilize um serviço o Angular implementa o padrão **Dependency Injection** (DI), um padrão de projeto que faz com que o próprio framework crie instâncias do serviço para serem utilizadas no momento adequado. Para exemplificar, considere o trecho de código a seguir:


```typescript
import { Component, OnInit } from '@angular/core';
import { NoticiasService } from '../noticias.service';
import { ActivatedRoute, Router } from '@angular/router';

...
export class LeitorDeNoticiaComponent implements OnInit {
  noticia = null;

  constructor(private noticias: NoticiasService,
    private route: ActivatedRoute,
    private router: Router) { }

  ngOnInit() {
  }

...
}
```

A classe `LeitorDeNoticiaComponent` utiliza o recurso de DI para "injetar" o serviço `NoticiasService` -- isso é feito no construtor da classe. A sintaxe para utilizar esse recurso é `private variavel: Tipo`. Por exemplo: `private noticias: NoticiasService` declara o atributo `noticias`, do tipo `NoticiasService` e faz com que o Angular use o recurso de DI para tornar disponível uma instância de `NoticiasService` (no atributo `noticias`) para ser utilizada no componente.

As seções a seguir têm conteúdo introdutório, antes de apresentar conceitos de rotas.

## URI, URL e URN

**URI** (_Uniform Resource Identifier_) é um identificador de um recurso. A sintaxe desse identificador é:

```{style=nonumber}
scheme:[//[user[:password]@]host[:port]][/path][?query][#fragment]
```

Onde os elementos entre **[]** são opcionais e:

* **scheme**: representa o protocolo ou o contexto;
* **user** e **password**: representam as credenciais do usuário;
* **host**: representa o identificador do dispositivo que contém o recurso;
* **port**: representa o número da porta do dispositivo;
* **path**: representa o caminho do recurso;
* **query**: uma cadeia de caracteres que representa parâmetros de URL, um conjunto de pares `chave=valor` separados por `&`; e
* **fragment**: uma cadeia de caracteres com formato dependente do contexto.

A figura a seguir ilustra um URI e a sua composição:

![Composição da URL](./graphics/ilustracao-uri-url.png){#fig:composicao-url}

O URI identifica um recurso disponível na internet, mais especificamente um repositório do Github. Provavelmente você já conhece isso e pode ser que use os termos **endereço** e **URL**. Você não está errado. URL é a forma mais comum de URI na internet. **URL** (_Uniform Resource Locator_) é um endereço de um recurso na internet. Além disso, URL é a forma mais comum de criar _links_ entre páginas web e incorporar uma imagem em uma página web, por exemplo.

**URN** (_Uniform Resource Name_) é o nome de um recurso em um contexto específico. Por exemplo: o ISBN (_International Standard Book Number_) de uma edição de "Romeu e Julieta", de William Shakespeare, é 0-486-27557-4; seu URN poderia ser **urn:isbn:0-486-2557-4**.

Voltando para URL e o contexto da internet, os browsers geralmente fornecem uma **barra de endereço**, por meio da qual o usuário indica qual URL deseja acessar, por exemplo a URL de uma página web. A partir do momento que o browser acessa uma página web ele passa a armazenar o primeiro endereço acessado e os demais endereços que forem acessados por meio de cliques em _links_.

Esse é, provavelmente, o formato mais intuitivo e o mais utilizado para acessar páginas web. Justamente por isso é necessário repensar a forma como o aplicativo desenvolvido em Angular não apenas entrega conteúdo para o usuário mas também como permite que o usuário o acesse.

## Rotas

Uma rota está diretamente relacionada a URL, ou seja, também funciona como um localizador de um recurso. A diferença é que acrescenta a possibilidade de utilizar **parâmetros de rota**. Por exemplo: considere um site de notícias **noticias.to** que permite acessar a notícia "Governo paga salarios de servidores", cujo identificador é 7899, está na categoria "política" e foi publicada em 20/12/2017, por meio do URL:

```{style=nonumber}
https://noticias.to/noticias/politica/2017/12/20/governo-paga-salarios-de-servidores/7899
```

Há informações no URL que pertencem à notícia e mudam de uma notíca para outra:

* **categoria:** politica
* **ano:** 2017
* **mês:** 12
* **dia:** 20
* **titulo:** governo-paga-salarios-de-servidores
* **identificador:** 7899

Analisando URLs de outras notícias alguém poderia chegar à conclusão de que há um padrão:

```{style=nonumber}
/noticias/categoria/ano/mes/dia/titulo/identificador
```

Independentemente de possuir parâmetros de rota, uma rota é um **padrão**. Cada uma dessas informações (categoria, ano, mes, dia, titulo, identificador), que muda de uma notícia para outra, pode ser representada como um **parâmetro de rota**.

A implementação desse conceito pode variar entre frameworks, mas provavelmente as mesmas funcionalidades estão disponíveis:

* definir uma rota (e, opcionalmente, usar parâmetros de rota)
* identificar valores dos parâmetros de rota

Além disso, como URLs são localizadores de recursos, rotas também servem para esse propósito, ou seja, uma rota está associada algum recurso e é uma forma de acessá-lo.

## Rotas no Angular

Como as seções anteriores apresentam, rotas podem ser entendidas como "caminhos", não URLs verdadeiras, pois são interpretados conforme algum contexto. No Angular isso também acontece. Uma das formas de definir rotas é modificar o `AppModule`:

```typescript
...
import { RouterModule, Routes } from '@angular/router';

import { AppComponent } from './app.component';
import { NoticiasRecentesComponent } from './noticias-recentes/noticias-recentes.component';
import { PaginaNaoEncontradaComponent } from './pagina-nao-encontrada/pagina-nao-encontrada.component';
import { LeitorDeNoticiaComponent } from './leitor-de-noticia/leitor-de-noticia.component';
import { ListaDeNoticiasComponent } from './lista-de-noticias/lista-de-noticias.component';

const rotas: Routes = [
  {path: 'admin/noticias', component: ListaDeNoticiasComponent, },
  {path: 'noticias/:id', component: LeitorDeNoticiaComponent, },
  {path: '', component: NoticiasRecentesComponent, },
  {path: '**', component: PaginaNaoEncontradaComponent}
];

@NgModule({
  declarations: [
    AppComponent,
    NoticiasRecentesComponent,
    PaginaNaoEncontradaComponent,
    LeitorDeNoticiaComponent,
    ListaDeNoticiasComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    RouterModule.forRoot(rotas)
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Primeira parte de destaque é que o código importa `RouterModule` e `Routes` do pacote `@angular/router`. 

Na sequência, depois de importações de vários componentes, há a definição de `rotas`, do tipo `Routes`, que recebe um `array`. Cada elemento do array é um objeto com dois atributos:

* `path`: representa a rota para o componente; e
* `component`: representa o componente associado à rota.

A primeira rota, `admin/noticias` está associada ao componente `ListaDeNoticiasComponent`. 

A segunda rota, `noticias/:id`, está associada ao componente `LeitorDeNoticiasComponent`. Esta rota possui um **parâmetro de rota**. A sintaxe do Angular para parâmetro de rotas é usar o sinal de dois pontos seguido do nome do parâmetro. Nesse caso o parâmetro chama-se `id`. 

A terceira e a quarta rotas têm um comportamento especial. A terceira rota é uma string de comprimento zero ("vazia") e está associada ao componente `NoticiasRecentesComponent`. Isso significa que essa é a **rota padrão**. A quarta rota é `**`, associada ao componente `PaginaNaoEncontradaComponent`. Isso significa um atalho para o seguinte comportamento: se o usuário informar uma URL que não combina com nenhuma das rotas, então navegue até o componente `PaginaNaoEncontradaComponent`.

Por fim, as rotas são inseridas no módulo por meio de um elemento do array `imports` resultante de uma chamada para o método `RouterModule.forRoot()`, passando como parâmetro o array de rotas, o a variável `rotas`.

Quando o software é executado o Angular busca uma combinação entre a URL fornecida no browser e as rotas definidas no módulo. Isso é feito de cima para baixo, procurando no array `rotas`. Ao encontrar uma rota correspondente, o Angular cria uma instância do componente e a apresenta. O modo de comparação entre a URL no browser e o caminho da rota é muito interessante: a forma de comparação considera se o caminho da rota combina com o final da URL do browser. 

Para exemplificar, considere que o usuário informa a URL `http://localhost:4200` no browser. Ao iniciar seu ciclo de execução o Angular começa a procurar a rota que combina com essa URL e descobre que é a **rota padrão**.

Note que a *ordem das rotas* no array de rotas (no exemplo, `rotas`) é realmente importante. Como disse, o Angular procura pela rota que combina com o final da URL, assim, se a rota padrão estiver no início da lista das rotas, então o Angular sempre a encontrará primeiro. Da mesma forma, se não encontrar uma rota correspondente o Angular vai até a rota `**` (por isso tem o comportamento de "página não encontrada").

### Shell component

Ao utilizar rotas o Angular atribui um comportamento especial a um dos componentes do projeto, chamando-o de **shell**. O componente **shell** será responsável por servir como uma "casca", contendo outros outros componentes. Nesse contexto, **shell** geralmente representa algo visual, até em termos de HTML. Por exemplo, considere um site com a seguinte estrutura:

* cabeçalho
* navegação
* conteúdo
* rodapé

Todas as páginas do site compartilham dessa mesma estrutura, diferenciando apenas no conteúdo. Assim, o **shell** contém as partes que não mudam: cabeçalho, navegação e rodapé. 

No Angular, o **shell** determina onde cada componente será apresentado por meio do elemento `router-outlet`. Por exemplo, o código a seguir apresenta um trecho do template do `AppComponent`:

```html
<h1>Notícias::Angular</h1>
<hr>
<nav>
...
</nav>
<hr>

<router-outlet></router-outlet>
```

Quando o Angular entrega o conteúdo para o browser ele executa o processo composto, resumidamente, por:

1. identificar o componente shell e obter seu template
2. identificar a rota e o componente que deve ser apresentado
3. obter o conteúdo do componente e inseri-lo no shell

Para o usuário, a experiência é a de ver uma "página" ou "tela" completa.


### Parâmetros de rota

A rota `noticias/:id`, como já visto, possui um **parâmetro de rota** chamado `id`. Obter o valor desse identificador da notícia na URL é uma tarefa importante desse processo de lidar com rotas no Angular. Para fazer isso, o componente `LeitorDeNoticiaComponent` possui o atributo `route`, uma instância de `ActivatedRoute`, como mostra o trecho de código a seguir:

```typescript
import { Component, OnInit } from '@angular/core';
import { NoticiasService } from '../noticias.service';
import { ActivatedRoute, Router } from '@angular/router';

...
export class LeitorDeNoticiaComponent implements OnInit {
  noticia = null;

  constructor(private noticias: NoticiasService,
    private route: ActivatedRoute,
    private router: Router) { }

  ngOnInit() {
    const id = this.route.snapshot.paramMap.get('id');
    this.noticia = this.noticias.encontrar(Number.parseInt(id));
  }

...
}
```

Como o componente implementa a interface `OnInit`, o código do método `ngOnInit()` é responsável por:

* **identificar o valor do parâmetro de rota**: isso é feito por meio de uma chamada para o método `route.snapshot.paramMap.get()`. O parâmetro para o método `get()` é o nome do parâmetro de rota desejado (nesse caso, `id`)
* **tratar o valor do parâmetro de rota**: o valor retornado por `get()` é do tipo `string`. O método `parseInt()` é utilizado para converter o valor da variável `id` para `number` antes de chamar o método `encontrar()` de `NoticiasService` e atribuir seu retorno para o atributo `noticia`.

### Navegação

A navegação é o recurso que permite mudar de uma rota para outra. Isso pode ser feito por meio de uma ação do usuário (por exemplo, quando ele clica em um link) ou via código.

A navegação por meio de link utiliza o elemento `a` e o atributo `routerLink`, como mostra o trecho do template do `AppComponent`:

```html
<h1>Notícias::Angular</h1>
<hr>
<nav>
  <a routerLink="">Home</a> |
  Admin: 
  <a routerLink="admin/noticias">Gerenciar notícias</a>
</nav>
<hr>
```

Perceba que o valor de `routerLink` está relacionado com o caminho da rota (como definido na variável `rotas`, no módulo `AppModule`). 

A outra forma de gerar navegação é via código. Para isso, considere o trecho do controller de `NoticiasRecentesComponent`:

```typescript
import { Component, OnInit } from '@angular/core';
import { Noticia } from '../noticia.model';
import { NoticiasService } from '../noticias.service';
import { Router } from '@angular/router';

...
export class NoticiasRecentesComponent implements OnInit {

  constructor(private noticias: NoticiasService,
    private router: Router) { }

  ngOnInit() {
  }

  mostrar(noticia: Noticia) {
    this.router.navigate(['noticias', noticia.id]);
  }

}
```

O método `mostrar()`, que é chamado no template no clique de cada item da lista de notícias para acessar o leitor da notícia, utiliza o atributo `router`, uma instância de `Router`. O método `navigate()` recebe como parâmetro um array cujo cada elemento é utilizado para compor a rota de navegação. Nesse caso, como o objeto é navegar até a rota `noticias/:id`, o array possui dois elementos: `'noticias'` e `noticia.id`.

## Padrão de trabalho (workflow)

Lidar com rotas no Angular é um processo simples, mas que aumenta de complexidade na proporção da quantidade de componentes, módulos ou na complexidade da arquitetura do software. Entretanto, lidar com esse processo contém alguns passos padrão (supondo que o projeto já tenha iniciado e adote a mesma estrutura do Angular CLI):

1. **criar componente**: a primeira coisa a fazer é criar o componente desejado. Como é importante conduzir esse processo de forma iterativa, não se preocupe em construir o componente por inteiro, deixe-o como criado pelo Angular CLI;
2. **definir rotas**: depois, defina as rotas e seus componentes (mantenha o componente com conteúdo padrão até aqui, para simplificar);
3. **implementar a lógica de negócio em um serviço**: ao utilizar serviços, implemente a lógica de negócio do serviço; e
4. **implementar lógica do componente e usar o serviço**: por fim, melhore a lógica do componente, fornecendo código para a lógica da interface e da lógica de negócio, e utilize o serviço.

Adotar esse padrão de trabalho pode tornar o desenvolvimento mais simples e rápido, bem como reduzir a quantidade de erros e permitir a identificação dos erros de forma mais ágil.
