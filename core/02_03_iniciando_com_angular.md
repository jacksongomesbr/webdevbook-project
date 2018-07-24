# Iniciando com o Angular {#sec:angular-iniciando}

O **Angular** é um framework para o desenvolvimento de software front-end. Isso quer dizer que utiliza tecnologias padrão do contexto web como HTML, CSS e uma linguagem de programação como JavaScript ou TypeScript [@angularhome].

Um software desenvolvido em Angular é composto por diversos elementos como: módulos, componentes, templates e serviços. Esses elementos fazem parte da arquitetura do Angular, que é ilustrada pela figura a seguir.

![](./graphics/visao-geral-da-arquitetura-do-angular.png)

Essa arquitetura de software orientada a componentes implementa conceitos de dois padrões de arquitetura de software:

* **MVC** (de *Model, View, Controller*) é um padrão de software que separa a representação da informação (Model) da interação do usuário com ele (View-Controller). Geralmente, Model e Controller são representados por código em linguagem de programação (classes e/ou funções) e View é representado por HTML e CSS [@wiki:mvc].
* **MVVM** (de *Model, View, View-Model*) é um padrão de software semelhante ao MVC, com a diferença de que o View-Model utiliza recurso de **data binding** (mais sobre isso depois) para fazer com que a View seja atualizada automaticamente quando ocorrer uma modificação no Model [@wiki:mvvm].

No contexto do Angular esses elementos são descritos conforme as seções a seguir.

## Elementos da Arquitetura do Angular

### Módulos

Módulos representam a forma principal de modularização de código. Isso significa que um módulo é um elemento de mais alto nível da arquitetura do Angular e é composto por outros elementos, como componentes e serviços.

Um software desenvolvido em Angular possui pelo menos um módulo, chamado **root module** (módulo raiz). Os demais módulos são chamados **feature modules** (módulos de funcionalidades).

### Bibliotecas

Bibliotecas funcionam como um agrupador de elementos de software desenvolvido em Angular. Bibliotecas oficiais têm o prefixo `@angular`. Geralmente é possível instalar bibliotecas utilizando o **npm** (gerenciador de pacotes do **Node.Js**).

Uma biblioteca pode conter módulos, componentes, diretivas e serviços.

### Componentes

Um componente está, geralmente, relacionado a algo visual, ou seja, uma tela ou parte dela. Nesse sentido, um componente possui código (**Controller**) que determina ou controla o comportamento da interação com o usuário (**View** ou **Template**).

O **Template** determina a parte visual do componente e é definido por código HTML e CSS, além de recursos específicos do Angular, como outros componentes e diretivas.

### Metadados

Metadados são um recurso do Angular para adicionar detalhes a classes. Isso é utilizado para que o Angular interprete uma classe como um Módulo ou como um Componente, por exemplo.

Os metadados utilizam o conceito de **decorators** do TypeScript, que permitem formas de adicionar informações (metadados) a classes e membros de classe (atributos e métodos) [@typescripthome].

### Data binding {#sec:angular-data-binding}

Data binding (que seria algo como "vinculação de dados" em português) é um reucrso do Angular que representa um componente importante da sua arquitetura. Considere os seguintes elementos:

* um Model define dados que serão apresentados no Template
* um Template apresenta os dados do Model
* um Controller ou um View-Model determina o comportamento do Template

Se o Controller atualiza o Model, então o Template tem que ser atualizado automaticamente. Se o usuário atualiza o Model por meio do Template (usando um formulário, por exemplo) o Controller também precisa ter acesso ao Model atualizado. O Data Binding atua garantindo que esse processo ocorra dessa forma.

A **sintaxe de data binding** é o mecanismo que controla a ordem entre fonte e destino dos dados:

* **one-way de fonte para view**: pode ser de cinco tipos: **Interpolation**, **Property**, **Attribute**, **Class** e **Style**. A sintaxe de interpolação é `{{expressao}}` para apresentar o valor de `expressao` no Template ou `[target]="expressao"` para que `target` (por exemplo, uma propriedade) receba o valor de `expressao`
* **one-way de view para destino**: pode ser do tipo **Event**. A sintaxe de evento é `(evento)="instrucao"`, indica para o Angular que deve executar `instrucao` no tratador do `evento`
* **two-way**: a sintaxe é `[(alvo)]="expressao"` 

### Diretivas

Diretivas representam um conceito do Angular que é um pouco confuso. Na prática, um Componente é uma Diretiva com um Template. Assim, um Componente é um tipo de Diretiva, que nem sempre está relacionada a algo visual. Angular define dois tipos de diretivas:

* **Diretivas Estruturais**: modificam o Template dinamicamente por meio de manipulação do DOM, adicionando ou removendo elementos HTML
* **Diretivas de Atributos**: também modificam o Template, mas operam sobre elementos HTML já existentes

### Serviços

Um Serviço é uma abstração do Angular utilizado para isolar a lógica de negócio de Componentes. Na prática, um Serviço é representado por uma classe com métodos que podem ser utilizados em Componentes. Para isso, para que um Componente utilize um serviço, o Angular utiliza o conceito de **Injeção de Dependência** (DI, do inglês **Dependency Injection**). DI é um padrão de software que faz com que dependências sejam fornecidas para quem precisar. Na prática, o Angular identifica as dependências de um Componente e cria automaticamente instâncias delas, para que sejam utilizadas posteriormente no Componente [@wiki:di].

Enquanto esses elementos da Arquitetura do Angular representam conceitos, é importante visualizar a relação deles com elementos práticos do software, ou seja, código. Para isso, a seção a seguir apresenta a estrutura padrão de um software desenvolvido em Angular.

## Estrutura padrão de um software desenvolvido em Angular

Um software desenvolvido em Angular é representado por vários arquivos HTML, CSS, TypeScript e de configuração (geralmente arquivos em formato **JSON**).


\dirtree{%
 .1 e2e.
 .1 node\_modules.
 .1 src.
 .1 .editorconfig.
 .1 .gitignore.
 .1 angular.json.
 .1 package.json.
 .1 package-lock.json.
 .1 README.md.
 .1 tsconfig.json.
 .1 tslint.json.
}

No diretório raiz do software:

* `e2e`: contém especificações de testes **end-to-end**
* `node_modules`: contém os pacotes (dependências)
* `src`: contém o código-fonte (módulos, componentes etc.)
* `angular.json`: contém configurações do projeto Angular (nome, scripts etc.)
* `package.json`:  contém as especificações dos pacotes utilizados no projeto
* `tsconfig.json` e `tslint.json`:  contêm configurações do processo de tradução de código TypeScript para JavaScript

No diretório `src` também há uma estrutura importante:

\dirtree{%
 .1 src.
 .2 app.
 .2 assets.
 .2 environments.
 .2 browserlist.
 .2 favicon.ico.
 .2 index.html.
 .2 karma.conf.js.
 .2 main.ts.
 .2 polyfills.ts.
 .2 styles.css.
 .2 test.ts.
 .2 tsconfig.app.json.
 .2 tsconfig.spec.json.
 .2 tslint.json.
}

* `app`: contém o **root module** e os demais **feature modules** do projeto
* `assets`: contém arquivos CSS, JSON e scripts, por exemplo
* `environments`: contém arquivos de configuração do ambiente de desenvolvimento (`environment.ts`) e de produção (`environment.prod.ts`)
* `index.html`: contém o código HTML inicial para o projeto e inclui o componente principal do **root module**
* `main.ts`: contém o código TypeScript necessário para iniciar o software (processo chamado de **Bootstrap**)
* `polyfills.ts`: contém código TypeScript que indica scripts adicionais a serem carregados pelo Browser para funcionamento do software como um todo e para questões de compatibilidade com versões antigas de Browsers
* `style.css`: contém o código CSS para definir estilos globais para o software
* `tsconfig.app.json`: complementa configurações do arquivo `tsconfig.json` específicas para o software em questão

No diretório `app` há os arquivos:

* `app.component.css`, `app.component.html` e `app.component.ts`: definem o component `AppComponent`, respectivamente: apresentação por meio de CSS, **Template** e **Controller**. Basicamente, estes três arquivos formam a base de todo componente
* `app.module.ts`: define o **root module** (`AppModule`)

Esse capítulo apresentou conceitos importantes do Angular. Sempre que necessário, volte a esse capítulo para revisar conceitos do Angular. Muito provavelmente, mesmo desenvolvedores experientes precisem, de tempos em tempos, rever essas definições da arquitetura do Angular.

