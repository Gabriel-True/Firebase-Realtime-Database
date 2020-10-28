# Firebase-Realtime-Database
Entender as regras do Firebase Realtime Database

## :lock: Entenda as regras do Firebase - Realtime Database

As regras de segurança do Firebase Realtime Database determinam quem tem acesso de leitura e gravação ao banco de dados, como os dados são estruturados e quais índices são definidos. Essas regras residem nos servidores do Firebase e são sempre aplicadas automaticamente. As solicitações de leitura e gravação só serão concluídas se as regras permitirem. Por padrão, suas regras não concedem acesso ao banco de dados. A finalidade é proteger o banco de dados contra uso indevido até que você personalize as regras ou configure a autenticação.

A sintaxe das regras de segurança do Realtime Database é parecida com a do JavaScript e vêm em quatro tipos:

###### Tipos de regra:

``````````````
.read
``````````````
Descreve se e quando os dados podem ser lidos pelos usuários.

`````````````
.write
`````````````
Descreve se e quando os dados podem ser gravados.



