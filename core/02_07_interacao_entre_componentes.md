# Interação entre componentes {#sec:interacao-componentes}

O Capítulo [-@sec:noticias-angular-crud-telas] apresenta uma implementação do **noticias-angular** com funcionalidades de listar notícias recentes, ler notícia, listar todas as notícias, cadastrar notícia, editar notícia e excluir notícia. Todas essas funcionalidades foram implementadas utilizando um só componente. Embora o software funcione perfeitamente foi necessário adotar uma lógica baseada em condicionais para troca da visibilidade de telas. A lógica fica complexa na medida da quantidade de funcionalidades, o que pode se tornar uma dor de cabeça para manutenções e entendimento do software.

Este capítulo apresenta uma solução alternativa: a utilização de componentes individuais para cada funcionalidade. Por ser um framework orientado a princípios e boas práticas de desenvolvimento de software, o Angular também proporciona recursos para aplicar essas boas práticas. Um deles é apresentado neste capítulo.

Este capítulo apresenta:

* comando do **Angular CLI** para criar componente
* configuração do módulo
* metadados `Input` e `Output`
* interação entre componentes
* componentes **host** e **guest**

## Clonar e executar localmente

Este capítulo está relacionado com o software **noticias-angular**, branch "componentes"^[O repositório do branch "componentes" está disponível em <https://github.com/jacksongomesbr/webdevbook-noticias-angular/tree/componentes> e você pode vê-lo em execução acessando <https://stackblitz.com/github/jacksongomesbr/webdevbook-noticias-angular/tree/componentes>]. Antes de prosseguir, clone e execute o projeto localmente.

## Criar componente com o Angular CLI

Para criar um componente com o **Angular CLI** usamos o comando `generate component`:

```{style=nonumber .sh}
ng g c NoticiasRecentes --spec false
```

O comando cria o componente `NoticiasRecentesComponent` na pasta `src/app/noticias-recentes`. A opção `--spec false` é utilizada para não criar arquivos de teste.

## Configuração do módulo

Cada componente precisa ser *declarado* por um módulo para poder ser utilizado no software. Isso significa que um módulo precisa conter o componente no array `declarations` dos seus metadados.

O **Angular CLI** faz essa configuração automaticamente, tornando o código do módulo assim:

```typescript
...
import { AppComponent } from './app.component';
import { NoticiasRecentesComponent } from './noticias-recentes/noticias-recentes.component';

@NgModule({
  declarations: [
    AppComponent,
    NoticiasRecentesComponent
  ],
  ...
})
export class AppModule { }
```

Uma vez que o componente está no array `declarations` de um módulo (nesse caso, o **root module**) ele pode ser utilizado em outros componentes. Mas o que significa isso? É o que mostra a próxima seção.

## Estrutura inicial do `NoticiasRecentesComponent`

O componente `NoticiasRecentesComponent` tem a responsabilidade de implementar a funcionalidade de apresentar a lista de notícias recentes. Veja bem: eu disse apresentar. Isso quer dizer que ele não tem a responsabilidade de obter ou conter a lista das notícias recentes, apenas de apresentá-las. 

Além disso, não é responsabilidade desse componente apresentar a tela de leitura da notícia. Entretanto, como cada item da lista de notícias recentes atende ao evento de clique, é necessário utilizar um recurso específico para este propósito.

Primeiro, o componente deve poder receber como entrada a lista de notícias. Isso é representado por meio de metadados:

```typescript
import { Component, OnInit, Input } from '@angular/core';

@Component({
...
})
export class NoticiasRecentesComponent implements OnInit {

  @Input()
  noticias;
...
}
```

Perceba a importação da função de anotação (metadado) `Input` e do seu uso junto do atributo `noticias`, como metadados. Os metadados podem ser usados também numa sintaxe alternativa:

```typescript
import { Component, OnInit, Input } from '@angular/core';

@Component({
...
})
export class NoticiasRecentesComponent implements OnInit {

  @Input() noticias;
...
}
```

O comportamento é o moesmo: indicar que o atributo `noticias` torna-se uma propriedade de entrada do componente.

## Utilizando o componente `NoticiasRecentesComponent`

Uma parte importante de cada componente é que seus metadados incluem o atributo `selector`, como mostra o trecho a seguir:

```typescript
...
@Component({
  selector: 'app-noticias-recentes',
  templateUrl: './noticias-recentes.component.html',
  styleUrls: ['./noticias-recentes.component.css']
})
export class NoticiasRecentesComponent implements OnInit {
...
}
```

O `selector` é uma string que representa um **seletor CSS**. Assim, qualquer seletor CSS é válido e aceito (seletor de elemento, classe, identificador, pseudo-seletor etc.). 

Os metadados do `NoticiasRecentesComponent` indicam que seu `selector` é `app-noticias-recentes`, ou seja, é um seletor de elemento. Isso quer dizer que esse componente deve ser utilizado no template de outro componente utilizando o seletor de elemento, ou seja, algo parecido com:

```html
<app-noticias-recentes></app-noticias-recentes>
```

Vamos chamar o componente que usa outro de **host** (hospedeiro) e o componente que é utilizado de **guest** (convidado). 

Assim, o código a seguir mostra um trecho do template do `AppComponent` (**host**):

```html
<h1>Notícias::Angular</h1>
<hr>
<nav>
...
</nav>
<hr>

<app-noticias-recentes 
    [noticias]="noticiasPublicadas()" 
    *ngIf="tela == 'home'">
</app-noticias-recentes>

<div *ngIf="tela == 'leitura' && leituraNoticia">
...
```

O template indica que está sendo utilizado o componente cujo seletor é `app-noticias-recentes`, ou seja, `NoticiasRecentesComponent` (**guest**). Ainda, a propriedade `noticias` do `NoticiasRecentesComponent` está vinculada, por meio de data-binding, ao método `noticiasPublicadas()` do `AppComponent`.

Isso demonstra uma interação bastante interessante entre os componentes:

* `AppComponent` (**host**) contém a lista de notícias e o método `noticiasPublicadas()`
* `AppComponent` utiliza o `NoticiasRecentesComponent` (**guest**) e, por meio de data-binding, vincula a propriedade de entrada `noticias` ao método `noticiasPublicadas()` do `AppComponent`

Outra coisa: a diretiva `NgIf` é aplicada diretamente ao elemento `app-noticias-recentes`, para demonstrar que as diretivas estruturais podem ser realmente aplicadas a elementos quaisquer^[Certamente o mesmo comportamento poderia ser obtido encapsulando o elemento `app-noticias-recentes` em um elemento `div`, aplicando neste último a diretiva `NgIf`].

-------------------------------------------

**Uso de componentes nos templates e instaciação**

Cada vez que um componente é utilizado no template o Angular cria uma instância do componente. Isso quer dizer que, a princípio, é possível criar mais de uma instância de um componente inserindo mais de um seletor correspondente. Experimente.

-------------------------------------------

## Criando eventos

Como você já sabe, cada item da lista de notícias pode ser clicado. Quando o clique acontece, o software mostra a tela de leitura da notícia. 

O desenvolvimento orientado a componentes envolve a arte de estruturar e pensar a arquitetura do software de tal maneira a elaborar as interações e relações entre os diversos componentes. 

Nesse sentido, a questão é que o componente `NoticiasRecentesComponent` pode, claramente, continuar utilizando os recursos de eventos do Angular. O problema é que, como a funcionalidade de apresentar a tela de leitura da notícia está fora dele, é necessário um meio que permita interagir com o componente **host**. A maneira de fazer isso é criando uma propriedade de saída, ou seja, um evento:

```typescript
import { Component, OnInit, Input, Output, EventEmitter } from '@angular/core';
import { Noticia } from '../noticia.model';

@Component({
..
})
export class NoticiasRecentesComponent implements OnInit {

  @Input()
  noticias;

  @Output()
  mostrou = new EventEmitter<Noticia>();

  ...

  mostrar(noticia) {
    this.mostrou.emit(noticia);
  }
}
```

O trecho de código mostra que, além de importar a anotação `Input`, também é necessário importar a anotação `Output` e a classe `EventEmitter`. A anotação `Output` é utilizada como metadado no atributo `mostrou`, que recebe uma instância de `EventEmitter`. Novamente, vale a sintaxe alternativa:

```typescript
...
export class NoticiasRecentesComponent implements OnInit {

  @Input() noticias;

  @Output() mostrou = new EventEmitter<Noticia>();

  ...
}
```

A sintaxe `construtor<tipo>()` pertence ao recurso chamado de **generics** e serve para indicar que o tipo de dados utilizado no evento é `Noticia`^[Leia mais sobre **generics** na documentação do TypeScript: <http://www.typescriptlang.org/docs/handbook/generics.html>.]. Cada evento contém um objeto com informações adicionais e isso será muito útil a seguir.

Ainda do lado do controller há o método `mostrar()`, que recebe o parâmetro `noticia` e chama o método `emit()` do atributo `mostrou`, passando como argumento o parâmetro `noticia`. Isso indica que o **componente host** terá disponível no tratador de evento o objeto `noticia` que corresponde ao que foi clicado pelo usuário.

No **componente host** o elemento completo, no tempate, fica como:

```html
<app-noticias-recentes 
    [noticias]="noticiasPublicadas()" 
    (mostrou)="onMostrou($event)" 
    *ngIf="tela == 'home'">
</app-noticias-recentes>
```

O elemento `app-noticias-recentes` tem, além da propriedade `noticias` e da diretiva `NgIf`, o evento `mostrou`. O evento `mostrou` está vinculado ao método `onMostrou()`, definido no `AppComponent`. É importante notar que o template chama o método `onMostrou()` e informa, como argumento, o valor especial `$event`. `$event` representa o dado associado ao evento conforme definido pelo **componente guest** ao chamar o método `emit()`. A seguir o código do método `onMostrou()`: 

```typescript
onMostrou(noticia) {
    this.leituraNoticia = noticia;
    this.irPara('leitura');
}
```

O método `onMostrou()` recebe o parâmetro `noticia`. Seu corpo é o mesmo do método `mostrar()` do branch `crud-telas`:

* atribui o parâmetro `noticia` ao atributo `leituraNoticia`
* chama o método `irPara()` com o argumento `leitura`, indicando que deve mostrar a tela **leitura** da notícia.

## Conclusão

Este capítulo apresentou:

* comando do **Angular CLI** para criar componente
* configuração do módulo para declarar um componente
* metadados `Input` e `Output`
* interação entre componentes
* componentes **host** e **guest**
