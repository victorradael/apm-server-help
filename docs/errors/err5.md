## Erro no APM Server - Autenticação com Chave de API

### Erro:

O APM Server não conseguiu se autenticar no Elasticsearch, indicando problemas com a chave de API.

### Solução:

1. **Gerar uma Chave de API para o APM Server**:

   ```bash
   curl -u elastic:<senha_do_elastic> -X POST "http://localhost:9200/_security/api_key" -H "Content-Type: application/json" -d'
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

2. **Configurar o APM Server com a Chave de API**:
   No `docker-compose.yml`, configure o APM Server para usar a chave de API gerada:
   ```yaml
   apm-server:
     environment:
       - output.elasticsearch.api_key=<API_KEY_GERADA>
   ```

Após isso, o APM Server foi capaz de se autenticar e enviar dados para o Elasticsearch.

---

[Voltar para o tutorial](../config.md)
