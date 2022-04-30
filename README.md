# Trabalho Individual  2021.2

Os conhecimentos de *Gestão de Configuração de Software* são fundamentais no ciclo de vida de um produto de software. As técnicas para a gestão vão desde o controle de versão, automação de build e de configuração de ambiente, testes automatizados, isolamento do ambiente até o deploy do sistema. Todo este ciclo nos dias de hoje são integrados em um pipeline de DevOps com as etapas de Integração Contínua (CI) e Deploy Contínuo (CD) implementadas e automatizada.

Para exercitar estes conhecimentos, neste trabalho, você deverá aplicar os conceitos estudados ao longo da disciplina no produto de software contido neste repositório.

O sistema se trata de uma aplicação em Ruby on Rails composta por:

- Aplicação de Gerência de Biblioteca;
- Banco de Dados Postgres;

Para executar a aplicação em sua máquina, basta seguir o passo-a-passo descrito no arquivo [README](./aplicacao/README.md) da pasta.

## Resumo da aplicação

A aplicação é um simples sistema de cadastro de livros. Porém, o foco do trabalho é na automação da build, testes, conteinerização e configuração dos pipelines de CI/CD. Está escrita em Ruby on Rails, utiliza o banco de dados Postgres e inclui testes.

## Etapas de trabalho

O trabalho deve ser elaborado através de etapas. Cada uma das etapas deve ser realizada em um commit separado com o resultado funcional desta etapa.

As etapas de 1 a 3 são relacionadas ao isolamento do ambiente utilizando a ferramenta Docker e Docker Compose. Neste sentido o tutorial abaixo cobre os conceitos fundamentais para o uso destas tecnologias.

[Tutorial de Docker](https://github.com/FGA-GCES/Workshop-Docker-Entrega-01/tree/main/tutorial_docker)

As etapas de 4 e 5 são relacionadas à configuração do pipeline de CI e CD.

[Tutorial CI - Gitlab](https://github.com/FGA-GCES/Workshop-CI-Entrega-02/tree/main/gitlab-ci_tutorial)

### 1. Containerização do Banco

A versão inicial do sistema é uma aplicação Ruby on Rails cujo funcionamento requer uma instalação de um banco de dados Postgres. A primeira etapa do trabalho é de configurar um container somente para o banco de dados com as credenciais especificadas na descrição da aplicação e testar o funcionamento do mesmo.

> Nesta estapa, o banco de dados foi containerizado a partir de um arquivo ```Dockerfile```, de forma bem simples. Para a sua utilização é necessária a realização do build para que a imagem possa ser gerada. Esse passo é realizado através do comando (dentro da pasta [aplicacao](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/tree/main/aplicacao)):  
```sudo docker build -t postgres-db ./```  
A criação do container e a sua execução são realizadas com o comando (também na pasta [aplicacao](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/tree/main/aplicacao)):  
```sudo docker run --name postgresdb-container -p 5432:5432 postgres-db```  
Após a realização destes dois passos, a aplicação pode ser [configurada e executada normalmente](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/tree/main/aplicacao/README.md). 

> **Commits**:  
> [atualizando gitignore](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/7ef9ce0d8d99d18ea839d18603823324a622f57e)  
> [criando Dockerfile do banco de dados](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/aaec3ffed4b7b002f8c96ae5087fb567cb64c293)  
> **[1. Containerização do Banco](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/c429e1b061252a1aa24ffbb3d9f4c5408b23b5b3)**  

### 2. Containerização da Aplicação + Banco

Nesta segunda etapa, tanto a aplicação quanto o banco de dados deverão estar funcionando em containers individuais.

Deverá ser utilizado um orquestrador (Docker Compose) para gerenciar comunicação entre os containers além do uso de credenciais, networks, volumes, entre outras configurações necessárias para a correta execução da aplicação.

> Nesta etapa tanto o banco de dados quanto a aplicação foram conteinerizados. Para o gerenciamento entre os containers foi criado um arquivo docker-compose. O Dockerfile do banco de dados, por se tratar de um serviço simples, foi removido. Assim, o container do banco se baseia em uma imagem já pronta do Postgre. Para o Ruby, por ser um serviço mais complexo, foi criado um Dockerfile.   
> Os servicos foram nomeados como app (Ruby) e db (Postgre).  
> Tanto o Dockerfile quando o docker-compose ainda estão localizados na raíz da [aplicacao](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/tree/main/aplicacao).  
> 
> Para a execução do container, utiliza-se o comando:  
> ```sudo docker-compose up --build```  
> na pasta [aplicacao](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/tree/main/aplicacao). Após a inicialização dos serviços, é necessário configurar o banco de dados com o comando:  
> ```sudo docker exec -it aplicacao_app_1 rails db:setup```

> **Commits**:  
> [removendo postgree e criando ruby Dockerfile](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/bd3c2bb90ae6a74fcd4dafc387df9407f1d3ff54)  
> [criando docker-compose](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/b49f92f9318b94da36557c110d4cb689e8464015)  
> [configurando host da BD na aplicacao](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/e0fe432aaa307943fe09539aaffa21d157f71632)  
> [corrigindo senha do banco de dados](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/11cacde7750f79d60a2f233a0a344972dfa6bab0)  
> [renomeando servico da aplicacao](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/0c4148bffa86e5944f6ab82b0b3e67ccf2c86af0)  
> **[2. Containerização da Aplicação + Banco](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/254d6762733a990272c8694590daab5b38e51bda)**  

### 3. Adição de um container do Nginx 

A aplicação originalmente está configurada para rodar com um servidor web simples interno na porta 3000. Nesta etapa será necessário adicionar o servidor Nginx para servir a aplicação através da porta 80. O resultado final também estará expresso utilizando o Docker Compose.  

> Nesta etapa foi criado um [diretório](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/tree/main/aplicacao/docker) para armazenar os Dockerfiles tanto do container app (Ruby) quanto do web (nginx). Além disso, foi criado também um arquivo [.dockerignore](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/tree/main/aplicacao/.dockerignore), para que arquivos desnecessários não sejam copiados para o container da aplicação.   
> Assim como na etapa anterior, para o build e execução dos containers, é utilizado o comando:  
>  ```sudo docker-compose up --build```    
> Entretanto, como foi criado um servidor nginx para a aplicação, basta digitar ```localhost``` no seu navegador para ter acesso à Biblioteca Pessoal.  
> O resultado desta etapa é uma aplicação completamente containerizada, mais organizada que as anteriores (possuindo um diretório direcionado para o docker e um arquivo .dockerignore) e pronta para seguir para a integração contínua.  

> **Commits**:  
> [criando diretório de docker](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/49200324b003b31f1b50ff1a162978ff179ea07b)  
> [tornando o dockerfile da aplicação mais robusto](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/c21c8e8586a32fee1cb38478fbe52d27f70185ad)  
> [criando dockerignore](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/0983411f684660a227d01bc972fabc1afc8301c8)  
> [criando Dockerfile do nginx](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/7661fe10f2408ab739daa80cbfd538af12c1eef7)  
> [adicionando arquivo de configuração do nginx](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/358c44abaabe3c22cbbfea0ce7974dccc0961dd5)  
> [alterando o docker compose](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/5072cee69736e679db47be41409af9e26acab1be)  
> **[3. Adição de um container do Nginx](https://github.com/giovanadionisio/Trabalho-Individual-2021-2/commit/3dd70303c4d5bf58aa7d535ca3e37f9af873bd6c)**  

### 4. Integração Contínua (CI)

Para a realização desta etapa, a aplicação já deverá ter seu ambiente completamente containerizado.

Deverá ser utilizada uma ferramenta de Integração Contínua para garantir o build, os testes e os deploy para o [Docker Hub](https://hub.docker.com) dos serviços principais.

Esta etapa do trabalho poderá ser realizada utilizado os ambientes de CI do [GitLab-CI](https://docs.gitlab.com/ee/ci/). ou [Github Actions](https://github.com/features/actions).  

Requisitos da configuração da Integração Contínua (Gitlab ou Github) incluem:
- Build
- Test
- Lint

### 5. Deploy Contínuo (CD)

A etapa final do trabalho deverá ser realizada à partir do deploy automático da aplicação que deve ser realizado à partir após passar sem erros em todas as etapas de CI.

## Avaliação

A avaliação do trabalho será feita à partir da correta implementação de cada etapa 1 a 5. A avaliação será feita de maneira quantitativa (se foi realizado a implementação + documentação), e qualitativa (como foi implementado, entendimento dos conceitos na prática, complexidade da solução). Para isso, faça os commits atômicos, bem documentados, completos a fim de facilitar o entendimento e avaliação do seu trabalho. Lembrando o trabalho é individual.

| Item | Peso |
|---|---|
| Containerização do Banco                      | 1.0 |
| Containerização da Aplicação + Banco          | 2.0 |
| Containerização da Aplicação + Banco + Nginx  | 2.0 |
| Integração Contínua (Build, Test, Lint)       | 3.0 |
| Deploy Contínuo                               | 2.0 |


##  Exemplo de Trabalhos Anteriores

Alguns trabalhos de trabalhos anteriores:

- [2020/2](https://github.com/FGA-GCES/Trabalho-Individual-2020-2)
- [2021/1 - Parte 1](https://github.com/FGA-GCES/Workshop-Docker-Entrega-01)
- [2021/1 - Parte 2](https://github.com/FGA-GCES/Workshop-Docker-Entrega-02)
