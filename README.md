<p align="center">
  <a href="https://dev.wirecard.com.br/v2.0/">
    <img src="https://res.cloudinary.com/https-github-com-matmiranda-moipcsharp/image/upload/v1540216474/Wirecard%20png.png" alt="Wirecard logo" width=200>
  </a>
</p>
<p align="center">
    O jeito mais simples e rápido de integrar o Wirecard a sua aplicação .NET e feito com base nas APIs REST do Wirecard.
  <br>
  <br>
    <a href="https://dev.wirecard.com.br/v2.0/docs">
        <img src="https://img.shields.io/badge/Docs-Wirecard-lightgrey.svg"
            alt="Docs"></a>
    <a href="https://dev.wirecard.com.br/v2.0/reference">
        <img src="https://img.shields.io/badge/API%20Reference-Wirecard-darkblue.svg"
            alt="API Reference"></a>
    <a href="https://slackin-cqtchmfquq.now.sh/">
        <img src="https://img.shields.io/badge/Slack-Wirecard%20Devs-black.svg"
            alt="Slack"/></a>
    <a href="https://github.com/matmiranda/WirecardCSharp/blob/master/LICENSE">
        <img src="https://img.shields.io/badge/License-MIT-brightgreen.svg"
            alt="MIT"></a>
    <a href="https://www.nuget.org/packages/WirecardCSharp">
        <img src="https://img.shields.io/badge/Nuget-v2.0.5-blue.svg"
            alt="NuGet"></a>
</p>

## Índice
- [Implementações .NET com suporte](#implementações-net-com-suporte)
- [Aviso Importante](#aviso-importante)
- [Instalação](#instalação)
- [Autenticando e configurando o ambiente (E-Commerce)](#autenticando-e-configurando-o-ambiente-e-commerce)
- [Autenticando e configurando o ambiente (Marketplace)](#autenticando-e-configurando-o-ambiente-marketplace)
- [Assíncrona x Síncrona](#assíncrona-x-síncrona)
- [Conta Clássica](#conta-clássica)
- [Conta Transparente](#conta-transparente)
- [Clientes](#clientes)
- [Pedidos](#pedidos)
- [Pagamentos](#pagamentos)
- [Multipedidos](#multipedidos)
- [Multipagamentos](#multipagamentos)
- [Notificações](#notificações)
- [Contas Bancárias](#contas-bancárias)
- [Saldo Wirecard](#saldo-wirecard)
- [Lançamentos](#lançamentos)
- [Transferências](#transferências)
- [Reembolsos](#reembolsos)
- [Conciliação](#conciliação)
- [Assinatura](#assinatura)
- [Convertendo objeto para json](#convertendo-objeto-para-json)
- [Tabela - Filtros de busca](#tabela---filtros-de-busca)
- [Exceção](#exceção)
- [Licença](#licença)

## Implementações .NET com suporte
Essa biblioteca foi feito em (**.NET Standard 1.2 e VS2017**) e tem suporte das seguintes implementações do .NET:

* .NET Core 1.0 ou superior
* .NET Framework 4.5.1 ou superior
* Mono 4.6 ou superior
* Xamarin.iOS 10.0 ou superior
* Xamarin.Android 7.0 ou superior
* Universal Windows Platform 10 ou superior
* Windows 8.1 ou superior
* Windows Phone 8.1 ou superior

Para mais informações: [.NET Standard](https://docs.microsoft.com/pt-br/dotnet/standard/net-standard).

## Aviso Importante
Pensando em melhorar ainda mais a sua segurança e para atender a padrões internacionais do nosso selo PCI Compliance, o Wirecard desativará protocolos de segurança TLS (Transport Layer Security) inferiores a 1.2 à partir do dia 30/06/2018. Verifique se o seu projeto já possui TLS na versão 1.2, caso não, você receberá uma exceção:

```diff
- InnerException = {"A solicitação foi anulada: Não foi possível criar um canal seguro para SSL/TLS."}
```
Para isso, adicione o seguinte código no seu projeto:

```C#
System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```
Para mais informações : [TLS1.2](https://dev.wirecard.com.br/page/atualiza%C3%A7%C3%A3o-do-protocolo-de-seguran%C3%A7a-tls-12).

## Instalação
Execute o comando para instalar via [NuGet](https://www.nuget.org/packages/WirecardCSharp/):


```xml
PM> Install-Package WirecardCSharp
```

💥 **Obs**: Trocamos a biblioteca [MoipCSharp](https://www.nuget.org/packages/MoipCSharp/) por [WirecardCSharp](https://www.nuget.org/packages/WirecardCSharp/).

## Autenticando e configurando o ambiente (E-Commerce)
Escolha o "ambiente" você quer executar suas ações e informe seu (token, chave):

```C#
using WirecardCSharp;
using WirecardCSharp.Models;

private const string token = "xxxxxxxxxxxxxxxxxxx";
private const string key = "xxxxxxxxxxxxxxxxxxxxxxxxxx";
private WirecardClient WC = new WirecardClient(Environments.SANDBOX, token, key);
```
Para obter um token e a chave, primeiro faça o login [aqui](https://connect-sandbox.wirecard.com.br/login).

Você pode acessá-las em **Minha conta** > **Configurações** > **Chaves de Acesso**.

## Autenticando e configurando o ambiente (Marketplace)
Escolha o "ambiente" você quer executar suas ações e informe seu accesstoken: 
```C#
using WirecardCSharp;
using WirecardCSharp.Models;

private const string accessToken = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxx_v2";
private WirecardClient WC = new WirecardClient(Environments.SANDBOX, accessToken);
```

Para obter o accesstoken, você precisa criar um [App](https://dev.wirecard.com.br/reference#1-criar-um-app).

## Assíncrona x Síncrona
Todos os métodos são **assíncronos**, caso você queira executar de forma **síncrona**, veja o exemplo:

```C#
var result = Task.Run(() => WirecardClient.Customer.List()).Result;
```

## Conta Clássica
#### Verificar se usuário já possui Conta Wirecard (email)
🚩 Essa função funciona somente na conta clássica.
```C#
var result = await WirecardClient.ClassicAccount.AccountExist("meu_email@email.com");
if (result == HttpStatusCode.OK)
{
    // já existe
    //HttpStatusCode.OK == 200 (já existe)
    //HttpStatusCode.BadRequest == 400 (CPF inválido)
    //HttpStatusCode.NotFound == 404 (Para CPF válido, mas não possui Conta Wirecard)
}
```

#### Verificar se usuário já possui Conta Wirecard (documento)
🚩 Essa função funciona somente na conta clássica.
```C#
var result = await WirecardClient.ClassicAccount.AccountExist("123.456.789-01");
if (result == HttpStatusCode.OK)
{
    // já existe
    //HttpStatusCode.OK == 200 (já existe)
    //HttpStatusCode.BadRequest == 400 (CPF inválido)
    //HttpStatusCode.NotFound == 404 (Para CPF válido, mas não possui Conta Wirecard)
}
```

#### Criar Conta Wirecard Clássica (Conta PF)
```C#
var body = new ClassicAccountRequest
{
    Email = new Email
    {
        Address = "fulano@hotmail.com"
    },
    Person = new Person
    {
        Name = "Fulano",
        LastName = "da Silva",
        TaxDocument = new Taxdocument
        {
            Type = "CPF",
            Number = "123.456.789-91"
        },
        IdentityDocument = new Identitydocument
        {
            Type = "RG",
            Number = "434322344",
            Issuer = "SPP",
            IssueDate = "2000-12-12"
        },
        BirthDate = "1990-01-01",
        Phone = new Phone
        {
            CountryCode = "55",
            AreaCode = "11",
            Number = "965213244"
        },
        Address = new Address
        {
            Street = "Av. Brigadeiro Faria Lima",
            StreetNumber = "2927",
            District = "Itaim",
            ZipCode = "01234-000",
            City = "São Paulo",
            State = "SP",
            Country = "BR"
        }
    },
    Type = "MERCHANT"
};
var result = await WirecardClient.ClassicAccount.Create(body);
```
#### Criar Conta Wirecard Clássica (Conta PJ)
```C#
var body = new ClassicAccountRequest
{
    Email = new Email
    {
        Address = "fulano@hotmail.com"
    },
    Person = new Person
    {
        Name = "Fulano",
        LastName = "da Silva",
        BirthDate = "1990-01-01",
        TaxDocument = new Taxdocument
        {
            Type = "CPF",
            Number = "123.456.789-91"
        },
        IdentityDocument = new Identitydocument
        {
            Type = "RG",
            Number = "434322344",
            Issuer = "SPP",
            IssueDate = "2000-12-12"
        },
        Phone = new Phone
        {
            CountryCode = "55",
            AreaCode = "11",
            Number = "965213244"
        },
        Address = new Address
        {
            Street = "Av. Brigadeiro Faria Lima",
            StreetNumber = "2927",
            District = "Itaim",
            ZipCode = "01234-000",
            City = "São Paulo",
            State = "SP",
            Country = "BR"
        }
    },
    Company = new Company
    {
        Name = "Noma da empresa",
        BusinessName = "Wirecard Pagamentos",
        OpeningDate = "2011-01-01",
        TaxDocument = new Taxdocument
        {
            Type = "CNPJ",
            Number = "11.698.147/0001-13"
        },
        MainActivity = new Mainactivity
        {
            Cnae = "82.91-1/00",
            Description = "Atividades de cobranças e informações cadastrais"
        },
        Phone = new Phone
        {
            CountryCode = "55",
            AreaCode = "11",
            Number = "32234455"
        },
        Address = new Address
        {
            Street = "Av. Brigadeiro Faria Lima",
            StreetNumber = "2927",
            District = "Itaim",
            ZipCode = "01234-000",
            City = "São Paulo",
            State = "SP",
            Country = "BRA"
        }
    },
    BusinessSegment = new Businesssegment
    {
        Id = 3
    },
    Type = "MERCHANT"
};
var result = await WirecardClient.ClassicAccount.Create(body);
```

#### Consultar Conta Wirecard
```C#
var result = await WirecardClient.ClassicAccount.Consult("MPA-XXXXXXXXXXXX");
```

#### Solicitar Permissões de Acesso ao Usuário

🚩 O código a seguir não consome API, apenas monta o URL. Mais informações clica [aqui](https://dev.wirecard.com.br/reference#section-como-funciona-a-permiss%C3%A3o).

```C#
string response_type = "code";
string client_id = "APP-FFFGVQMOK123";
string redirect_uri = "https://example.com/abc?DEF=あいう\x20えお";
string scope = "RECEIVE_FUNDS,MANAGE_ACCOUNT_INFO,DEFINE_PREFERENCES";
var url = Utilities.RequestUserAccessPermissions(response_type, client_id, redirect_uri, scope);

//https://connect-sandbox.moip.com.br/oauth/authorize?response_type=code&client_id=APP-
//FFFGVQMOK123&redirect_uri=https://example.com/abc?DEF=%E3%81%82%E3%81%84%E3%81%86%20%
//E3%81%88%E3%81%8A&scope=RECEIVE_FUNDS,MANAGE_ACCOUNT_INFO,DEFINE_PREFERENCES
```

Veja [aqui](https://dev.wirecard.com.br/reference#section-como-funciona-a-permiss%C3%A3o) como funciona a permissão.

#### Gerar Access Token
```C#
string client_id = "APP-M11STAPPOAU";
string client_secret = "SplxlOBeZQQYbYS6WxSbIA";
string redirect_uri = "http://localhost/moip/callback";
string grant_type = "authorization_code";
string code = "4d9e0466bc14aad85b894237145b217219e9a825";
var result = await WirecardClient.ClassicAccount.GenerateAccessToken(
                client_id, client_secret, redirect_uri, grant_type, code);
```

#### Atualizar accessToken
```C#
string grant_type = "refresh_token";
string refresh_token = "2381dfbbcbd645268af1dd0e4456bfe1_v2";
var result = await WC.ClassicAccount.UpdateAccessToken(grant_type, refresh_token);
```

#### Obter chave pública de uma Conta Wirecard
```C#
var result = await WC.ClassicAccount.GetPublickey();
```

## Conta Transparente
#### Criar Conta Wirecard Transparente
```C#
var body = new TransparentAccountRequest
{
    TransparentAccount = true,
    Type = "MERCHANT",
    Email = new Email
    {
        Address = "teste@hotmail.com"
    },
    Person = new Person
    {
        Name = "PrimeiroNome",
        LastName = "SegundoNome",
        TaxDocument = new Taxdocument
        {
            Type = "CPF",
            Number = "123.456.798-91"
        },
        BirthDate = "2011-01-01",
        Phone = new Phone
        {
            CountryCode = "55",
            AreaCode = "11",
            Number = "965213244"
        },
        Address = new Address
        {
            Street = "Av. Brigadeiro Faria Lima",
            StreetNumber = "2927",
            District = "Itaim",
            ZipCode = "01234000",
            City = "Osasco",
            State = "SP",
            Country = "BRA"
        }
    }
};
var result = await WC.TransparentAccount.Create(body);
```

## Clientes
#### Criar Cliente
```C#
var body = new CustomerRequest
{
    //informe os campos aqui
};
var result = await WC.Customer.Create(body);
```

#### Adicionar Cartão de Crédito
```C#
var body = new CustomerRequest
{
    Method = "CREDIT_CARD",
    CreditCard = new Creditcard
    {
        ExpirationMonth = "06",
        ExpirationYear = "2022",
        Number = "4012001037141112",
        Cvc = "123",
        Holder = new Holder
        {
            FullName = "João da Silva",
            BirthDate = "1961-03-01",
            TaxDocument = new Taxdocument
            {
                Type = "CPF",
                Number = "11111111111"
            },
            Phone = new Phone
            {
                CountryCode = "55",
                AreaCode = "11",
                Number = "111111111"
            }
        }
    }
};
var result = await WC.Customer.AddCreditCard(body, "CUS-XXXXXXXXXXXX");
```

#### Deletar Cartão de Crédito
```C#
var result = await WC.Customer.DeleteCreditCard("CRC-XXXXXXXXXXXX");
```

#### Consultar Cliente
```C#
var result = await WC.Customer.Consult("CUS-XXXXXXXXXXXX");
```

#### Listar Todos os Clientes
```C#
var result = await WC.Customer.List();
```

## Pedidos
#### Criar Pedido
```C#
var body = new OrderRequest
{
    //informe os campos aqui
};
var result = await WC.Order.Create(body);
```

#### Consultar Pedido
```C#
var result = await WirecardClient.Order.Consult("ORD-XXXXXXXXXXXX");
```

#### Listar Todos os Pedidos - Sem Filtros
```C#
var result = await WirecardClient.Order.List();
```

#### Listar Todos os Pedidos - Com Filtros
```C#
string filtros = "q=josesilva&filters=status::in(PAID,WAITING)|paymentMethod::in(CREDIT_CARD,BOLETO)|value::bt(5000,10000)&limit=3&offset=0";
var result = await WirecardClient.Order.ListFilter(filtros);
```
 Veja a tabela filtros de busca [aqui](#tabela---filtros-de-busca).
 
 ## Pagamentos
 #### Criar Pagamento - Cartão de Crédito
 ```C#
var body = new PaymentRequest
{
    //informe os campos aqui
    InstallmentCount = 1,
    FundingInstrument = new Fundinginstrument
    {
        Method = "CREDIT_CARD",
        CreditCard = new Creditcard
        {
            Id = "CRC-XXXXXXXXXXXX",
            Cvc = "123",
            Holder = new Holder
            {
                FullName = "Jose Portador da Silva",
                BirthDate = "1988-12-30",
                TaxDocument = new Taxdocument
                {
                    Type = "CPF",
                    Number = "33333333333"
                }
            }
        }
    }
};          
var result = await WirecardClient.Payment.Create(body, "ORD-XXXXXXXXXXXX");
```

#### Criar Pagamento - Boleto

```C#
var body = new PaymentRequest
{
    //informe os campos aqui
    StatementDescriptor = "Minha Loja",
    FundingInstrument = new Fundinginstrument
    {
        Method = "BOLETO",
        Boleto = new Boleto
        {
            ExpirationDate = "2020-06-20", //yyyy-MM-dd
            InstructionLines = new Instructionlines
            {
                First = "Atenção",
                Second = "fique atento à data de vencimento do boleto.",
                Third = "Pague em qualquer casa lotérica."
            }
        }        
    }
};
var result = await WirecardClient.Payment.Create(body, "ORD-XXXXXXXXXXXX");
```

#### Liberação de Custódia
```C#
var result = await WirecardClient.Payment.ReleaseCustody("ECW-XXXXXXXXXXXX");
```

#### Capturar Pagamento Pré-autorizado
```C#
var result = await WirecardClient.Payment.CaptureAuthorized("PAY-XXXXXXXXXXXX");
```

#### Cancelar Pagamento Pré-autorizado
```C#
var result = await WirecardClient.Payment.CancelAuthorized("PAY-XXXXXXXXXXXX");
```

#### Consultar Pagamento
```C#
var result = await WirecardClient.Payment.Consult("PAY-XXXXXXXXXXXX");
```

#### Simular Pagamentos (sandbox)
```C#
var result = await WirecardClient.Payment.Simulate("PAY-XXXXXXXXXXXX", 26500);
```

## Multipedidos
#### Criar Multipedido
```C#
var body = new MultiOrderRequest
{
    //informe os campos aqui
};            
var result = await WirecardClient.MultiOrder.Create(body);
```

#### Consultar Multipedido
```C#
var result = await WirecardClient.MultiOrder.Consult("MOR-XXXXXXXXXXXX");
```

## Multipagamentos
#### Criar Multipagamento
```C#
var body = new MultiPaymentRequest
{
    //informe os campos aqui
};            
var result = await WirecardClient.MultiPayment.Create(body, "MOR-XXXXXXXXXXXX");
```
#### Consultar Multipagamento
```C#
var result = await WirecardClient.MultiPayment.Consult("MPY-XXXXXXXXXXXX");
```
#### Capturar Multipagamento Pré-autorizado
```C#
var result = await WirecardClient.MultiPayment.CaptureAuthorized("MPY-XXXXXXXXXXXX");
```
#### Cancelar Multipagamento Pré-autorizado
```C#
var result = await WirecardClient.MultiPayment.CancelAuthorized("MPY-XXXXXXXXXXXX");
```
#### Liberação de Custódia
```C#
var result = await WirecardClient.MultiPayment.ReleaseCustody("ECW-XXXXXXXXXXXX");
```
## Notificações
#### Criar Preferência de Notificação para Conta Wirecard
```C#
var body = new NotificationRequest
{
    //informe os campos aqui
};            
var result = await WirecardClient.Notification.CreatAccountWirecard(body);
```

#### Criar Preferência de Notificação para App
Caso não tenha uma URL disponível, você pode usar o **Webhook Tester** para fazer seus testes e receber os webhooks. 

Para isso basta acessar o [site](https://webhook.site) e gera uma URL automaticamente.

```C#
var body = new NotificationRequest
{
    Events = new List<string> { "ORDER.*" },
    Target = "https://webhook.site/a54daf-da54-8d5a-8d5d1-kfa4gahf42",
    Media = "WEBHOOK"
};           
var result = await WirecardClient.Notification.CreateApp(body);
```
#### Criar Preferência de Notificação para App com código identificador
```C#
var body = new NotificationRequest
{
    Events = new List<string> { "ORDER.*" },
    Target = "https://webhook.site/a54daf-da54-8d5a-8d5d1-kfa4gahf42",
    Media = "WEBHOOK"
};           
var result = await WirecardClient.Notification.CreateApp(body, "APP-3984HG73HE9");
```
#### Consultar Preferência de Notificação
```C#
var result = await WirecardClient.Notification.Consult("NPR-XXXXXXXXXXXX");
```
#### Listar Todas as Preferências de Notificação
```C#
var result = await WirecardClient.Notification.List();
```
#### Remover Preferência de Notificação
```C#
var result = await WirecardClient.Notification.Remove("NPR-XXXXXXXXXXXX");
if (result == HttpStatusCode.NoContent)
{
    // Caso a Preferência de Notificação tenha sido deletada, você deve receber uma response vazia (NoContent)
}
```
#### Consultar Webhook Enviado
```C#
var result = await WirecardClient.Notification.ConsultWebhook("PAY-XXXXXXXXXXXX"); 
```
#### Listar Todos os Webhooks Enviados
```C#
var result = await WirecardClient.Notification.ListWebhooks();
```
#### Desserializar WebHook
Ao configurar suas Preferências de Notificação você deve receber os webhooks em formato JSON e você pode desserializar.

```C#
var json = "{ \"date\": \"\", \"env\": \"\", ... }";
var result = Utilities.DeserializeWebHook(json);
```
Veja um exemplo do webhook [aqui](https://gist.githubusercontent.com/matmiranda/61b8fac6159d0a61c1cd52deb0941fd8/raw/c08a41818abd135d56c7608587f353bc0bd99df7/Exemplo%2520WebHook.json).

Para aumentar a segurança da sua aplicação e garantir que apenas a Wirecard pode enviar notificações para o seu sistema, você pode conferir o token enviado no header dos webhooks. Este token é o mesmo que é gerado no momento do cadastro da sua URL:
```C#
var token = Request.Headers["Authorization"];
```

## Contas Bancárias
#### Criar Conta Bancária
```C#
var body = new BankAccountRequest
{
    bankNumber = "237",
    agencyNumber = "12345",
    agencyCheckNumber = "0",
    accountNumber = "12345678",
    accountCheckNumber = "7",
    type = "CHECKING",
    holder = new Holder
    {
        taxDocument = new Taxdocument
        {
            type = "CPF",
            number = "622.134.533-22"
        },
        fullname = "Demo Wirecard"
    }
};
string accesstoken = "XXXXXXXXXXXXXXXXXXXXXXXXXXX_v2"; // accesstoken do recebedor
var result = await WirecardClient.BankAccount.Create(body, accesstoken, "MPA-XXXXXXXXXXXX");
```
#### Consultar Conta Bancária
```C#
string accesstoken = "XXXXXXXXXXXXXXXXXXXXXXXXXXX_v2"; // accesstoken do recebedor
var result = await WirecardClient.BankAccount.Consult(accesstoken, "BKA-XXXXXXXXXXXX");
```
#### Listar Todas Contas Bancárias
```C#
string accesstoken = "XXXXXXXXXXXXXXXXXXXXXXXXXXX_v2"; // accesstoken do recebedor
var result = await WirecardClient.BankAccount.List(accesstoken, "MPA-XXXXXXXXXXXX");
```
#### Deletar Conta Bancária
```C#
string accesstoken = "XXXXXXXXXXXXXXXXXXXXXXXXXXX_v2"; // accesstoken do recebedor
var result = await WirecardClient.BankAccount.Delete(accesstoken, "BKA-XXXXXXXXXXXX");
```
#### Atualizar Conta Bancária
```C#
var body = new BankAccountRequest
{
    //informe os campos aqui
};
string accesstoken = "XXXXXXXXXXXXXXXXXXXXXXXXXXX_v2"; // accesstoken do recebedor
var result = await WirecardClient.BankAccount.Update(body, accesstoken, "BKA-XXXXXXXXXXXX");
```
## Saldo Wirecard
#### Consultar Saldos
```C#
var result = await WirecardClient.Balance.Consult();
```
## Lançamentos
#### Consultar Lançamento
```C#
var result = await WirecardClient.Launch.Consult("ENT-XXXXXXXXXXXX");
```
#### Listar Todos Lançamentos
```C#
var result = await WirecardClient.Launch.List();
```
#### Listar Todos Lançamentos com Filtro
```C#
string filtros = "filters=status::in(SETTLED)";
var result = await WirecardClient.Launch.ListFilter(filtros);
```
## Transferências
#### Criar Transferência
```C#
var body = new TransferRequest
{
    //informe os campos aqui
};
string accessToken = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxx_v2";
var result = await WirecardClient.Transfer.Create(body, accessToken);
```
#### Reverter Transferência
```C#
string accessToken = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxx_v2";
var result = await WirecardClient.Transfer.Revert("TRA-XXXXXXXXXXXX", accessToken);
```
#### Consultar Transferência
```C#
string accessToken = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxx_v2";
var result = await WirecardClient.Transfer.Consult("TRA-XXXXXXXXXXXX", accessToken);
```
#### Listar Todas Transferências
```C#
var result = await WirecardClient.Transfer.List();
```
#### Listar Todas Transferências com filtros
```C#
string filtros = "filters=transferInstrument.method::in(MOIP_ACCOUNT)&limit=3&offset=0";
var result = await WirecardClient.Transfer.List();
```
## Reembolsos
#### Reembolsar Pagamento
```C#
var body = new RefundRequest
{
    //informe os campos aqui
};            
var result = await WirecardClient.Refund.RefundPayment(body, "PAY-XXXXXXXXXXXX");
```
#### Reembolsar Pedido via Cartão de Crédito
```C#
var body = new RefundRequest
{
    //informe os campos aqui
};            
var result = await WirecardClient.Refund.RefundRequestCreditCard(body, "ORD-XXXXXXXXXXXX");
```
#### Consultar Reembolso
```C#
var result = await WirecardClient.Refund.Consult("REF-XXXXXXXXXXXX");
```
#### Listar Reembolsos do Pagamento
```C#
var result = await WirecardClient.Refund.ListPayments("PAY-XXXXXXXXXXXX");
```
#### Listar Reembolsos do Pedido
```C#
var result = await WirecardClient.Refund.ListOrders("ORD-XXXXXXXXXXXX");
```
## Conciliação
#### Obter Arquivo de Vendas
```C#
var result = await WirecardClient.Conciliation.GetSalesFile("20180829"); // Data no formato YYYYMMDD
```
#### Obter Arquivo Financeiro
```C#
var result = await WirecardClient.Conciliation.GetFinancialFile("2018-08-29"); // Data no formato YYYY-MM-DD
```
## Assinatura
#### Criar Plano
```C#
var body = new PlanRequest
{
    Code = "plan103",
    Name = "Plano Especial",
    Description = "Descrição do Plano Especial",
    Amount = 990,
    Setup_Fee = 500,
    Max_Qty = 1,
    Interval = new Interval
    {
        Length = 1,
        Unit = "MONTH"
    },
    Billing_Cycles = 12,
    Trial = new Trial
    {
        Days = 30,
        Enabled = true,
        Hold_Setup_Fee = true
    }
};
var result = await WirecardClient.Signature.CreatePlan(body);
```
#### Listar Planos
```C#
var result = await WirecardClient.Signature.ListPlans();
```
#### Consultar Plano
```C#
var result = await WirecardClient.Signature.ConsultPlan("plan101");
```
#### Ativar Plano
```C#
var result = await WirecardClient.Signature.EnablePlan("plan101");
```
#### Desativar Plano
```C#
var result = await WirecardClient.Signature.DisablePlan("plan101");
```
#### Alterar Plano
```C#
var body = new PlanRequest
{
    Name = "Plano Especial",
    Description = "",
    Amount = 1290,
    Setup_Fee = 800,
    Max_Qty = 1,
    Payment_Method = "CREDIT_CARD",
    Interval = new Interval
    {
        Length = 1,
        Unit = "MONTH"
    },
    Billing_Cycles = 12,
    Trial = new Trial
    {
        Days = 30,
        Enabled = true,
        Hold_Setup_Fee = true
    }
};
var result = await WirecardClient.Signature.ChangePlan(body, "plan101");
```
#### Criar Assinante
```C#
var body = new SubscriberRequest
{
    Code = "cliente03",
    Email = "nome@exemplo.com.br",
    FullName = "Nome Sobrenome",
    Cpf = "22222222222",
    Phone_Area_Code = "11",
    Phone_Number = "934343434",
    BirthDate_Day = "26",
    BirthDate_Month = "04",
    BirthDate_Year = "1980",
    Address = new Address
    {
        Street = "Rua Nome da Rua",
        StreetNumber = "100",
        Complement = "casa",
        District = "Nome do Bairro",
        City = "São Paulo",
        State = "SP",
        Country = "BRA",
        ZipCode = "05015010"
    },
    Billing_Info = new Billing_Info
    {
        Credit_Card = new Credit_Card
        {
            Holder_Name = "Nome Completo",
            Number = "4111111111111111",
            Expiration_Month = "06",
            Expiration_Year = "22"
        }
    }
};
var result = await WirecardClient.Signature.CreateSubscriber(body, true);
```
#### Listar Assinantes
```C#
var result = await WirecardClient.Signature.ListSubscribers();
```
#### Consultar Assinante
```C#
var result = await WirecardClient.Signature.ConsultSubscriber("cliente01");
```
#### Alterar Assinante
```C#
var body = new SubscriberRequest
{
    Code = "cliente01",
    Email = "nome@exemplo.com.br",
    FullName = "Nome Sobrenome",
    Cpf = "22222222222",
    Phone_Area_Code = "11",
    Phone_Number = "934343434",
    BirthDate_Day = "26",
    BirthDate_Month = "04",
    BirthDate_Year = "1980",
    Address = new Address
    {
        Street = "Rua Nome da Rua1",
        StreetNumber = "100",
        Complement = "casa",
        District = "Nome do Bairro",
        City = "São Paulo",
        State = "SP",
        Country = "BRA",
        ZipCode = "05015010"
    }
};
var result = await WirecardClient.Signature.ChangeSubscriber(body, "cliente01");
```
#### Atualizar Cartão do Assinante
```C#
var body = new SubscriberRequest
{
    Billing_Info = new Billing_Info
    {
        Credit_Card = new Credit_Card
        {
            Holder_Name = "Novo nome222",
            Number = "5555666677778884",
            Expiration_Month = "12",
            Expiration_Year = "20"
        }
    }
};
var result = await WirecardClient.Signature.UpdateSubscriberCard(body, "cliente01");
```
#### Criar Assinaturas
```C#
var body = new SubscriptionRequest
{
    Code = "assinatura04",
    Amount = "9000",
    Plan = new Plan
    {
        Code = "plan101"
    },
    Payment_Method = "CREDIT_CARD",
    Customer = new Customer
    {
        Code = "cliente01",
    }
};
var result = await WirecardClient.Signature.CreateSubscriptions(body, false);
```
#### Listar Todas Assinaturas
```C#
var result = await WirecardClient.Signature.ListAllSubscriptions();
```
#### Consultar Assinatura -Sem Filtro
```C#
var result = await WirecardClient.Signature.ConsultSubscriptionFilter("assinatura01");
```
#### Consultar Assinatura - Com Filtro
```C#
var filter = "q=assinatura01&filters=status::eq(ACTIVE)";
var result = await WirecardClient.Signature.ConsultSubscription(filter);
```
Alguns exemplos de como filtrar:

1. Pesquisar e Filtrar assinaturas (``` q=teste&filters=status::eq(EXPIRED) ```)
2. Filtrar assinaturas por status (``` filters=status::eq(EXPIRED)&limit=10&offset=0 ```)
3. Filtrar assinaturas por creation_date (``` filters=creation_date::bt(2014-11-08,2015-05-07)&limit=100&offset=0 ```)
4. Filtrar assinaturas por next_invoice_date (``` filters=next_invoice_date::bt(2015-10-12,2015-10-12)&limit=100&offset=0 ```)
5. Filtrar assinaturas por plano (``` filters=plan.code::eq(TESTE_WIRECARD)&limit=100&offset=0 ```)
6. Filtrar assinaturas por customer.code (``` filters=customer.code::eq(HHDGOo)&limit=100&offset=0 ```)
7. Filtrar assinaturas por customer.email (``` filters=customer.email::eq(joao.silva@email.com.br)&limit=100&offset=0 ```)
8. Filtrar assinaturas por customer.cpf (``` filters=customer.cpf::eq(22222222222)&limit=100&offset=0 ```)
9. Filtrar assinaturas por valor (``` filters=amount::bt(100,100000) ```)
10. Pesquisar Assinatura (``` q=diego nunes&limit=10&offset=0 ```)
#### Suspender Assinatura
```C#
var result = await WirecardClient.Signature.SuspendSubscription("assinatura01");
```
#### Reativar Assinatura
```C#
var result = await WirecardClient.Signature.ReactivateSignature("assinatura01");
```
#### Cancelar Assinatura
```C#
var result = await WirecardClient.Signature.CancelSignature("assinatura01");
```
#### Alterar Assinatura
```C#
var body = new SubscriptionRequest
{
    Plan = new Plan
    {
        Code = "plan101"
    },
    Amount = "9990",
    Next_Invoice_Date = new Next_Invoice_Date
    {
        Day = 15,
        Month = 12,
        Year = 2018
    }
};
var result = await WirecardClient.Signature.ChangeSubscription(body, "assinatura01");
```
#### Alterar método de pagamento
```C#
var body = new SubscriptionRequest
{
    Payment_Method = "BOLETO"
};
var result = await WirecardClient.Signature.ChangePaymentMethod(body, "assinatura01");
```
#### Listar Todas as Faturas de Uma Assinatura
```C#
var result = await WirecardClient.Signature.ListSignatureInvoices("assinatura01");
```
#### Consultar Fatura
```C#
var result = await WirecardClient.Signature.ConsultInvoice("10865746");
```
#### Listar todos os pagamentos de fatura
```C#
var result = await WirecardClient.Signature.ListAllInvoicePayments("10865746");
```
#### Consultar pagamento de assinatura
```C#
var result = await WirecardClient.Signature.ConsultSubscriptionPayment("PAY-123456789012");
```
#### Criar Cupom
```C#
var body = new CouponRequest
{
    Code = "coupon-0002",
    Name = "Coupon name",
    Description = "My new coupon",
    Discount = new Discount
    {
        Value = 1000,
        Type = "percent"
    },
    Status = "active",
    Duration = new Duration
    {
        Type = "repeating",
        Occurrences = 12
    },
    Max_Redemptions = 100,
    Expiration_Date = new Expiration_Date
    {
        Year = 2020,
        Month = 08,
        Day = 01    
    }
};
var result = await WirecardClient.Signature.CreateCoupon(body);
```
#### Associar Cupom para Assinatura
```C#
var body = new CouponRequest
{
    Coupon = new Coupon
    {
        Code = "coupon-0001"
    }
};
var result = await WirecardClient.Signature.AssociateCouponForExistingSignature(body, "assinatura01");
```
#### Associar Cupom para Nova Assinatura
```C#
var body = new CouponRequest
{
    //informar os campos
};
var result = await WirecardClient.Signature.AssociateCouponForExistingSignature(body, "true");
```
#### Consultar Cupom
```C#
var result = await WirecardClient.Signature.ConsultCoupon("coupon-0001");
```
#### Listar Todos os Cupons
```C#
var result = await WirecardClient.Signature.ListAllCoupons();
```
#### Ativar e Inativar Cupons
```C#
var result = await WirecardClient.Signature.EnableOrDisableCoupon("coupon-0001", "inactive");
```
#### Excluir Cupom de uma Assinatura
```C#
var result = await WirecardClient.Signature.DeleteSignatureCoupon("assinatura01");
```
#### Retentativa de pagamento de uma fatura
```C#
var result = await WirecardClient.Signature.RetentiveInvoicePayment("1548222");
```
#### Gerar um novo boleto para uma fatura
```C#
var body = new RetentativeRequest
{
    Year = 2020,
    Month = 08,
    Day = 01
};
var result = await WirecardClient.Signature.CreateNewInvoiceBoleto(body,"1548222");
```
#### Criar Regras de Retentativas Automáticas
```C#
var body = new RetentativeRequest
{
    First_Try = 1,
    Second_Try = 3,
    Third_Try = 5,
    Finally = "cancel"
};
var result = await WirecardClient.Signature.CreateAutomaticRetentionRules(body);
```

#### Criar Preferência de Notificação - Em Desenvolvimento...
```C#
var body = new NotificationRequest
{
    Notification = new Notification
    {
        Webhook = new Webhook
        {
            Url = "http://exemploldeurl.com.br/assinaturas"
        },
        Email = new Email
        {
            Merchant = new Merchant
            {
                Enabled = true
            },
            Customer = new Customer
            {
                Enabled = true
            }
        }
    }
};
var result = await WirecardClient.Signature.CreateNotificationPreference(body);
```

## Convertendo objeto para json

As vezes você enfrenta um problema e o suporte Wirecard pede o código json para verificar se realmente está no json:

```C#
using Newtonsoft.Json;

var body = new PaymentRequest
{
    //informe os campos aqui
    DelayCapture = true,
    InstallmentCount = 1,
    FundingInstrument = new Fundinginstrument
    {
        Method = "CREDIT_CARD",
        CreditCard = new Creditcard
        {
            Id = "CRC-XXXXXXXXXXXX",
            Cvc = "123",
            Holder = new Holder
            {
                FullName = "Jose Portador da Silva",
                BirthDate = "1988-12-30",
                TaxDocument = new Taxdocument
                {
                    Type = "CPF",
                    Number = "33333333333"
                }
            }
        }
    }
};

//Aqui você pode obter json e compratilhar para suporte Wirecard
string json = JsonConvert.SerializeObject(body, Formatting.Indented);
```

Veja como ficou na variável json:

```json
{
  "installmentCount": 1,
  "delayCapture": true,
  "fundingInstrument": {
    "method": "CREDIT_CARD",
    "creditCard": {
      "id": "CRC-XXXXXXXXXXXX",
      "cvc": "123",
      "holder": {
        "fullname": "Jose Portador da Silva",
        "birthdate": "1988-12-30",
        "taxDocument": {
          "type": "CPF",
          "number": "33333333333"
        }
      }
    }
  }
}
```

## Tabela - Filtros de busca

| Nome  | Tipo | Descrição |
| ------------- | ------------- | ------------- |
| limit  | int  | Quantidade de registros por busca (página). O valor default é 20 |
| offset  | int | Registro a partir do qual a busca vai retornar. O valor default é 0 |
| gt(x)  | number or date | Maior que - “Greater Than” |
| ge(x)  | number or date | Maior ou igual - “Greater than or Equal” |
| lt(x)  | number or date | Menor que - “Less Than” |
| le(x)  | number or date | Menor ou igual - “Less than or Equal” |
| bt(x,y)  | string | Entre - “Between” |
| in(x,y…z)  | string | Em - “IN” |
| q  |  | Consulta um valor em específico |

✅ Fazendo uma busca com os seguintes requisitos:

```diff
+ Transações de valores entre 5000 e 10000 (em centavos);
+ Formas de pagamento: Cartão de Crédito e Boleto;
+ Cliente com o nome jose silva;
+ Retornando 3 resultados.
```

> GET https: //sandbox.moip.com.br/v2/orders?q=jose silva
&filters=status::in(PAID,WAITING)|paymentMethod::in(CREDIT_CARD,BOLETO)
|value::bt(5000,10000)&limit=3&offset=0

Você pode também fazer uma busca por pedidos dentro de um intervalo de tempo:

> GET https: //sandbox.moip.com.br/v2/orders?filters=createdAt::bt(2017-10-10T13:07:00Z,2017-10-25T13:08:00Z)

## Exceção
#### Obter erros
Você pode recuperar os atributos `code`, `path`, `description`, `message` e `error`, veja no exemplo abaixo:
```C#
using WirecardCSharp.Exception;

try
{
    var result = await WirecardClient.Customer.Create(new CustomerRequest());
}
catch (WirecardException ex)
{
    var t = ex.wirecardError;
    var t_text = ex.GetExceptionText();
} 
```

#### Tabela de erros

| Nome  | Descrição | Detalhe |
| ------------- | ------------- | ------------- |
| code  | Código identificador do erro  | string |
| path  | Parâmetro relacionado ao erro | string |
| description  | Descrição do erro | string |
| message  | Mensagem do retorno Wirecard  | string |



## Licença

[The MIT License](https://github.com/matmiranda/WirecardCSharp/blob/master/LICENSE)

Tem dúvidas? Fale com a gente no [Slack](https://slackin-cqtchmfquq.now.sh/)!
Algum problema ? Abre issues!
