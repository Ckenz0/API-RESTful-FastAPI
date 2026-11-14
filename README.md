# API RESTful FastAPI

API de mensagens com Python 3.12, FastAPI e Docker. Permite criar, listar, consultar, atualizar e excluir mensagens, com respostas em JSON.

[Azure](https://api-restful-fastapi-dcfcaqgxc4duf0gd.brazilsouth-01.azurewebsites.net/docs) · [Coleção do Postman](postman/messages-api.postman_collection.json)

## Docker

```powershell
docker build -t api-restful-fastapi:local .
docker run -d --name api-restful-fastapi-local -p 127.0.0.1:8000:8000 api-restful-fastapi:local
```

- API: <http://127.0.0.1:8000/>
- Mensagens: <http://127.0.0.1:8000/api/mensagens>
- Swagger: <http://127.0.0.1:8000/docs>

Para acompanhar os logs ou parar o container:

```powershell
docker logs api-restful-fastapi-local
docker stop api-restful-fastapi-local
```

Para iniciar o mesmo container novamente, use `docker start api-restful-fastapi-local`.

## Rotas

| Método | Rota | Descrição |
| --- | --- | --- |
| GET | `/` | Mensagem de boas-vindas |
| GET | `/api/mensagens` | Lista as mensagens |
| GET | `/api/mensagens/{id}` | Consulta uma mensagem |
| POST | `/api/mensagens` | Cria uma mensagem |
| PUT | `/api/mensagens/{id}` | Atualiza uma mensagem |
| DELETE | `/api/mensagens/{id}` | Exclui uma mensagem |

POST e PUT recebem um objeto JSON como `{"texto": "Olá, FastAPI!"}`. Entradas inválidas retornam 422 e mensagens inexistentes retornam 404. As mensagens ficam em memória e são apagadas quando a aplicação reinicia.

## Testes

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements-dev.txt
.\.venv\Scripts\python.exe -m unittest discover -s tests -v
```

No Postman, importe [a coleção](postman/messages-api.postman_collection.json) e execute **Run collection**. A variável `baseUrl` aponta para a API no Azure; para testar o container local, troque por `http://127.0.0.1:8000`.

## Deploy

O GitHub Actions executa os testes, cria a imagem Docker e testa o container com a coleção do Postman. Após alterações na `main`, publica a imagem em `ghcr.io/ckenz0/api-restful-fastapi` e atualiza o Azure App Service com a versão do commit. A API publicada também é testada pelo pipeline.

A imagem no GHCR precisa estar pública para o Azure baixá-la. A conexão com o App Service usa OIDC e a variável `AZURE_WEBAPP_NAME`; sem essa variável, o pipeline executa os testes e publica a imagem, deixando o deploy desabilitado. O container atende na porta 8000.

Autor: Cauê Kenzo Adaniya.
