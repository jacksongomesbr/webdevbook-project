# Configuração do ambiente de desenvolvimento {#sec:apendice-1}
 
## Node.js

O **Node.js** é um ambiente de execução do JavaScript independente do browser e multiplataforma [@nodejshome]. Nos projetos desse livro é necessário utilizar **Node.js** e também a ferramenta **npm**, um gerenciador de pacotes JavaScript para o **Node.js** [@npmjshome].

A instalação do **Node.js** é simples, bastando acessar [https://nodejs.org/en/download/](https://nodejs.org/en/download/) para obter os binários de instalação conforme a plataforma desejada. O **npm** também é fornecido junto com a instalação do **Node.js**.

Para verificar se seu ambiente de execução do **Node.js** está operando normalmente, execute os comandos a seguir em um prompt:

```{style=nonumber .sh}
$ node -v
$ npm -v
```

A saída dos programas apresenta, respectivamente, as versões do **Node.js** e do **npm** instaladas, como:

```{style=nonumber .sh}
v10.5.0
6.2.0
```

## Angular CLI

O **Angular CLI** é fornecido como um pacote **npm**, então deve ser instalado da seguinte forma:

```{style=nonumber .sh}
$ npm install -g @angular/cli
```

O comando `install` seguido da opção `-g` faz uma *instalação global* do **Angular CLI**, o que significa que ele estará disponível para qualquer usuário.

