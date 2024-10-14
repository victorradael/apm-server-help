## Erro no Kibana Relacionado ao Fleet - Segurança Não Habilitada

### Erro:

O Kibana exibiu o seguinte erro, relacionado ao Fleet:

```
Kibana security must be enabled to use Fleet
```

### Causa:

O Fleet, um plugin do Kibana, requer que a segurança do Kibana esteja habilitada. No entanto, apesar de a segurança estar ativada no `docker-compose.yml`, a autenticação necessária não estava configurada corretamente.

### Solução:

- **Ativar APIs de Segurança**: Habilitar a autenticação por chave de API para garantir que o Kibana e o APM Server possam autenticar corretamente.

  No `docker-compose.yml`, ajuste a configuração do Kibana e Elasticsearch para habilitar as chaves de API:

  ```yaml
  elasticsearch:
    environment:
      - xpack.security.enabled=true
      - xpack.security.authc.api_key.enabled=true

  kibana:
    environment:
      - xpack.security.enabled=true
      - xpack.security.authc.api_key.enabled=true
  ```

- **Remover a Configuração de Segurança Temporariamente** (para fins de teste, se necessário): Desabilitar temporariamente a segurança para verificar se o Fleet funciona sem autenticação:
  ```yaml
  - xpack.security.enabled=false
  ```
  [Erro 4 Complementar](./err4.md)

---

[Voltar para o tutorial](../config.md)
