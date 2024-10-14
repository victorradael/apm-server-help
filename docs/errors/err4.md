## Falha no Login com Usuário `kibana_user`

### Erro:

O usuário `kibana_user`, criado com o papel `kibana_system`, não conseguiu efetuar login no Kibana, enquanto o superusuário `elastic` conseguiu.

### Causa:

O papel `kibana_system` é destinado a operações internas do Kibana, não sendo adequado para logins administrativos.

### Solução:

Criar um novo usuário com permissões de superusuário.

#### Criar um Usuário Superusuário (`kibana_admin`):

Execute o seguinte comando para criar um novo usuário:

```bash
curl -u elastic:<senha_do_elastic> -X POST "http://localhost:9200/_security/user/kibana_admin" -H "Content-Type: application/json" -d'
{
  "password" : "your_secure_password",
  "roles" : [ "superuser" ],
  "full_name" : "Kibana Admin"
}
'
```

#### Atualizar o `docker-compose.yml`:

Configure o Kibana para usar o novo usuário `kibana_admin`:

```yaml
kibana:
  environment:
    - ELASTICSEARCH_USERNAME=kibana_admin
    - ELASTICSEARCH_PASSWORD=your_secure_password
```

Após isso, o login foi possível usando o novo usuário `kibana_admin`.

---

[Voltar para o tutorial](../config.md)
