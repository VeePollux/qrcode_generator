# qrcode_generator

![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2.0-green)
![Java](https://img.shields.io/badge/Java-21-blue)
![Maven](https://img.shields.io/badge/Maven-3.x-red)
![AWS S3](https://img.shields.io/badge/AWS%20S3-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)

Este projeto é uma aplicação **Spring Boot** que oferece uma **API RESTful** para gerar códigos QR a partir de um texto fornecido. A imagem PNG do QR Code gerado é automaticamente **carregada para um bucket no Amazon S3**, e a API retorna a URL pública para acesso ao QR Code.

---

## ✨ Funcionalidades

* Recebe um **texto** via requisição API.
* **Gera um QR Code** correspondente ao texto.
* **Converte** o QR Code em uma imagem **PNG**.
* Realiza o **upload** da imagem PNG para um bucket S3 configurado na AWS.
* Retorna a **URL pública** do QR Code armazenado no S3.

---

## 🛠️ Tecnologias Utilizadas

O projeto foi construído com as seguintes tecnologias:

* **Spring Boot 3.x:** Framework base para a construção da API.
* **Java 21:** Linguagem de programação utilizada.
* **Apache Maven:** Gerenciador de dependências e ferramenta de build.
* **ZXing (Zebra Crossing):** Biblioteca da Google para geração de códigos de barras (incluindo QR Code).
* **AWS SDK for Java 2.x:** Kit de desenvolvimento para interagir com os serviços da Amazon Web Services (especificamente S3).
* **Docker:** Para conteinerização e facilitar a implantação em diferentes ambientes.

---

## ⚙️ Pré-requisitos e Configuração

Para configurar e rodar este projeto, você precisará dos seguintes pré-requisitos:

* **Java Development Kit (JDK) 21**
* **Apache Maven 3.x**
* **Docker** (opcional, para execução via container)
* Uma **Conta AWS** com credenciais configuradas e um **bucket S3** existente.

### Configuração do AWS S3

1.  **Crie um Bucket S3:**
    * Acesse o console da AWS e navegue até o serviço **S3**.
    * **Crie um novo bucket**.
    * **Anote o nome do bucket** (ex: `storager-qrcodee`) e a **região** onde ele foi criado (ex: `us-east-1`).
    * Garanta que as permissões do seu bucket ou da política de IAM do usuário/perfil que sua aplicação usará permitam a ação `s3:PutObject` (para que a aplicação possa fazer upload de arquivos).

2.  **Credenciais AWS:**
    O projeto espera que suas credenciais AWS (**AWS Access Key ID** e **AWS Secret Access Key**) sejam configuradas no ambiente de execução. As formas recomendadas são:
    * Variáveis de ambiente (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`).
    * Arquivo `~/.aws/credentials`.
    * Perfis IAM em uma instância EC2 (se estiver implantando na AWS).

    As configurações de **região e nome do bucket** são lidas do arquivo `src/main/resources/application.properties`, que usa variáveis de ambiente:

    ```properties
    spring.application.name=qrcode.generator
    aws.s3.region=${AWS_REGION}
    aws.s3.bucket-name=${AWS_BUCKET_NAME}
    ```

---

## 🚀 Como Rodar o Projeto

Você tem duas opções para executar a aplicação: diretamente via Maven ou utilizando Docker.

### 1. Rodando com Maven (Localmente)

1.  **Clone o repositório:**
    ```bash
    git clone [https://github.com/VeePollux/qrcode-generator.git](https://github.com/VeePollux/qrcode-generator.git)
    cd qrcode-generator
    ```
2.  **Defina as variáveis de ambiente:**
    Antes de rodar, exporte as variáveis para a região e o nome do seu bucket S3:

    * **Linux/macOS:**
        ```bash
        export AWS_REGION="sua-regiao-aws" # Ex: us-east-1
        export AWS_BUCKET_NAME="seu-nome-de-bucket-s3" # Ex: storager-qrcodee
        ```
    * **Windows (CMD):**
        ```cmd
        set AWS_REGION="sua-regiao-aws"
        set AWS_BUCKET_NAME="seu-nome-de-bucket-s3"
        ```
    * **Windows (PowerShell):**
        ```powershell
        $env:AWS_REGION="sua-regiao-aws"
        $env:AWS_BUCKET_NAME="seu-nome-de-bucket-s3"
        ```
3.  **Compile e execute a aplicação:**
    ```bash
    mvn spring-boot:run
    ```
    A aplicação estará acessível em `http://localhost:8080`.

### 2. Rodando com Docker

1.  **Construa a imagem Docker:**
    ```bash
    docker build -t qrcode-generator .
    ```
2.  **Execute o container Docker:**
    É crucial passar suas chaves de acesso AWS, a região e o nome do bucket como variáveis de ambiente ao executar o container:
    ```bash
    docker run -p 8080:8080 \
        -e AWS_ACCESS_KEY_ID="SUA_AWS_ACCESS_KEY_ID" \
        -e AWS_SECRET_ACCESS_KEY="SUA_AWS_SECRET_ACCESS_KEY" \
        -e AWS_REGION="sua-regiao-aws" \
        -e AWS_BUCKET_NAME="seu-nome-de-bucket-s3" \
        qrcode-generator
    ```
    **Lembre-se de substituir** `SUA_AWS_ACCESS_KEY_ID`, `SUA_AWS_SECRET_ACCESS_KEY`, `sua-regiao-aws` e `seu-nome-de-bucket-s3` pelos seus valores reais.

    A aplicação estará acessível em `http://localhost:8080`.

---

## 🖥️ Uso da API

A API expõe um único endpoint `POST` para a geração de QR Codes.

### `POST /qrcode`

Gera um QR Code para o texto fornecido e faz o upload para o S3.

* **URL:** `http://localhost:8080/qrcode`
* **Método:** `POST`
* **Content-Type:** `application/json`

#### **Exemplo de Request Body:**

```json
{
  "text": "Seu texto para o QR Code aqui!"
}
