[Voltar para o README](../README.MD)

# Configuração do Elasticsearch, Kibana e APM Server

## Passo 1: Configurar o Docker Compose para Elasticsearch, Kibana e APM Server

Primeiro, crie um arquivo [`docker-compose.yml`](../docker-compose.yml) com a seguinte estrutura básica para configurar Elasticsearch, Kibana e APM Server:

---

## Passo 2: Inicializar os Containers

Após configurar o arquivo `docker-compose.yml`, inicialize os serviços:

```bash
docker-compose up -d
```

Verifique se os containers estão rodando corretamente:

```bash
docker ps
```

Você deve ver os containers `elasticsearch`, `kibana`, e `apm-server` listados.

---

### Problema Relacionado:

- [**Kibana Não Consegue Resolver o Nome `elasticsearch`**](./errors/err3.md)

---

## Passo 3: Gerar Senha para o Usuário `elastic`

Se você ainda não tiver a senha do superusuário `elastic`, pode gerá-la utilizando o comando de reset:

```bash
docker exec -it elasticsearch /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic
```

Guarde a senha gerada, pois ela será usada para criar novos usuários e configurar permissões.

---

### Problemas Relacionados:

- [**Erro no Kibana Relacionado ao Fleet - Segurança Não Habilitada**](./errors/err1.md)
- [**Falha no Login com Usuário `kibana_user`**](./errors/err4.md)

---

## Passo 4: Criar Usuário `kibana_admin` com Permissões de Superusuário

Como o papel `kibana_system` não permite login no Kibana, você precisará criar um usuário com permissões mais amplas para acessar o painel.

Execute o seguinte comando para criar um novo usuário `kibana_admin` com permissões de superusuário:

```bash
curl -u elastic:<SENHA_DO_ELASTIC> -X POST "http://localhost:9200/_security/user/kibana_admin" -H "Content-Type: application/json" -d'
{
  "password" : "your_secure_password",
  "roles" : [ "superuser" ],
  "full_name" : "Kibana Admin User"
}
'
```

Esse novo usuário `kibana_admin` terá permissões completas, permitindo o acesso ao Kibana sem problemas.

Atualize o `docker-compose.yml` para que o Kibana utilize esse novo usuário:

```yaml
kibana:
  environment:
    - ELASTICSEARCH_USERNAME=kibana_admin
    - ELASTICSEARCH_PASSWORD=your_secure_password
```

---

### Problema Relacionado:

- [**Falha no Login com Usuário `kibana_user`**](./errors/err4.md)

---

## Passo 5: Gerar Chave de API para o APM Server

Agora, vamos gerar uma chave de API para o APM Server, que permitirá que ele se autentique no Elasticsearch.

Execute o seguinte comando para gerar a chave de API:

```bash
curl -u elastic:<SENHA_DO_ELASTIC> -X POST "http://localhost:9200/_security/api_key" -H "Content-Type: application/json" -d'
{
  "name": "apm-server-key",
  "expiration": "1d",
  "role_descriptors": {
    "apm_writer": {
      "cluster": ["monitor", "read_ilm"],
      "index": [
        {
          "names": ["apm-*"],
          "privileges": ["write", "create_index"]
        }
      ]
    }
  }
}
'
```

Esse comando retornará um JSON contendo a chave de API gerada.

Atualize o `docker-compose.yml` do APM Server com a chave de API gerada:

```yaml
apm-server:
  environment:
    - output.elasticsearch.api_key=<API_KEY_GERADA>
```

---

### Problema Relacionado:

- [**Erro no APM Server - Autenticação com Chave de API**](./errors/err5.md)

---

## **Passo 6: Reiniciar Todos os Containers**

Após configurar tudo, reinicie os containers para aplicar as alterações:

```bash
docker-compose down
docker-compose up -d
```

Verifique novamente os logs para garantir que todos os serviços estão funcionando corretamente:

```bash
docker logs -f elasticsearch
docker logs -f kibana
docker logs -f apm-server
```

---

[Voltar para o README](../README.MD)
