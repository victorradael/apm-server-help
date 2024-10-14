## Kibana Não Consegue Resolver o Nome `elasticsearch`

### Erro:

O Kibana não conseguiu resolver o nome de host `elasticsearch`, com o erro:

```
getaddrinfo ENOTFOUND elasticsearch
```

### Causa:

Esse erro ocorreu devido a problemas de rede Docker, onde os containers não estavam se comunicando corretamente.

### Soluções:

- **Verifique a disponibilidade do serviço** com `docker ps`.

- **Verificar o Nome do Serviço no `docker-compose.yml`**: Certifique-se de que o nome do serviço `elasticsearch` está correto.

  No Kibana:

  ```yaml
  kibana:
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
  ```

- **Verificar Conectividade Entre os Containers**: Dentro do container do Kibana, execute:

  ```bash
  curl http://elasticsearch:9200
  ```

- **Verificar a Rede Docker**: Certifique-se de que ambos os serviços (`elasticsearch` e `kibana`) estão na mesma rede Docker:
  ```yaml
  networks:
    - elastic
  ```

---

[Voltar para o tutorial](../config.md)
