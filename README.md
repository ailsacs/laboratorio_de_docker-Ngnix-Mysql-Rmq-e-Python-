## Estrutura Geral

Este cenário envolve a configuração de um ambiente Docker composto por vários contêineres para criar um sistema de comunicação assíncrona e processamento de mensagens.

### 1. Configuração Inicial em Docker

Nesta etapa, estamos configurando a base do projeto. Primeiro, é necessário garantir que o Docker e o Docker Compose estejam instalados em sua máquina. Essa configuração é fundamental para criar e executar nossas aplicações.

- Certifique-se de que o Docker e o Docker Compose estejam instalados em sua máquina.
- Utilizamos o Docker Compose, uma ferramenta para definir e executar aplicativos multicontêineres, para criar e gerenciar esses containers.
- O comando `docker-compose up -d` é usado para iniciar os containers em segundo plano.
- Verifique se os containers estão em execução com o comando `docker container ls`.

### 2. Integrações

Na segunda etapa, introduzimos o RabbitMQ, um sistema de mensagens assíncronas que facilita a comunicação entre diferentes partes do sistema. Esta etapa envolve diversos containers para configurar essa integração.

- **Container Nginx (lab-web):**
  - Recebe uma página web que cria mensagens e as envia para o container RabbitMQ.
  - Verifica a quantidade de mensagens na fila RabbitMQ.
  - Processa a fila, o que inclui a ação de salvar as mensagens no container de banco de dados e enviar uma confirmação ("acknowledge") para a fila RabbitMQ.

- **Container RabbitMQ (lab-rabbitmq):**
  - Hospeda o serviço RabbitMQ, que é responsável pelo gerenciamento de filas de mensagens.

- **Container Intermediário (lab-backend-intermediario):**
  - Implementa funções em Python para interagir com o RabbitMQ e executar ações como adicionar mensagens à fila e processar mensagens.
  - Recebe comandos da página web do container Nginx para adicionar mensagens à fila RabbitMQ.
  - Responde às solicitações do Nginx para obter a quantidade de mensagens na fila e processar a fila.
  - Salva os dados processados no banco de dados configurado na etapa de configuração inicial.
  - Envia uma confirmação ("acknowledge") para o RabbitMQ após o processamento bem-sucedido.

### Estrutura do Docker Compose

O arquivo `docker-compose.yml` define e orquestra os serviços Docker necessários para o projeto. Aqui está uma visão geral dos serviços e como eles se relacionam:

- `lab-rabbitmq` (RabbitMQ):
  - Fornece a funcionalidade de fila de mensagens RabbitMQ.

- `lab-web` (Nginx):
  - Recebe uma página web que cria e envia mensagens para o RabbitMQ.
  - Verifica a quantidade de mensagens na fila e processa a fila.
  - Encaminha solicitações para o container intermediário.

- `lab-backend-intermediario` (Contêiner Intermediário):
  - Implementa funções em Python para interagir com o RabbitMQ e executar ações específicas.
  - Recebe comandos da página web para adicionar mensagens à fila RabbitMQ.
  - Responde às solicitações para obter a quantidade de mensagens na fila e processar a fila.
  - Salva dados processados no banco de dados configurado na etapa de configuração inicial.
  - Envia uma confirmação ("acknowledge") para o RabbitMQ após o processamento bem-sucedido.

- Outros serviços, como o banco de dados MySQL e a interface de administração, também estão presentes para suportar as operações do projeto.

**Observação:** Lembre-se de que as configurações específicas, como nomes de usuários, senhas e outras variáveis de ambiente, devem ser definidas no arquivo `.env` antes de iniciar o projeto. Certifique-se de fazer essas alterações para corresponder às configurações desejadas.
