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
  



    

  




 

