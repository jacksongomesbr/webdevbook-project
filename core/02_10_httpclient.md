# Comunicação com back-end por meio do HttpClient

Browsers atuais permitem a comunicação entre um software front-end e um back-end (ex: uma API HTTP) por meio da interface `XmlHttpRequest` (XHR). 

O módulo `@angular/common/http` fornece o `HttpClient`, um serviço para comunicação com back-ends por meio do `XmlHttpRequest`. 

## Configuração do módulo

Para utilizar o `XmlHttpRequest` é necessário configurar o módulo do software Angular, importando `HttpClientModule` (fornecido pelo pacote `@angular/common/http`), como mostra o trecho de código a seguir:

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { RouterModule, Routes } from '@angular/router';
import { NgbModule } from '@ng-bootstrap/ng-bootstrap';
import { HttpClientModule } from '@angular/common/http';

import { AppComponent } from './app.component';
...

const rotas: Routes = [
...
];

@NgModule({
  declarations: [
    AppComponent,
    ...
  ],
  imports: [
    NgbModule,
    BrowserModule,
    FormsModule,
    RouterModule.forRoot(rotas),
    HttpClientModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Uma vez que o `HttpClientModule` é importado no módulo, qualquer elemento do software (ex: componente ou serviço) pode injetar o `HttpClient`. A seguir, um trecho de código do `NoticiasService`: 

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
...

@Injectable({
  providedIn: 'root'
})
export class NoticiasService {
  API_URL = 'http://localhost:8000/api/noticias/';

  constructor(private http: HttpClient) {
  }
  ...
}
```

O código importa a classe `HttpClient`, fornecida por `@angular/common/http` e a injeta no serviço `NoticiasService` como o atributo `http`. 

Depois dessa configuração inicial o software pode utilizar o `HttpClient` para requisições HTTP, como demonstram as seções a seguir.

## Recebendo dados de um arquivo JSON

Uma forma interessante de distribuir dados no software é codificá-los no formato JSON em um arquivo. Isso também é interessante porque qualquer servidor web (incluindo o servidor de desenvolvimento local) pode entregar esse arquivo. Por exemplo, considere que os dados de uma notícia estejam no arquivo `assets/db.json` e o serviço `NoticiasService` deseja consultar seu conteúdo. 

Primeiro, o conteúdo do arquivo JSON:

```json
[
    {
        "id": 1,
        "titulo": "Os seis atos do jogo de equipe da Mercedes que tirou vitória de Bottas e beneficiou Hamilton"
    },
    {
        "id": 2,
        "titulo": "Passa de 800 o número de mortos na Indonésia devastada por terremoto e tsunami"
    }
]
```

O arquivo representa uma lista com dois objetos que possuem a mesma estrutura: dois atributos:

* id (um número)
* titulo (uma string)

Segundo, um trecho de código de `NoticiasService`:

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable({
  providedIn: 'root'
})
export class NoticiasService {
  DB_URL = 'assets/db.json';

  constructor(private http: HttpClient) {
  }

  public todas() {
    return this.http.get(this.DB_URL);
  }
}
```

O método `todas()` utiliza o objeto `http` e seu método `get()` para realizar uma requisição HTTP para a URL que direciona para o arquivo JSON.

Para consumir `NoticiasService` o componente `NoticiasRecentesComponent` utiliza injeção de dependência e o método `subscribe()`:

```typescript
import { Component, OnInit } from '@angular/core';
import { NoticiasService } from '../noticias.service';

@Component({
  selector: 'app-noticias-recentes',
  templateUrl: './noticias-recentes.component.html',
  styleUrls: ['./noticias-recentes.component.css']
})
export class NoticiasRecentesComponent implements OnInit {
  noticias = null;

  constructor(private noticias: NoticiasService) { }

  ngOnInit() {
    this.noticias.todas()
      .subscribe(noticias => this.noticias = noticias);
  }
}
```

O método `todas()` do `NoticiasService` retorna um `Observable`, que ainda não representa os dados de notícias, em si. Para acessar os dados de notícias é utilizado o método `subscribe()`, que recebe uma *callback* com um parâmetro (`noticias`) e copia seu valor para o atributo `noticias` do componente. 

Também é possível passar uma *callback* para o método `subscribe()` para tratar erros (como erro de conexão ou erro de execução no servidor, por exemplo). Para isso, basta usar o segundo parâmetro:

```typescript
ngOnInit() {
    this.noticias.todas()
        .subscribe(
            noticias => this.noticias = noticias,
            erro => console.log(erro)
        );
}
```

A segunda *callback* recebe o parâmetro `erro`, um objeto que contém detalhes do erro.

Posteriormente, as notícias são apresentadas no template:

```html
<h2 class="mb-4"><i class="fa fa-newspaper-o"></i> Notícias recentes</h2>
<div class="card-deck">
  <div class="card" *ngFor="let noticia of noticias">
      <div class="card-body">
        <h5 class="card-title">{{noticia.titulo}}</h5>
      </div>
  </div>
</div>
```

A diretiva `*ngFor` é utilizada no elemento `div.card` para apresentar a lista das notícias.

## Comunicação assíncrona e o `Observable`

O `HttpClient` implementa um **modelo de programação assíncrona** que é utilizado para consumir dados de uma API HTTP e também para suportar passagem de mensagens entre **editores** (ou **publicadores**) e **assinantes**.

Esse modelo de programação permite *atrasar* a execução de um código, executando-o apenas quando for recebida uma resposta. Isso combina muito bem com o modelo de comunicação assíncrona do `XmlHttpRequest` porque os elementos do modelo de programação assíncrona podem ser traduzidos diretamente, assim:

* **publicador**: código que cria uma requisição HTTP (ex: método `get()` do `HttpClient`)
* **assinante**: método `subscribe()` do `Observable`

A [@fig:10-httpclient-diagrama] ilustra ainda mais esse processo por meio de um diagrama de sequência.

```{#fig:10-httpclient-diagrama .plantuml caption="Diagrama de sequência da consulta da lista de notícias" format="eps"}
@startuml

autonumber "<b>[0]"

actor Usuario
boundary "Front-end" as Front
control NoticiasService
boundary "Back-end" as Back
database DB

Usuario -> Front : Ver lista das notícias
Front -> NoticiasService : todas().subscribe(cb1, cb2)
NoticiasService -> Back : GET /noticias
Back -> DB : consulta Noticias
alt 200 (OK)
    DB -> Back : registros
    Back -> NoticiasService : JSON (noticias)
    NoticiasService -> Front : chama callback cb1 (sucesso)
    Front -> Usuario : lista das notícias
else 500 (Erro interno do servidor)
    DB -> Back : erro de acesso ao banco de dados
    Back -> NoticiasService : JSON (erro)
    NoticiasService -> Front : chama callback cb2 (erro)
    Front -> Usuario : mensagem de erro
end

@enduml
```

Os passos representados no diagrama são os seguintes:

1. O **Usuário** utiliza o **Front-end** (componente) para ver a lista das notícias
2. O **Front-end** chama o método `todas()` do **NoticiasService** (o publicador) e, na sequência, chama o método `subscribe()` para **assinar** o conteúdo, executando as callbacks *cb1* ou *cb2*
3. O **NoticiasService** cria uma requisição HTTP GET para o **Back-end** e fica aguardando uma resposta
4. O **Back-end** atende a requisição e realiza uma consulta no **DB** para obter os dados
5. Se a execução no **Back-end** ocorrer com sucesso, ele vai retornar um código *200 (OK)* para o **NoticiasService**. Antes, o **DB** retorna os registros para o **Back-end**
6. O **Back-end** retorna os registros para o **NoticiasService** em formato JSON
7. O **NoticiasService** retorna a lista de notícias para a callback *cb1* na forma de lista (array de objetos, convertido do JSON)
8. O **Front-end** apresenta a lista das notícias para o **Usuário**
9. Se a execução no **Back-end** ocorrer com erro, ele vai retornar um código *500 (Erro interno do servidor)* para o **NoticiasService**. Antes, o **DB** retorna um erro para o **Back-end**
10. O **Back-end** retorna um JSON para o **NoticiasService** que representa o erro que ocorreu no servidor
11. O **NoticiasService** retorna o erro para a callback *cb2* na forma de um objeto (convertido do JSON)
12. O **Front-end** apresenta uma mensagem de erro para o **Usuário**


Importante notar que o Front-end e o NoticiasService fazem parte de um software (o próprio Front-end) e o Back-end e o DB fazem parte de outro software (o próprio Back-end). Assim, esse modelo de programação assíncrona permite a comunicação entre dois softwares independentemente das suas plataformas de execução. Por exemplo, o Front-end pode ser desenvolvido em Angular, enquanto o Back-end, em Python.


