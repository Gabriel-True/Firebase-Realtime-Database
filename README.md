# Firebase-Realtime-Database
Entender as regras do Firebase Realtime Database

## :lock: Entenda as regras do Firebase - Realtime Database

As regras de segurança do Firebase Realtime Database determinam quem tem acesso de leitura e gravação ao banco de dados, como os dados são estruturados e quais índices são definidos. Essas regras residem nos servidores do Firebase e são sempre aplicadas automaticamente. As solicitações de leitura e gravação só serão concluídas se as regras permitirem. Por padrão, suas regras não concedem acesso ao banco de dados. A finalidade é proteger o banco de dados contra uso indevido até que você personalize as regras ou configure a autenticação.

A sintaxe das regras de segurança do Realtime Database é parecida com a do JavaScript e vêm em quatro tipos:

###### Tipos de regra:

``````````````
.read
``````````````
Descreve-se quando os dados podem ser lidos pelos usuários.

`````````````
.write
`````````````
Descreve-se quando os dados podem ser gravados.

````````````
.validate
````````````
Define a formatação correta do valor, o tipo de dados e se o valor tem atributos filhos.

````````````
.indexOn
`````````````
Especifica um filho como índice para que a ordenação e consulta sejam possíveis.

### Autorização

A identificação do usuário é apenas uma parte do processo de segurança. Uma vez identificado, é preciso controlar o acesso dele ao banco de dados. Com as regras de segurança do Realtime Database, é possível controlar o acesso de cada usuário. Este conjunto de regras de segurança, por exemplo, permite que qualquer pessoa leia o caminho ` /foo/ `, mas não faça gravações nele:

```
{
  "rules": {
     "foo": {
        ".read": true,
        ".write": false
      }
     } 
    }
```

As regras ` .read ` e ` .write ` são aplicadas em cascata. Portanto, esse conjunto de regras concede acesso de leitura a qualquer dado no caminho ` /foo/ `, bem como em níveis inferiores, como ` /foo/bar/baz `. Observe que as regras ` .read ` e ` .write ` são aplicadas em níveis superiores no banco de dados. Portanto, o acesso de leitura a ` /foo/bar/baz ` ainda será concedido neste exemplo, mesmo que uma regra no caminho ` /foo/bar/baz ` seja avaliada como falsa.

As regras de segurança do Realtime Database incluem variáveis integradas e funções que permitem referir-se a outros caminhos, carimbos de data/hora do servidor e informações de autenticação, e muito mais. Confira um exemplo da regra que concede acesso de leitura para usuários autenticados em ` /users/<uid>/ `, em que ` <uid> ` é o ID do usuário obtido pelo Firebase Authentication.

```
{
   "rules": {
      "users": {
          "$uid": {
             ".write": "$uid === auth.uid"
           }
         }
       }
     }   
```
      
### Validação de dados

O Firebase Realtime Database não usa esquemas, o que facilita fazer alterações durante o desenvolvimento. No entanto, quando o app estiver pronto para distribuição, será importante manter a consistência dos dados. A linguagem de regras inclui uma regra `.validate`, que permite aplicar a lógica de validação usando as mesmas expressões usadas pelas regras `.read` e `.write`. A única diferença é que, como as regras de validação não são aplicadas em cascata, todas as regras de validação relevantes são avaliadas como verdadeiras para que a gravação seja permitida.

De acordo com essas regras, os dados gravados em `/foo/` precisam ser uma string com menos de 100 caracteres:

```
{
   "rules": {
   "foo": {
      ".validate": "newData.isString() && newData.val().length < 100"
      }
    }
  }
 ```
    
As regras de validação têm acesso às mesmas funções e variáveis incorporadas que as regras `.read` e `.write`. Com elas, você cria regras de validação que reconhecem os dados de qualquer local no banco de dados, as identidades de usuário, o horário do servidor e muito mais.
  
### Como definir índices de banco de dados

No Firebase Realtime Database, ordene e consulte dados. Para pequenos volumes de dados, é possível fazer consultas ad hoc no banco de dados. Por isso, os índices geralmente não são necessários durante o desenvolvimento. Antes de lançar o app, é importante especificar índices para todas as consultas existentes. Isso garante que elas continuem funcionando mesmo com o aumento do volume de dados do app.

Os índices são especificados usando a regra `.indexOn`. Confira um exemplo de declaração de índice que indexaria os campos de altura e comprimento de uma lista de dinossauros:
    
```
  {
     "rules": {
         "dinossauros": {
             ".indexOn": ["height", "length"]
          }
       }
    }
```
    
### Conhecendo as Security Rules
  
As regras de segurança não são nada mais que uma árvore JSON. E em árvores, temos 3 conceitos conhecidos: `nó`, `chave` e `valor`.

![Imagem 1](https://github.com/Gabriel-True/Firebase-Realtime-Database/blob/main/images.jpeg)

Todas as nossas regras ficam em um `nó` denominado “rules”. E como deu para notar, nesse nó temos 2 `chaves`: “.read” e “.write”. Nas nossas regras, os `valores` destas 2 `chaves` têm de ser do tipo booleano para indicar as condições de acesso à base de dados(`true` para permitir o acesso e/ou `false` para negar o acesso).

#### Os nós
 
Como já vimos, a base de dados começa com o nó “rules”. Mas podemos definir sub-nós para cada nó da Realtime Database. (Meio confuso, eu sei :smile:)

Vamos ver um exemplo. Se a nossa Realtime Database tiver 2 nós: animes e posts.

```
{
    "animes":{
          //…lista de animes
     },
     "posts":{
          //…lista de posts
     }
   }
```

 Podemos definir regras diferentes para estes nós, basta criar 2 sub-nós em “rules”:      

   ```
    {
      "rules":{
           "animes":{
                 ".read":"auth != null",
                 ".write":"auth.uid === 'bZk15h2SU6T3u7FS3TSefJCs0mI3'"
          },
           "posts":{  
                 ".read":"auth != null",   
                 ".write":"auth != null"
                }
            }
         }
```

No sub-nó `"animes"` definimos que o usuário logado poderá lê o que está escrito, mas somente o usuário que estiver com a uid `"bZk15h2SU6T3u7FS3TSefJCs0mI3"` poderá escrever no database. Como um usuário admin, onde os usuários vão poder lê o que está escrito, mas somente o usuário (admin) que estiver com essa uid poderá escrever/alterar no database.

No sub-nó `"posts"` definimos que qualquer usuário logado poderá lê e escrever no database.

### Os valores

Como já tinha dito, os valores de “.read” e de “.write” devem sempre retornar um `boolean`. E para fazer isso, o Firebase dá-nos acesso à mais algumas variáveis:

#### auth

Já conhecíamos esta variável, ela representa um utilizador autenticado. Através dela podemos acessar 3 outras variáveis: `uid`(id do utilizador autenticado), `provider`(método de login utilizado, pode ser “password”, “facebook”, “google”, “twitter”, “github” ou “anonymous”) e `token` que contém mais variáveis (como por exemplo o nome e email do utilizador autenticado);

Podemos utilizar esta variável, por exemplo, para que os utilizadores autenticados acessem apenas os seus próprios dados:
    
```
"usuarios":{
     "$uid":{
        ".write":"auth!=null && $uid == auth.uid",
        ".read":"auth!=null && $uid == auth.uid"
        }
      }
```
Com estas regras, o usuário Paulo não tem permissão para aceder aos dados do João.
    
#### $ (cifrão)

De certeza que você viu este símbolo no exemplo anterior. Tal como no PHP, ele serve para indicar que trata-se de uma variável. A ideia de declarar estas variáveis não é atribuir valores à elas, mas sim utilizar elas para comparar os seus valores com outros, tal como no exemplo anterior onde comparamos se o `uid` do utilizador que está na base de dados (variável `$uid`) é igual ao uid do utilizador que fez o login no firebase auth (variável `auth.uid`).
 
#### root

Tal como o nome indica, esta variável corresponde ao nó raiz da nossa base de dados. Através dela podemos aceder à outros dados que estão fora do nó que estamos a tentar ler/escrever.

#### data

Esta variável contem os dados que estão atualmente nesse nó onde pretendemos ler/escrever.

#### newData

Diferentemente da variável `data`, esta variável só pode ser acedida quando estamos a fazer uma escrita (.write) ou validação (.validate). Ela contém os dados que serão escritos nesse nó. Podemos utilizar em conjunto com a variável `data`, por exemplo, quando estamos a atualizar a data em que um utilizador esteve online pela última vez:

```
"uid1":{
  "nome":"Paulo",
  "visto":"201802282030"
  //timestamp da data em que o utilizador foi visto pela última vez
    }
 ```

### Métodos/Funções adicionais

As variáveis `root`, `data` e `newData` são como se fossem os `DataSnapshot` que nós conhecemos (sim, aqueles que utilizamos quando lemos dados da Realtime Database). Isso significa que eles têm os mesmos métodos que existem em um `DataSnapshot`:

- `val()`
- `child()`
- `parent()`
- `hasChild()`
- `exists()`
- `getPriority()`

Mas para além destes métodos, existem mais 3 métodos para verificar o tipo de dado:

- `isString()`
- `isNumber()`
- `isBoolean()`

E é tudo para este artigo. Espero que você tenha aprendido a utilizar as regras de segurança e que comece a implementar nos seus projetos (se ainda não o fazia) para termos aplicações mais seguras. :wink:
