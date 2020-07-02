# API Médicos
> API para agendamento de consultas de médicos baseados na sua proximidade

![](https://img.shields.io/badge/php->=%207.3-brightgreen)
![](https://img.shields.io/badge/mysql-v10.1.37-brightgreen)

## Objetivo da API
Esta foi uma API desenvolvida para servir de busca por médicos cadastrados
previamente, baseados na distância e na disponibilidade de horários.

## Como usar
Primeiramente, coloque a pasta **api** no diretório raiz do site e siga com as
configurações indicadas.

### Configurações
Dentro da pasta **config** estão todas as configurações que se fazem necessárias
cada qual no seu próprio arquivo/pasta. Lembrando que todos os arquivos de configurações possuem o esquema
```javascript
'NOME_DA_CONFIGURAÇÃO' => 'VALOR';
```
onde o que deve ser mudado é apenas o valor, a menos que mude no código posteriormente.
#### Arquivos
##### database.php
Aqui estão as configurações gerais do banco de dados, tais como host, nome, usuário...
##### directory.php
Aqui estão as configurações gerais das pastas da aplicação, tais como o nome da pasta onde está a api, a pasta das classes e do cache utilizado para o carregamento mais rápido da API.
##### message.php
Este arquivo apenas contém a configuração para o idioma das mensagens retornadas pela API. O valor deve ser o mesmo nome do arquivo contido em config/lang
##### misc.php
Contém as configurações gerais que não se aplicam a nenhuma outra categoria, como a extensão dos arquivos de classe, o host permitido para o acesso e algumas outras configurações.
* **host_url:** O host que a API permitirá o acesso. **MUDE PARA O LINK DO SEU SITE, CASO CONTRÁRIO, PERMITIRÁ QUE QUALQUER UM ACESSE**
* **convert_space:** A configuração que irá transformar os caracteres vazios (espaços) para serem passadas pela url sem erro.
* **min_hour:** A quantidade de horas necessárias para que não haja consultas em um espaço muito curto de tempo. Só serão aceitas consultas próximas, caso o horário marcado esteja nesse intervalo.
* **max_distance:** A distância máxima a ser considerada para o agendamento de consultas no horário próximo. (É medida em metros).
* **valid_cache:** O tempo máximo que o cache da consulta terá validade. Após esse tempo, será necessário a criação de um novo arquivo atualizado, porém poderá levar um tempo maior.
* **valid_cache_unit:** Unidade de medida do tempo do cache. São aceitos:
  * S => segundos (Valor padrão)
  * M => minutos
  * H => horas
#### Pastas
##### /database
Contém todos os arquivos das tabelas necessárias para o banco de dados, assim como suas colunas. Devem ser mudados os nomes, conforme necessidade apenas do lado do `=> 'Valor'`
##### /lang
Pasta com os idiomas para as mensagens da API. Podem ser modificadas para apresentar as mensagens escolhidas por você.

As mensagens também podem conter variáveis pré-definidas que serão substituidas pelo valor especificado na documentação
* **{class_name}** será substituído pelo nome da classe onde está sendo chamado o script.

### Instalação
Caso não possua alguma tabela necessária para o funcionamento, é fortemente recomendado criá-la, ou simplesmente executar o script chamado `db.php` que serão criadas as tabelas restantes de forma automática.
```cmd
$ php db.php [-force | -help | -check]
```
O uso dos atributos é opcional
* **-force:** forçar a criação das tabelas (isso apagará todas e criará tudo do zero). Nunca use em servidores que estejam em produção, apenas para fins de testes.
* **-help:** exibe esta ajuda.
* **-check:** verifica se todas as tabelas estão corretas.

### Uso
O uso é todo feito por chamadas HTTP/JS. O retorno de todas as chamadas é um objeto JSON que pode ser tratado da melhor maneira pelo cliente. Os exemplos aqui serão dados com base na função fetch, implementada no HTML5, mas podem ser facilmente adaptados para funcionar com AJAX.
#### Listar médicos
Para listar os médicos existentes, pode-se fazer a seguinte chamada http
```apache
$ GET /medic/list
```
ou em JS
```javascript
fetch('/medic/list')
.then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "code": 200,
  "total": 5,
  "msg": "Registros encontrados",
  "result": [
    {
      "id": 5,
      "title": "Dr",
      "name": "Joaquim"
    },{
      "id": 6,
      "title": "Dra",
      "name": "Manuela"
    }
  ]
}
```
#### Listar médicos por especialidade
```apache
$ GET /medic/list/specialist/{SPEC}
```
```javascript
fetch('/medic/list/specialist/{SPEC}')
.then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "code": 200,
  "total": 5,
  "msg": "Registros encontrados",
  "result": [
    {
      "id": 5,
      "title": "Dr",
      "name": "Joaquim"
    },{
      "id": 6,
      "title": "Dra",
      "name": "Manuela"
    }
  ]
}
```
#### Listar médicos disponíveis para atendimento
```apache
$ POST /medic/list/area/{PACIENT}
```
```javascript
var form = new FormData(document.getElementById('consult'));
fetch('/medic/list/area/{PACIENT}', {
  method: "POST",
  body: form
})
.then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "code": 200,
  "total": 5,
  "msg": "Registros encontrados",
  "result": [
    {
      "id": 5,
      "title": "Dr",
      "name": "Joaquim"
    },{
      "id": 6,
      "title": "Dra",
      "name": "Manuela"
    }
  ]
}
```
#### Resgatar um médico específico
Também é possível resgatar um médico tendo o seu id. É útil para fazer alterações.
```apache
$ GET /medic/get/{ID}
```
```javascript
fetch('/medic/get/{ID}')
.then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "id": 5,
  "title": "Dr",
  "name": "Joaquim",
  "consults": [
    {
      "id": 1,
      "pacient": {
        "id": 653,
        "name": "Rafael",
        "sex": "M"
      },
      "value": 279.00,
      "done": true
    }
  ]
}
```
#### Inserir um médico novo
```apache
$ POST /medic/post
```
```javascript
var form = new FormData(document.getElementById('medic'));
fetch('/medic/post', {
  method: "POST",
  body: form
}).then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "code": 678,
  "msg": "Dados salvos com sucesso!"
}
```
#### Atualizar um médico existente
```apache
$ POST /medic/put
```
```javascript
var form = new FormData(document.getElementById('medic'));
fetch('/medic/put', {
  method: "POST",
  body: form
}).then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "code": 678,
  "msg": "Dados salvos com sucesso!"
}
```
#### Bloquear um médico
```apache
$ POST /medic/block
```
```javascript
var form = new FormData(document.getElementById('medic'));
fetch('/medic/block', {
  method: "POST",
  body: form
}).then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "code": 200,
  "msg": "Bloqueio realizado com sucesso!"
}
```
#### Desbloquear um médico
```apache
$ POST /medic/unblock
```
```javascript
var form = new FormData(document.getElementById('medic'));
fetch('/medic/unblock', {
  method: "POST",
  body: form
}).then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "code": 200,
  "msg": "Desbloqueio realizado com sucesso!"
}
```
#### Listar pacientes
Para listar os pacientes existentes, pode-se fazer a seguinte chamada http.
O parâmetro {query} é opcional
```apache
$ GET /pacient/list/{query}
```
ou em JS
```javascript
fetch('/pacient/list/{query}')
.then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "code": 200,
  "total": 5,
  "msg": "Registros encontrados",
  "result": [
    {
      "id": 5,
      "title": "Dr",
      "name": "Joaquim"
    },{
      "id": 6,
      "title": "Dra",
      "name": "Manuela"
    }
  ]
}
```
#### Resgatar um paciente específico
Também é possível resgatar um paciente tendo o seu id. É útil para fazer alterações.
```apache
$ GET /pacient/get/{ID}
```
```javascript
fetch('/pacient/get/{ID}')
.then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "id": 5,
  "title": "Dr",
  "name": "Joaquim",
  "consults": [
    {
      "id": 1,
      "pacient": {
        "id": 653,
        "name": "Rafael",
        "sex": "M"
      },
      "value": 279.00,
      "done": true
    }
  ]
}
```
#### Inserir um paciente novo
```apache
$ POST /pacient/post
```
```javascript
var form = new FormData(document.getElementById('pacient'));
fetch('/pacient/post', {
  method: "POST",
  body: form
}).then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "code": 678,
  "msg": "Dados salvos com sucesso!"
}
```
#### Atualizar um paciente existente
```apache
$ POST /pacient/put
```
```javascript
var form = new FormData(document.getElementById('pacient'));
fetch('/pacient/put', {
  method: "POST",
  body: form
}).then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "code": 678,
  "msg": "Dados salvos com sucesso!"
}
```
#### Bloquear um paciente
```apache
$ POST /pacient/block
```
```javascript
var form = new FormData(document.getElementById('pacient'));
fetch('/pacient/block', {
  method: "POST",
  body: form
}).then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "code": 200,
  "msg": "Bloqueio realizado com sucesso!"
}
```
#### Desbloquear um paciente
```apache
$ POST /pacient/unblock
```
```javascript
var form = new FormData(document.getElementById('pacient'));
fetch('/pacient/unblock', {
  method: "POST",
  body: form
}).then(data => data.json())
.then((data) => {
  // Faça alguma coisa aqui
}).catch(error => console.error(error));
```
A resposta esperada é um JSON similar ao abaixo
```json
{
  "code": 200,
  "msg": "Desbloqueio realizado com sucesso!"
}
```
