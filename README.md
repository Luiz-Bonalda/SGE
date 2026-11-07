
# Sistema de Gestão de Estoque (SGE)

Bem-vindo ao **Sistema de Gestão de Estoque (SGE)**, um projeto desenvolvido com Django e Bootstrap 5 para facilitar o gerenciamento e controle de estoque.

O sistema permite gerenciar movimentações de estoque e automatizar notificações por meio da integração com uma segunda aplicação, responsável pelo envio de mensagens via e-mail e WhatsApp.

Este README apresenta as principais informações sobre o projeto, tecnologias utilizadas e instruções para configuração e execução das aplicações.

## Sumário

- [Tecnologias utilizadas](#tecnologias-utilizadas)
- [Requisitos](#requisitos)
- [Instalação e configuração local](#instalação-e-configuração-local)
  - [1. Clonar o repositório](#1-clonar-o-repositório)
  - [2. Criar e ativar o ambiente virtual](#2-criar-e-ativar-o-ambiente-virtual)
  - [3. Instalar as dependências](#3-instalar-as-dependências)
  - [4. Configurar as variáveis de ambiente](#4-configurar-as-variáveis-de-ambiente)
  - [5. Aplicar as migrations](#5-aplicar-as-migrations)
  - [6. Executar o projeto](#6-executar-o-projeto)
- [Execução com Docker](#execução-com-docker)
  - [Arquivos Docker](#arquivos-docker)
  - [Arquitetura dos containers](#arquitetura-dos-containers)
  - [Comandos Docker](#comandos-docker)
  - [Persistência de dados](#persistência-de-dados)
- [Integração com o Notify](#integração-com-o-notify)
  - [Funcionamento da integração](#funcionamento-da-integração)
  - [Configuração e execução da integração](#configuração-e-execução-da-integração)
- [Variáveis de ambiente e segurança](#variáveis-de-ambiente-e-segurança)
- [Autor](#autor)

## Tecnologias utilizadas

- Python 3.14
- Django
- Bootstrap 5
- PostgreSQL 15
- Docker
- Docker Compose
- HTML e CSS

## Requisitos

Para executar o projeto, você precisará ter:

- Python 3.14 ou superior.
- Git instalado.
- Docker e Docker Compose (para execução utilizando containers).
- Dependências listadas no arquivo `requirements.txt`.

## Instalação e configuração local

### 1. Clonar o repositório

```bash
git clone URL_DO_REPOSITORIO
```

Acesse o diretório do projeto:

```bash
cd SGE
```

### 2. Criar e ativar o ambiente virtual

```bash
python -m venv venv
```

No Windows (PowerShell):

```powershell
.\venv\Scripts\Activate.ps1
```

### 3. Instalar as dependências

```bash
pip install -r requirements.txt
```

### 4. Configurar as variáveis de ambiente

Crie um arquivo `.env` na raiz do projeto e configure as variáveis necessárias para a conexão com o banco de dados e demais funcionalidades da aplicação.

### 5. Aplicar as migrations

```bash
python manage.py migrate
```

### 6. Executar o projeto

```bash
python manage.py runserver
```

O sistema estará disponível em:

http://localhost:8000

## Execução com Docker

O projeto possui suporte ao Docker e Docker Compose, permitindo executar a aplicação Django e o banco de dados PostgreSQL em containers separados.

### Arquivos Docker

- **Dockerfile:** define a imagem da aplicação, utilizando Python 3.14 Slim, instalando as dependências e configurando a inicialização do Django.
- **docker-compose.yml:** responsável por orquestrar os serviços da aplicação e do banco de dados PostgreSQL.

### Arquitetura dos containers

A infraestrutura é composta por dois serviços:

- **sge_web:** container responsável pela execução da aplicação Django, disponibilizada na porta 8000.
- **sge_db:** container responsável pelo banco de dados PostgreSQL 15, com persistência dos dados através de um volume Docker.

### Comandos Docker

**Construir as imagens e iniciar os containers:**

```bash
docker compose up --build
```

**Executar os containers em segundo plano:**

```bash
docker compose up -d --build
```

**Verificar os containers em execução:**

```bash
docker compose ps
```

**Visualizar os logs da aplicação:**

```bash
docker compose logs -f sge_web
```

**Parar os containers:**

```bash
docker compose down
```

### Persistência de dados

O PostgreSQL utiliza um volume Docker chamado `postgres_data`, responsável por manter os dados do banco mesmo após a parada ou remoção dos containers.

Para remover também o volume e os dados persistidos, utilize:

```bash
docker compose down -v
```

**Atenção:** esse comando remove os volumes associados ao Compose e, consequentemente, os dados armazenados no banco de dados.

## Integração com o Notify

O Sistema de Gestão de Estoque possui integração com uma segunda aplicação desenvolvida em Django, responsável pelo processamento de eventos e envio automatizado de notificações.

As aplicações funcionam de maneira independente e se comunicam por meio de uma integração entre sistemas.

### Funcionamento da integração

Sempre que uma saída de estoque é registrada no SGE, um evento é enviado para a aplicação de notificações, que processa as informações recebidas e realiza os seguintes envios:

- **E-mail:** notificação contendo informações sobre a movimentação de estoque.
- **WhatsApp:** mensagem automática informando sobre a saída registrada.

Essa integração permite automatizar o acompanhamento das movimentações e manter os responsáveis informados sobre as operações realizadas no estoque.

### Configuração e execução da integração

Para utilizar a integração, é necessário configurar e executar as duas aplicações separadamente.

**1. Sistema de Gestão de Estoque (SGE)**

Configure o arquivo `.env` com as informações necessárias e inicie a aplicação utilizando Docker Compose:

```bash
docker compose up --build
```

**2. Sistema de notificações**

Clone o repositório da aplicação responsável pelas notificações:

```bash
git clone https://github.com/Luiz-Bonalda/Notify
```

Acesse o diretório:

```bash
cd Notify
```

Crie e ative um ambiente virtual:

```bash
python -m venv venv
```

No Windows (PowerShell):

```powershell
.\venv\Scripts\Activate.ps1
```

Instale as dependências:

```bash
pip install -r requirements.txt
```

Configure o arquivo `.env` com as credenciais e informações necessárias para o envio de e-mails, integração com WhatsApp e demais configurações da aplicação.

Aplique as migrations:

```bash
python manage.py migrate
```

Inicie o servidor:

```bash
python manage.py runserver
```

**3. Comunicação entre as aplicações**

Certifique-se de que a URL configurada no SGE corresponde ao endereço em que a aplicação de notificações está sendo executada.

Como o SGE é executado em um container Docker e a aplicação de notificações pode ser executada diretamente na máquina hospedeira, é necessário utilizar um endereço acessível entre os dois ambientes.

Em ambientes Windows com Docker Desktop, o endereço `host.docker.internal` pode ser utilizado para permitir a comunicação entre o container e a máquina hospedeira.

Para que as notificações funcionem corretamente, ambas as aplicações precisam estar em execução e configuradas adequadamente.

## Variáveis de ambiente e segurança

O projeto utiliza arquivos `.env` para armazenar configurações e informações sensíveis, como credenciais de acesso ao banco de dados e serviços externos.

Por motivos de segurança:

- Não compartilhe arquivos `.env` publicamente.
- Não envie credenciais ou senhas para o GitHub.
- Utilize um arquivo `.env.example` para documentar as variáveis necessárias sem expor informações sensíveis.

## Autor

Desenvolvido como projeto de estudo e aplicação prática de desenvolvimento web, com foco em Django, integração entre sistemas, automação de processos e utilização de containers Docker.
