# Parking Control - Sistema de Gerenciamento de Vagas de Garagem

Um sistema RESTful desenvolvido com Spring Boot para gerenciar vagas de estacionamento em condomínios residenciais. A aplicação permite registrar, listar, atualizar e deletar vagas de garagem com informações de veículos e moradores.

##  Índice

- [Visão Geral](#visão-geral)
- [Tecnologias](#tecnologias)
- [Requisitos do Sistema](#requisitos-do-sistema)
- [Instalação e Configuração](#instalação-e-configuração)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [API Endpoints](#api-endpoints)
- [Exemplos de Requisições](#exemplos-de-requisições)
- [Configuração do Banco de Dados](#configuração-do-banco-de-dados)
- [Como Executar](#como-executar)
- [Docker](#docker)
- [Melhorias Futuras](#melhorias-futuras)

## Visão Geral

O **Parking Control** é uma API REST que gerencia vagas de estacionamento em condomínios. Sistema completo que permite:

- ✅ Registrar novas vagas com informações de veículos
- ✅ Listar todas as vagas cadastradas
- ✅ Buscar vaga por ID
- ✅ Atualizar informações de vagas
- ✅ Deletar registros de vagas
- ✅ Validações automáticas para evitar duplicações (placa de veículo, número da vaga, apartamento/bloco)

## 🛠 Tecnologias

| Tecnologia | Versão | Descrição |
|-----------|--------|-----------|
| Java | 21 | Linguagem de programação |
| Spring Boot | 4.0.2 | Framework web |
| Spring Data JPA | - | Persistência de dados |
| PostgreSQL | Latest | Banco de dados relacional |
| Jakarta Validation | - | Validação de dados |
| Maven | 3.6+ | Gerenciador de dependências |
| Docker & Docker Compose | Latest | Containerização |

##  Requisitos do Sistema

- **Java 21** ou superior
- **Maven 3.6** ou superior
- **PostgreSQL 13+** (pode ser executado via Docker)
- **Docker e Docker Compose** (opcional, para containerização)
- **Git** para controle de versão

## 🚀 Instalação e Configuração

### 1. Clonar o Repositório

```bash
git clone <seu-repositorio-url>
cd parking-spot-project
```

### 2. Configurar Variáveis de Ambiente

Edite o arquivo `src/main/resources/application.properties`:

```properties
spring.application.name=parking-control
spring.datasource.url=jdbc:postgresql://localhost:5432/parking_control_db
spring.datasource.username=postgres
spring.datasource.password=sua_senha_aqui
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
```

### 3. Instalar Dependências

```bash
./mvnw clean install
```

## 📁 Estrutura do Projeto

```
parking-spot-project/
├── src/
│   ├── main/
│   │   ├── java/com/api/parking_control/
│   │   │   ├── ParkingControlApplication.java      # Classe principal
│   │   │   ├── controllers/
│   │   │   │   └── ParkingSpotController.java      # Endpoints REST
│   │   │   ├── models/
│   │   │   │   └── ParkingSpotModel.java           # Entidade JPA
│   │   │   ├── dtos/
│   │   │   │   └── ParkingSpotDto.java             # DTO para requisições
│   │   │   ├── services/
│   │   │   │   └── ParkingSpotService.java         # Lógica de negócio
│   │   │   └── repositories/
│   │   │       └── ParkingSpotRepository.java      # Acesso a dados
│   │   └── resources/
│   │       └── application.properties               # Configurações
│   └── test/
│       └── java/com/api/parking_control/
│           └── ParkingControlApplicationTests.java # Testes
├── infra/
│   └── docker/
│       └── docker-compose.yml                      # Configuração Docker
├── pom.xml                                          # Configuração Maven
└── README.md                                        # Este arquivo
```

##  API Endpoints

### Base URL
```
http://localhost:8080
```

### 1. **GET** `/parking-spots`
Retorna todas as vagas cadastradas.

**Response (200 OK):**
```json
[
  {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "parkingSpotNumber": "A001",
    "licensePlateCar": "ABC1234",
    "brandCar": "Toyota",
    "modelCar": "Corolla",
    "colorCar": "White",
    "registrationDate": "2024-02-08T10:30:00",
    "responsibleName": "João Silva",
    "apartment": "101",
    "block": "A"
  }
]
```

---

### 2. **GET** `/parking-spots/{id}`
Retorna uma vaga específica pelo ID.

**Path Parameters:**
- `id` (UUID): ID da vaga

**Response (200 OK):**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "parkingSpotNumber": "A001",
  "licensePlateCar": "ABC1234",
  "brandCar": "Toyota",
  "modelCar": "Corolla",
  "colorCar": "White",
  "registrationDate": "2024-02-08T10:30:00",
  "responsibleName": "João Silva",
  "apartment": "101",
  "block": "A"
}
```

**Response (404 Not Found):** Vaga não encontrada

---

### 3. **POST** `/parking-spots`
Cria uma nova vaga de estacionamento.

**Request Body:**
```json
{
  "parkingSpotNumber": "A002",
  "licensePlateCar": "XYZ9876",
  "brandCar": "Honda",
  "modelCar": "Civic",
  "colorCar": "Black",
  "responsibleName": "Maria Santos",
  "apartment": "102",
  "block": "A"
}
```

**Response (201 Created):**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440001",
  "parkingSpotNumber": "A002",
  "licensePlateCar": "XYZ9876",
  "brandCar": "Honda",
  "modelCar": "Civic",
  "colorCar": "Black",
  "registrationDate": "2024-02-08T10:35:00",
  "responsibleName": "Maria Santos",
  "apartment": "102",
  "block": "A"
}
```

**Possíveis Erros:**
- `409 Conflict`: Placa de veículo já em uso
- `409 Conflict`: Número de vaga já em uso
- `409 Conflict`: Apartamento/bloco já tem uma vaga registrada
- `400 Bad Request`: Validação de dados falhou

---

### 4. **PUT** `/parking-spots/{id}`
Atualiza uma vaga existente.

**Path Parameters:**
- `id` (UUID): ID da vaga

**Request Body:**
```json
{
  "parkingSpotNumber": "A002",
  "licensePlateCar": "XYZ9876",
  "brandCar": "Honda",
  "modelCar": "Civic",
  "colorCar": "Blue",
  "responsibleName": "Maria Santos",
  "apartment": "102",
  "block": "A"
}
```

**Response (200 OK):** Vaga atualizada com sucesso

---

### 5. **DELETE** `/parking-spots/{id}`
Deleta uma vaga cadastrada.

**Path Parameters:**
- `id` (UUID): ID da vaga

**Response (204 No Content):** Deletado com sucesso

---

##  Exemplos de Requisições

### Criar uma nova vaga (cURL)

```bash
curl -X POST http://localhost:8080/parking-spots \
  -H "Content-Type: application/json" \
  -d '{
    "parkingSpotNumber": "A001",
    "licensePlateCar": "ABC1234",
    "brandCar": "Toyota",
    "modelCar": "Corolla",
    "colorCar": "White",
    "responsibleName": "João Silva",
    "apartment": "101",
    "block": "A"
  }'
```

### Listar todas as vagas (cURL)

```bash
curl -X GET http://localhost:8080/parking-spots
```

### Buscar uma vaga específica (cURL)

```bash
curl -X GET http://localhost:8080/parking-spots/{id}
```

### Atualizar uma vaga (cURL)

```bash
curl -X PUT http://localhost:8080/parking-spots/{id} \
  -H "Content-Type: application/json" \
  -d '{
    "parkingSpotNumber": "A001",
    "licensePlateCar": "ABC1234",
    "brandCar": "Toyota",
    "modelCar": "Corolla",
    "colorCar": "Black",
    "responsibleName": "João Silva",
    "apartment": "101",
    "block": "A"
  }'
```

### Deletar uma vaga (cURL)

```bash
curl -X DELETE http://localhost:8080/parking-spots/{id}
```

##  Configuração do Banco de Dados

### Estrutura da Tabela TB_PARKING_SPOT

```sql
CREATE TABLE TB_PARKING_SPOT (
  id UUID PRIMARY KEY,
  parking_spot_number VARCHAR(10) NOT NULL UNIQUE,
  license_plate_car VARCHAR(7) NOT NULL UNIQUE,
  brand_car VARCHAR(70) NOT NULL,
  model_car VARCHAR(70) NOT NULL,
  color_car VARCHAR(70) NOT NULL,
  registration_date TIMESTAMP NOT NULL,
  responsible_name VARCHAR(130) NOT NULL,
  apartment VARCHAR(30) NOT NULL,
  block VARCHAR(30) NOT NULL,
  CONSTRAINT uk_apartment_block UNIQUE (apartment, block)
);
```

### Inicializar o Banco

O Hibernatepode criar automaticamente as tabelas com a configuração:
```properties
spring.jpa.hibernate.ddl-auto=update
```

##  Como Executar

### Opção 1: Executar Localmente

**1. Iniciar o PostgreSQL**

```bash
# Se usando Docker
docker run --name postgres_parking \
  -e POSTGRES_PASSWORD=postgres \
  -e POSTGRES_DB=parking_control_db \
  -p 5432:5432 \
  -d postgres:latest
```

**2. Compilar o projeto**

```bash
./mvnw clean install
```

**3. Executar a aplicação**

```bash
./mvnw spring-boot:run
```

A aplicação estará disponível em: `http://localhost:8080`

---

### Opção 2: Executar com Docker Compose

**1. Verificar se Docker e Docker Compose estão instalados**

```bash
docker --version
docker-compose --version
```

**2. Iniciar os serviços**

```bash
cd infra/docker
docker-compose up -d
```

**3. Parar os serviços**

```bash
docker-compose down
```

---

## 🐳 Docker

### Dockerfile (Exemplo para criar imagem)

```dockerfile
FROM maven:3.9-eclipse-temurin-21 as build
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /app/target/parking-control-*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Construir e executar a imagem

```bash
# Build da imagem
docker build -t parking-control:latest .

# Executar container
docker run -p 8080:8080 \
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://host.docker.internal:5432/parking_control_db \
  -e SPRING_DATASOURCE_USERNAME=postgres \
  -e SPRING_DATASOURCE_PASSWORD=postgres \
  parking-control:latest
```

##  Melhorias Futuras

###  Segurança
- [ ] Implementar **Spring Security** com autenticação JWT
- [ ] Adicionar autorização baseada em papéis (RBAC)
- [ ] Implementar rate limiting para proteger contra ataques DDoS
- [ ] Criptografar dados sensíveis (placa de veículo, nome do morador)
- [ ] Validação de CORS mais restritiva

###  Funcionalidades Adicionais
- [ ] Implementar **soft delete** para manter histórico de vagas
- [ ] Adicionar tracking de **histórico de modificações** (audit log)
- [ ] Sistema de relatórios com gráficos e estatísticas
- [ ] Controle de acesso tipo **entrada/saída** de veículos
- [ ] Sistema de **notificações** (SMS/Email) para avisos de vagas
- [ ] Integração com **câmeras de vigilância** para reconhecimento de placa
- [ ] Sistema de **multas e penalidades**
- [ ] **Dashboard web** para visualização de vagas em tempo real

###  Banco de Dados
- [ ] Implementar **replicação e backup automático**
- [ ] Adicionar índices para melhorar performance em grandes volumes
- [ ] Implementar particionamento de tabelas para melhor escalabilidade
- [ ] Cache distribuído com **Redis**

### Testes e Qualidade
- [ ] Expandir cobertura de testes unitários (target: 80%+)
- [ ] Implementar testes de integração com TestContainers
- [ ] Adicionar testes de carga e stress testing
- [ ] Configurar **CI/CD pipeline** (GitHub Actions, Jenkins, etc.)
- [ ] Análise de código com SonarQube

###  API e Documentação
- [ ] Integrar **Swagger/OpenAPI** para documentação interativa
- [ ] Implementar **versionamento de API** (v1, v2)
- [ ] Adicionar endpoint de health check
- [ ] Implementar **paginação e filtros** avançados
- [ ] Sistema de **busca por múltiplos critérios**

### ⚡ Performance e Escalabilidade
- [ ] Implementar **cache de leitura** com Spring Cache
- [ ] Otimizar queries com **projections** do Spring Data
- [ ] Implementar **lazy loading** adequadamente
- [ ] Monitoramento com **Micrometer e Prometheus**
- [ ] Implementar **circuit breaker** para chamadas externas
- [ ] Message queue para operações assíncronas (RabbitMQ, Kafka)

### Operacional
- [ ] Configurar **logs estruturados** (ELK Stack)
- [ ] Implementar **métricas de aplicação** (Prometheus)
- [ ] Criar **helm charts** para Kubernetes
- [ ] Documentação de arquitetura (ADR - Architecture Decision Records)
- [ ] Guia de contribuição e code style

###  Frontend
- [ ] Desenvolver **aplicação web** (React, Vue, Angular)
- [ ] Criar **aplicativo mobile** (React Native, Flutter)
- [ ] Interface para gestores de condomínio
- [ ] Dashboard de usuários

### 🐒
