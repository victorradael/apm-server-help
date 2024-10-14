# Elastic APM Stack (Local Environment)

Este projeto fornece um ambiente local para o Elastic APM, permitindo a integração com aplicações locais ou em containers usando Docker Compose.

## Serviços incluídos

- **Elasticsearch**: Banco de dados NoSQL que armazena os dados coletados pelo APM.
- **Kibana**: Interface web para visualizar e monitorar os dados do APM.
- **APM Server**: Responsável por coletar e enviar os dados de performance das aplicações para o Elasticsearch.

## Pré-requisitos

- [Docker](https://www.docker.com/get-started) e [Docker Compose](https://docs.docker.com/compose/install/) instalados.

## Como utilizar

### 1. Clone este repositório

```bash
git clone https://github.com/seu-usuario/apm-server-local-env.git
cd apm-server-local-env
```

### 2. Inicie os containers

```bash
docker-compose up -d
```

### 3. Verifique se os serviços estão funcionando

- **Elasticsearch**: [http://localhost:9200](http://localhost:9200)
- **Kibana**: [http://localhost:5601](http://localhost:5601)
- **APM Server**: [http://localhost:8200](http://localhost:8200)

### 4. Configure suas aplicações para enviar dados para o APM Server

Utilize o seguinte endpoint para configurar suas aplicações:

```
http://localhost:8200
```

#### Exemplo de configuração em uma aplicação Java (Spring Boot)

No arquivo de propriedades (`application.properties`):

```properties
elastic.apm.server_urls=http://localhost:8200
elastic.apm.service_name=nome-do-seu-servico
elastic.apm.application_packages=com.seu.pacote
```

#### Exemplo de configuração para aplicações Node.js

1. Instale o pacote APM para Node.js:

   ```bash
   npm install elastic-apm-node --save
   ```

2. Adicione a seguinte configuração no arquivo principal da sua aplicação (`app.js`, `index.js`, etc.):

   ```javascript
   const apm = require("elastic-apm-node").start({
     serviceName: "nome-do-seu-servico",
     serverUrl: "http://localhost:8200",
     environment: "development", // Ambiente (desenvolvimento, produção, etc.)
     captureBody: "all", // Captura o corpo das requisições
   });

   const express = require("express");
   const app = express();

   app.get("/", (req, res) => {
     res.send("Hello, Elastic APM!");
   });

   app.listen(3000, () => {
     console.log("Aplicação rodando na porta 3000");
   });
   ```

#### Exemplo de configuração para aplicações GoLang

1. Instale o pacote APM para GoLang:

   ```bash
   go get go.elastic.co/apm
   ```

2. Adicione a seguinte configuração no seu código Go:

   ```go
   package main

   import (
     "fmt"
     "log"
     "net/http"
     "go.elastic.co/apm"
     "go.elastic.co/apm/module/apmhttp"
   )

   func main() {
     handler := apmhttp.Wrap(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
       fmt.Fprintf(w, "Hello, Elastic APM!")
     }))

     http.Handle("/", handler)
     log.Println("Servidor rodando na porta 8080...")
     log.Fatal(http.ListenAndServe(":8080", nil))
   }
   ```

3. Defina as variáveis de ambiente para o APM Server:

   ```bash
   export ELASTIC_APM_SERVER_URL=http://localhost:8200
   export ELASTIC_APM_SERVICE_NAME=nome-do-seu-servico
   ```

#### Exemplo de configuração para aplicações Python

1. Instale o pacote APM para Python:

   ```bash
   pip install elastic-apm
   ```

2. Integre o APM ao seu código Python (por exemplo, em uma aplicação Flask):

   ```python
   from flask import Flask
   from elasticapm.contrib.flask import ElasticAPM

   app = Flask(__name__)

   app.config['ELASTIC_APM'] = {
     'SERVICE_NAME': 'nome-do-seu-servico',
     'SERVER_URL': 'http://localhost:8200',
     'ENVIRONMENT': 'development'
   }

   apm = ElasticAPM(app)

   @app.route('/')
   def hello():
       return "Hello, Elastic APM!"

   if __name__ == '__main__':
       app.run(port=5000)
   ```

3. Execute a aplicação Flask:

   ```bash
   flask run
   ```

### 5. Acesse o Kibana para visualizar os dados APM

- Abra o Kibana em: [http://localhost:5601](http://localhost:5601)
- Vá para a aba **APM** e comece a monitorar suas aplicações.

### 6. Encerrando o ambiente

Para parar os containers:

```bash
docker-compose down
```

## Persistência de dados

Os dados do Elasticsearch são persistidos localmente no volume `esdata` para que não sejam perdidos após reiniciar os containers.

---

### [**Configurações**](./docs/config.md)

---
