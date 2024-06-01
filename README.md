# Gerenciamento de Dependências e Build em Java com Maven

- Maven: possui diversas farrementas que fazem testes de forma automatizada, assim como compilar todas as classes Java
que existem na nossa aplicação.


- POM: Project Object Model.

Instalação do Mavem:

- fazer o download em: https://maven.apache.org/download.cgi => apache-maven-3.9.7-bin.zip

- Extrair arquivos.

- coloracar o caminho até o execultável do maven(mvn) nas variáveis de ambiente:
ex: D:\Projetos\apache-maven-3.9.7\bin => que é onde esta o execultável do Maven(arquivo mvn.cmd)
*pesquise no windows "variáveis de ambiente", cliquem variáveis de ambiente, variáveis de sistema, na lista abra
Path, clica em novo e coloca o caminho bin do maven.

* Para verificar se a instalação esta correta, no cmd, digite: mvn --version



# Comandos cmd do Maven

* criar um projeto pelo cmd:
mvn archetype:generate ``-DgroupId=br.com.nomeGrupo -DartifactId=nomeDoProjeto -Darchetype=maven-archetype-quickstart -Dversion=1.4 -DinteractiveMode=false``


* compilar: ``mvn compile``

* Testar: ``mvn test``

* Empacotar: ``mvn package`` => cria o .jar

* Limpar diretório de trabalho: ``mvn clean`` => limpa a pasta target



* Maven archetype = nada mais do que um template que possibilita a personalização e a configuração de como o projeto vai ser
construído. Definimos: versão de componentes, quais componentes serão inseridos automaticamente, organização de pacotes e
organização de arquivos.


* Maven archetype list= o template/archetype que faz mais sentido para o meu projeto.
lista de archetype disponível em: https://maven.apache.org/archetypes/index.html => clicando no archetype mostra o comando mvn de
para sua criação.
Há archetypes disponíveis tambem no git hub, basta clonar o projeto de lá.
ex: https://github.com/openjfx/javafx-maven-archetypes // https://gist.github.com/zbigniewTomczak/4235871
// https://mvnrepository.com/search?q=archetype&sort=popular



# POM

* Trabalha com conceitos de herança, atributos que estiverem faltando no seu pom, o maven vai buscar do
  pom pai, o super pom.

Repository

* Locais que podemos encontrar plugins e bibliotecas que o Maven proê. pode ser local ou remoto. o remoto
  busca os artefact/puglins no Maven central(site com todas as dependências) configurado automaticamente
  pelo super pom. O repositório local, nada mais é, o diretório onde são baixadas as dependências do
  projeto, fazendo que não seja necessário baixar novamente, aumentando a performace.

Adicionando dependências ao Projeto

### No pom, dentro da tag <dependencies>, adicione a dependência desejada e salve as alterações do arquivo:

  ``<dependency>
  <groupId>org.hibernate</groupId>
  <artifactId>hibernate-search-orm</artifactId>
  <version>5.11.9.final</version>
  </dependency>``

- em seguide no cmd execulte o comando do Maven para compilar e baixar a nova dependência: mvn compile

# Gerenciamento de dependências

* Tipos de dependências:
-Transformar um projeto seu num componente para ser usado em outro projeto e para se publicado no site no Maven,
escute o comando na raiz do seu projeto: mvn install => dessa fora seu projeto será transformado numa dependência
local para ser usada em um projeto ou ser enviado para o maven. Para usar o componente em um outro projeto como
dependência no pom do componente copie o groupId, artifactId e version e coloque dentro de uma tag dependency e
adicione no seu pomo como de costume.

``<dependency>
    <artifactId>spring-boot-starter-parent</artifactId>
    <groupId>org.springframework.boot</groupId>
    <version>3.2.0</version>
</dependency>``

* Existem dois tipos de dependências no Maven:
  - Diretas= dependências declaradas no pom.xml
  - Transitivas= dependências obrigratórias necessárias para uma componente/dependência funcionar no seu projeto.

  ### Transitividade e escopo

- Quando for usar um dependência/componente no seu projeto e se não quiser que dependências desse componente venha junto
para o seu projeto o Maven lida com isso usando escopos para limitar essa transitividade de depências obrigatórias do
do componente que você quer usar.
- Classpath= referência que a aplicação tem para para aquele momento de execulção, o Maven divide esse processo em
runtime, test e compile.

- Você pode definir o espopo da depêndia atravez da tag <scope> logo abaixo da dependência no pom:

``<dependency>
   <groupId>org.modelmapper</groupId>
   <artifactId>modelmapper</artifactId>
   <version>3.1.1</version>
   <scope>compile</scope>
</dependency>``

- Tipos de escopo
  - ``<scope>compile</scope>`` = escopo padrâo, ou seja todas as dependências do componente irão junto, nesta caso é opcional
colocar esse escopo na depência, por ser já o padrão.

  - ``<scope>provided</scope>`` =  indica que a dependência será fornecida em tempo de execução por uma implementação na JDK
ou via container, não é transitiva,vai no escopo de runtime e test.

  - ``<scope>runtime</scope>`` = indica que a dependência é necessária para a execução e não para a compilação, vai no escopo
de runtime e test.

  - ``<scope>test</scope>`` = disponível somente para compilação e execução de testes, não é transitivo.

  - ``<scope>system</scope>`` = similar ao provided exceto por ser necessário prove o JAR explicitamente(você tem que informar
a localização do Jar). Vai no escopo de de compile e teste, não é transitiva.
ex:
  - ``<scope>system</scope>``
<systemPath>${project.basedir}/libs/custom-dependency-1.3.2.jar</systemPath> <--- caminho até o JAR

  - ``<scope>import</scope>`` = disponível apenas com uma dependência do tipo pom e com tag <dependecyManagement>, indica um
processo de reutilizar dependência de um projeto, ou seja, você pode importar dependências de outros projetos.

# Dicas sobre escopos, dependências opcionais e exclusions
- Olhar como o Maven está construindo o classpath do meu projeto atravez dos comandos abaixo no cmd:

``mvn dependency:build-classpath -DincludeScope=compile``  

``mvn dependency:build-classpath -DincludeScope=test``

``mvn dependency:build-classpath -DincludeScope=runtime``

- Usando a tag ``<optional>true</optional>`` vai para o projeto ou não(true ou false).

- Usando a tag <``exclusions>`` , quando não quer uma biblioteca de uma depedência ou já possui no seu projeto.


# Maven Build Lifecycle

- Cliclos de vida:

  - default = principal ciclo, responsável pelo deploy local, possui 23 fases, ex: validate, compile, test-compile, test,
integration-test, package, install, deploy(todos são comandos mvn).

  - clean = ciclo intermediário, faz a limpeza do projeto, composto por 3 fase: pre-clean, clean, post-clean(comandos mvn)

  - site = ciclo final, criação do site de documentação do projeto, possue 4 fase: pre-site, site, post-site, site-deploy
(todos são comandos mvn).


# Projetos Multi-módulos

* Crie um projeto como de costume:
``mvn archetype:generate -DgroupId=br.com.jhonnyazevedo -DartifactId=multi-modules-project -Darchetype=maven-archetype-quickstart -Dversion=1.4 -DinteractiveMode=false``

* Abra o projeto e artere a tag packaging para pom pois vai ser um projeto que agrega os demais: <packaging>pom</packaging>

* Dentro da pasta do projeto, abra os terminal e crie os modules necessários dentro da pasta do projeto:
ex:

``mvn archetype:generate -DgroupId=br.com.jhonnyazevedo -DartifactId=core -Darchetype=maven-archetype-quickstart -Dversion=1.4 -DinteractiveMode=false``

``mvn archetype:generate -DgroupId=br.com.jhonnyazevedo -DartifactId=service -Darchetype=maven-archetype-quickstart -Dversion=1.4 -DinteractiveMode=false``

``mvn archetype:generate -DgroupId=br.com.jhonnyazevedo -DartifactId=controller -Darchetype=maven-archetype-quickstart -Dversion=1.4 -DinteractiveMode=false``

* se você olhar o pom do projeto princiapl(<parente>) tem uma tag <modules> dentro dela o maven já adiciona automaticamente os sub módules que você criou.


# Plugins

* A maioria das funcionalidades são providas por plugins(clean,compile, etc).

* Você pode criar seu próprio plugin(extensibilidade).

* Escrito prioritariamente e Java e disponibilizado como JARs.

* Alguns plugins mais usados:
- Eclipse, jacoco, ear, war, compile, clean, checkstyle, javadoc.

*Uso: ``mvn [plugin-name]:[goal-name]``

ex: ``mvn dependency:help``


# Configurando um plugin

* Dentro do pom dentro da tag ``<plugins>`` adicione o plugin como uma depência normal que dentro
da tag específica:
ex:

``<plugins>
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.13.0</version>
    <configuration> 
        <source>1.8</source>
        <target>1.8</target>
    </configuration>
</plugin>
</plugins>``

# Gerando um javadoc no projeto

- adicione o plugin no pom:

``<plugins>
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-javadoc-plugin</artifactId>
    <version>3.3.1</version>
  </plugin>
</plugins>``

- execute o plugin na linha de comando: ``mvn javadoc:javadoc``

- repositório de plugin do maven: https://maven.apache.org/plugins/


## Outros comando mvn:

* ver dependências do projeto: ``mvn dependency:analyze``









