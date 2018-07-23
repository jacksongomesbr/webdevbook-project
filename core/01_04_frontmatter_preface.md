# Prefácio

Este é um livro open-source, tanto no conteúdo quanto no código-fonte associado. O conteúdo é resultado de algumas práticas com o **Framework Angular** e segue uma abordagem prática, com foco no entendimento de conceitos e tecnologias no contexto do desenvolvimento de um software web.

Um *framework* representa um modelo, uma forma de resolver um problema. Em termos de desenvolvimento de software para a web um framework fornece ferramentas (ie. código) para o desenvolvimento de aplicações. Geralmente o propósito de um framework é agilizar as atividades de desenvolvimento de software, inclusive, fornecendo código pronto (componentes, bibliotecas etc.) para resolver problemas comuns, como uma interface de cadastro.

O objetivo deste livro é fornecer uma ferramenta para o desenvolvimento de habilidades de desenvolvimento web com Angular, com a expectativa de que você comece aprendendo o básico (o "hello world") e conclua com habilidades necessárias para o desenvolvimento de software que consome dados e interage com uma API HTTP REST, por exemplo.

## Fonte de referência e versão do Angular

Parte do conteúdo do livro é baseada na documentação oficial do Angular, disponível em [https://angular.io](https://angular.io).

Como o Angular é um projeto em constante desenvolvimento \(pelo menos até agora\) serão publicadas atualizações no conteúdo do livro sempre que possível, para refletir novos recursos e funcionalidades. No momento, o conteúdo do livro é baseado na versão **6.0.0**.


## Convenções

Os trechos de código apresentados no livro seguem o seguinte padrão:

* **comandos**: devem ser executados no prompt; começam com o símbolo `$`
* **códigos-fontes**: trechos de códigos-fontes de arquivos

A seguir, um exemplo de comando:

```{style=nonumber .sh}
$ mkdir hello-world
```

O exemplo indica que o comando `mkdir`, com a opção `hello-world`, deve ser executado no prompt para criar uma pasta com o nome `hello-world`.

A seguir, um exemplo de código-fonte:

```python
class Pessoa:
    pass
```

O exemplo apresenta o código-fonte da classe `Pessoa`. Em algumas situações, trechos de código podem ser omitidos ou serem apresentados de forma incompleta, usando os símbolos `...` e `#`, como no exemplo a seguir:

```python
class Pessoa:
    def __init__(self, nome):
        self.nome = nome
    
    def salvar(self):
        # executa validação dos dados
        ...
        # salva 
        return ModelManager.save(self)
```

## Conhecimentos desejáveis

Este livro aborda o desenvolvimento de software front-end para a web do ponto-de-vista do Angular. Isso quer dizer que não trata de conceitos iniciais de HTML, CSS, JavaScript, TypeScript e Bootstrap. Entretanto, os conceitos fundamentais dessas tecnologias vão sendo apresentados no decorrer dos capítulos, conforme surge a necessidade deles.

Para aprender mais sobre essas tecnologias recomendo essas fontes:

* **TypeScript**: [Documentação oficial do TypeScript - Microsoft](https://www.typescriptlang.org/docs/home.html), [TypeScript Deep Dive](https://www.gitbook.com/book/basarat/typescript/details)
* **HTML, CSS e JavaScript:** [W3Schools](https://www.w3schools.com/)
* **Boostrap**: [Documentação oficial do Bootstrap](http://getbootstrap.com/docs/4.0/getting-started/introduction/)

Este livro não leva em consideração o Sistema Operacional do seu ambiente de desenvolvimento, mas é importante que você se acostume a certos detalhes e a certas ferramentas, como o **prompt** ou **prompt de comando**. 

Além destas ferramentas também são utilizadas:

* **Node.js**: disponível em [https://nodejs.org](https://nodejs.org) representa um ambiente de execução do JavaScript fora do browser e também inclui o **npm**, um gerenciador de pacotes
* **Editor de textos ou IDE**: atualmente há muitas opções, mas destaco o **VisualStudioCode**, disponível em [https://code.visualstudio.com/](https://code.visualstudio.com/)
* **Git**
* **Heroku**

O **Git** é um gerenciador de repositórios com recursos de versionamento de código. É uma ferramenta essencial para o gerenciamento de código fonte de qualquer software.

O **Heroku** é um serviço de **PaaS** (de *Platform-as-a-Service*). PaaS é um modelo de negócio fornece um ambiente de execução conforme uma plataforma de programação, como o Python, um tecnologia de banco de dados, como MySQL e PostgreSQL e ainda outros recursos, como cache usando Redis.

> **Calma!** Não pira! (In)Felizmente você não vai usar todas as tecnologias lendo o conteúdo desse livro. Fica para outra oportunidade.

Para utilizar o Heroku você precisa criar uma conta de usuário. Acesse [https://www.heroku.com/](https://www.heroku.com/) e crie uma conta de usuário.

Depois que tiver criado e validado sua conta de usuário instale o **Heroku CLI**, uma ferramenta de linha de comando (prompt) que fornece uma interface de texto para criar e gerenciar aplicativos Heroku. Detalhes da instalação dessa ferramenta não são tratados aqui, mas comece acessando [https://devcenter.heroku.com/articles/heroku-cli](https://devcenter.heroku.com/articles/heroku-cli).

