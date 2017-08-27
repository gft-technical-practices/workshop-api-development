# Workshop API Development
O objetivo deste workshop é demonstrar os conceitos que estão envolvidos no desenvolvimento de uma API. Para este propósito será utilizado uma ferramenta de ESB (IBM Integration Bus) para implementar esta API.

O foco não é realizar uma implementação complexa e para isso este runtime terá um papel de fornecer um sandbox e não implementar questões complexas.

Quanto a exposição da API será utilizada um produto de API Manager (WSO2 API Manager). O papel desta ferramenta é fornecer uma módulo de gerenciamento integrado com um API Gateway e Developer Portal.

## Ferramentas
- IBM Integration Bus 10.0.0.3
- WSO2 API Manager 2.0.0

## Implementando uma BackEnd API
Uma ***Backend API*** é um conceito utilizado para se referir a uma API que está implementada / exposta diretamente no backend. As características tecnológicas deste backend que implementa a API poderiam ser: SpringBoot, Node.JS com express, Apache Camel, Enterprise Service Bus.

Esta API não expoem políticas de segurança e monitoramento. O cenário arquitetural e tratar estas políticas no entrey point.

Geralmente isso é feito utilizando uma plataforma de API Manager expondo as APIs em um API Gateway. Esta exposição de uma APÌ de backend é conhecida como Frontend API, pois é com essa frontend API que o consumidor irá falar diretamente.

### API Gateway
O API gateway é um design pattern que tem como objetivo:
- Disponibilizar um ponto de entrada para todos os clientes que irão consumir as APIs da organização
- Ter capacidades e responsabilidades de realizar autenticação, autorização, segurança, verificação de ameaças, throttling e proxying
- Compor a plataforma de execução das APIs dentro do ciclo de vida de um API Management

### Enterprise Service Bus (ESB)
Enterprise Serive Bus ou conhecido como barramento de serviços é o runtime que implementa um subconjunto dos Enterprise Integration Patterns:
- Filtragem
- Roteamento
- Agragação
- Transformação
- Enriquecimento
- Dead Letter Channel
- Orquestração

Este design pattern faz sentido para grandes corporações, pois existem muitas aplicações legadas e criar APIs / Integrações para canais modernos precisam passar por este tipo de camada.

Para arquiteturas mais disruptivas como a de Microserviços ou uma decomposição de um legado monolítico. A construção de uma ***Backend API*** não precisa de um ESB para orquestrar. Neste cenário a ***Backend API*** é o próprio sistema produto.

Referência:
- [Enterprise Integrations Patterns](https://www.safaribooksonline.com/library/view/enterprise-integration-patterns/0321200683/)

### Implementação
As apis foram desenvolvidas somente para os seguintes fluxos:

- /users/v1/users
- /users/v1/users/{user_id}

Seguem os dados do sandbox de cada um:

***/users/v1/users***
```javascript
[
      {
      "id": "1",
      "username": "rafaelm",
      "firstName": "Rafael",
      "lastName": "Manzoni",
      "email": "rafaelm@gft.com",
      "password": "123456",
      "phone": "(11) 98645-8787"
   },
      {
      "id": "2",
      "username": "josec",
      "firstName": "José Carlos",
      "lastName": "Oliveira",
      "email": "josecoli@gft.com",
      "password": "123457",
      "phone": "(11) 99571-8756"
   }
]
```

***/users/v1/users/{user_id}***
```javascript
{
   "id": "1",
   "username": "rafaelm",
   "firstName": "Rafael",
   "lastName": "Manzoni",
   "email": "rafaelm@gft.com",
   "password": "123456",
   "phone": "(11) 98645-8787"
}
```

*Teste das APIs*
- curl -X GET "http://localhost:7800/users/v1/users" -H "accept: application/json"

## Expondo da API utilizando uma Plataforma de API Mangaer (WSO2 API Manager)

### Criando / Publicando uma Frontend API
A ***Frontend API*** é o conceito utilizado para se referir a API que será exposta para os canais consumidores. Esta API geralmente é criada e gerenciada pelo módulo do API manager para disponibilizar um proxy no API gateway, onde políticas de segurança e controle de vazão são aplicadas.

- ***Frontend API***: API que será contruída no API Manager da WSO2 e consumida pelos canais.

Portanto, acesse o portal Publisher do WSO2 com o usuário administrativo:
- usuário: admin
- senha: admin

Execute as seguintes tarefas para criar a API:
- Crie a API com o swagger Users
- Utilize as configurações padrões para criar e publicar a api

Para maiores informações sobre o produto consulte a documentação [API Manager - WSO2 - Docs](https://docs.wso2.com/display/AM200/Getting+Started).
- [Tutorial - Criar uma API](https://docs.wso2.com/display/AM200/Create+and+Publish+an+API)


### Sou um Developer. Preciso assinar esta API
Nesta etapa vamos executar o papel de um desenvolver que irá consumir a API do pague-rapido.

Para esta atividade realize as atividades a seguir
- Acesse o portal do desenvolvedor */store*
- Crie um novo usuário como desenvolvedor
- Realize o login
- Crie uma nova aplicação que irá consumir a api
- Para esta nova api gere as chaves de acesso a API
- Selecione a api de pague-rapido e a assine
- Realize o teste na aba de api console
- Realize o teste no postman utilizando as credenciais gerada para sua aplicação

Para maiores informações:
- [Tutorial - Assinar uma API](https://docs.wso2.com/display/AM200/Subscribe+to+an+API)

Observações:
 - Durante os testes no console, caso as chamadas retornem erro, verifique se a chamada é HTTPS (Ex.: https://localhost:8243/users/v1/users). Caso seja, abra a URL em uma nova aba e aceite o certificado de segurança.

### Sua API não está segura, o que fazer?
API Gateway possuem mecanismos de policies para fazer o controle de sergurança e ameaças.
Para este exercício vamos editar e republicar a API para aplicar uma política de prevenção a ataques DDoS (Política de Throttling).

Para este passo atue como administrador das APIs
- Acesse o portal publisher
- Edite a api de ***users***
- Configure uma política de Throttling para 10K por minuto
- Salve e publique a api

Na sequência atue como um desenvolvedor da api
- Realize um teste de carga com a ferramenta SoapUI para atingir a taxa que irá barrar o acesso a API
- Para maiores informações sobre um load test do SoapUI. [SoapUI Load Test](https://www.soapui.org/load-testing/concept.html)


### Auditoria
Como estatística e registro de auditoria os payloads ajudam de maneira geral em investigações de problemas e avaliações de auditoria.

Para isto atue como administrador da API para configurar a auditoria:
- Acesse o portal publisher
- Edite a api de users
- Configure uma política de mediação log message para o fluxo de entrada e saída da API
- Salve e publique a api

Na sequência atue como desenvolvedor da api
- Realize o teste da api novamente
- Acompanhe no log do CMD os dados de entrada e saída de cada requisição / resposta

## Concluindo
Neste workshop passamos pelos principais conceitos que envolvem o API Management e focamos no produto Open Source da WSO2. Porém, existem vários outros produtos e estratégias customizadas para trabalhar com a exposição de APIs publicas e privadas.

Para maiores informações:
- [RESTful Cookbook](http://restcookbook.com/)
- [Enterprise Integrations Patterns](https://www.safaribooksonline.com/library/view/enterprise-integration-patterns/0321200683/)
- [Winning in the API Economy](https://www.3scale.net/wp-content/uploads/2013/10/Winning-in-the-API-Economy-eBook-3scale.pdf)
- [API - A Strategy Guide](http://shop.oreilly.com/product/0636920021223.do)

Rafael Manzoni - rafael.manzoni@gft.com
Team API & Integration Practice Brazil GFT Brazil
