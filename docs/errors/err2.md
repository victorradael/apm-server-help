## Erro de Login no Kibana - `security_exception`

### Erro:

Ao tentar acessar o Kibana, o seguinte erro foi exibido:

```
security_exception: missing authentication credentials for REST request [/_nodes?filter_path=nodes.*.version%2Cnodes.*.http.publish_address%2Cnodes.*.ip]
```

### Causa:

Esse erro ocorre porque o Kibana não estava fornecendo as credenciais corretas para se autenticar no Elasticsearch.

### Solução:

- **Adicionar Credenciais do `elastic` no Kibana**: Atualize o `docker-compose.yml` para incluir as credenciais do superusuário `elastic` no Kibana.

  ```yaml
  kibana:
    environment:
      - ELASTICSEARCH_USERNAME=elastic
      - ELASTICSEARCH_PASSWORD=<senha_do_elastic>
  ```

- **Gerar uma Chave de API para o APM Server**: Caso precise usar o APM Server, certifique-se de gerar uma chave de API corretamente.

[Erro 4 Relacionado](./err4.md)

---

[Voltar para o tutorial](../config.md)
