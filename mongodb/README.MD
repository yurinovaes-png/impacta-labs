## 1. Fundamentos do MongoDB

### 1.1 Introdução 
Nosso primeiro passo será nos familiarizar com os componentes principais do MongoDB, ajudando você a entender seu propósito e como interagir com eles.

Ao longo deste laboratório, você interagirá com uma instalação _standalone_ do MongoDB. Em ambientes de produção, você provavelmente ira intergir com um _deploy_ mais completo.

### 1.2 Componentes  
Os componentes principais do MongoDB são:

- [**mongosh**]("https://www.mongodb.com/pt-br/docs/mongodb-shell/"): O próprio shell do MongoDB, uma interface interativa em JavaScript e Node.js que você pode usar para se conectar e executar comandos.
- **mongod**: O processo principal (daemon), o processo central do sistema MongoDB.
- **mongos**: O processo que direciona consultas e operações de escrita em um cluster fragmentado.
  
Há também as **Ferramentas de Banco de Dados do MongoDB**, um conjunto de ferramentas para tarefas administrativas, como dump/restore e import/export (dump/restauração e importação/exportação).

### 1.3 Interação dos Componentes
Você precisa de uma ferramenta para acessar uma instância do **mongod**. Você pode usar o shell do MongoDB ou uma ferramenta de terceiros.

O fluxo de interação é o seguinte:

**mongo → conecta-se a → mongod**

Como o **mongod** é o processo do banco de dados, você pode verificar seu status executando o seguinte comando:

`systemctl status mongod`

### 1.4 Topologia

Uma pergunta que você pode ter neste momento é:

> O que posso fazer com esses componentes?

O MongoDB oferece uma implementação diversificada para replicação e disponibilidade de dados, e esses são os componentes necessários para utilizar esses recursos.

As implementações possíveis incluem o seguinte:

**_Standalone_ (Instância Independente):** A instalação básica, um único nó mongod.

**_Replica set_ (Conjunto de Réplicas):** Replicação padrão, várias cópias dos dados em servidores de banco de dados diferentes, oferecendo um nível de tolerância a falhas contra a perda de um único servidor de banco de dados.

![](./resources/images/replica-set-read-write-operations-primary.png)

O _primário_ e os _secundários_ são processos `mongod` que rodam em diferentes locais. Você pode usar o shell do MongoDB (`mongo`) para interagir com eles.

> A configuração mínima recomendada para um conjunto de réplicas é de três membros — um primário e dois secundários.

**_Sharded cluster_ (Cluster Fragmentado):** Esse recurso de cluster do MongoDB permite distribuir dados em várias máquinas (escala horizontal), para conjuntos de dados muito grandes e operações de alto desempenho.

![](./resources/images/sharded-cluster-production-architecture.png)

Em vez de se conectar ao `mongod`, a aplicação deve se conectar a um roteador (`mongos`).

Para tarefas administrativas, você pode usar o shell do MongoDB para se conectar tanto ao `mongos` quanto ao `mongod`.

### 1.5 Conectando-se a uma Instância Local 
Agora, vamos usar o seu entendimento básico dos componentes do MongoDB para se conectar a uma instância.

#### Shell do MongoDB

Primeiro, vamos tentar conectar usando o shell do MongoDB (**mongo**) sem nome de usuário e senha.

Você pode executar o seguinte comando:

```
mongo
```

Para sair e fechar a conexão com a instância, execute o seguinte:

```
exit
```

> :warning: Antes de seguir em frente, é importante destacar que essa instância não tem controle de acesso habilitado, o que permite que você se conecte livremente sem nome de usuário e senha. Do ponto de vista da segurança, essa não é uma configuração recomendada para ambientes de produção.

**Construindo sua String de Conexão**  
No mundo real qualquer base de dados deve ter algum tipo de controle de acesso habilitado. Para se conectar nessas circunstâncias, você deve usar um nome de usuário e senha válidos. Existem dois métodos para construir sua string de conexão:

1. **Usando o formato URI (_Uniform Resource Identifier_)**: Para uma instância independente com controle de acesso habilitado. Um exemplo de string de conexão usando o formato URI é o seguinte:

   ```
   mongo mongodb://dba_adm:sekr3t@mongodb0.example.com:27017/?authSource=admin
   ```

   Onde:

   - **dba_adm** é o usuário
   - **sekr3t** é a senha
   - **mongodb0.example.com** é o host
   - **27017** é a porta
   - **authSource** é o banco de dados com as credenciais do usuário

   A porta padrão para o **mongod** é 27017.

2. **Passando opções diretamente no comando do shell do MongoDB**:

   ```
   mongo --host=mongodb0.example.com \
   --port=27017 \
   --username=dba_adm \
   --password=sekr3t \
   --authenticationDatabase=admin
   ```

   Embora ambos os métodos tenham o mesmo propósito, o método URI é amplamente utilizado em drivers de aplicação, e o segundo método funcionará apenas no shell do MongoDB.

**Testando a Conexão**  
Embora o controle de acesso não esteja habilitado, você pode se conectar usando o seguinte usuário:

- **username**: read_user  
- **password**: gswmongo  
- **authSource**: playground  

Você pode substituir essas informações de usuário na string de conexão da seguinte maneira:

```
mongo mongodb://db_admin:'gswmongo'@localhost:27017/?authSource=playground
```

ou:

```
mongo --host=localhost \
--port=27017 \
--username=db_admin \
--password='gswmongo' \
--authenticationDatabase=playground
```

Ótimo! Você usou o shell do MongoDB para se conectar a uma instância MongoDB.

Uma vez conectado, você pode navegar e exibir as estruturas do banco de dados.