---
title: "Estado de sessão e de aplicativo no núcleo do ASP.NET"
author: rick-anderson
description: "Abordagens para preservar de aplicativo e o estado do usuário (sessão) entre as solicitações."
keywords: "Postagem do ASP.NET Core, o estado do aplicativo, estado de sessão, querystring,"
ms.author: riande
manager: wpickett
ms.date: 11/27/2017
ms.topic: article
ms.assetid: 18cda488-0769-4cb9-82f6-4c6685f2045d
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/app-state
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 35b34f1a40e431e59e6b9c1d9bfb4ce3fced35e6
ms.sourcegitcommit: 8f42ab93402c1b8044815e1e48d0bb84c81f8b59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="introduction-to-session-and-application-state-in-aspnet-core"></a>Introdução ao estado de sessão e de aplicativo no núcleo do ASP.NET

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), e [Diana LaRose](https://github.com/DianaLaRose)

HTTP é um protocolo sem monitoração de estado. Um servidor web trata cada solicitação HTTP como uma solicitação independente e não manter valores de usuário de solicitações anteriores. Este artigo aborda diferentes maneiras de preservar o estado da sessão entre as solicitações e aplicativo. 

## <a name="session-state"></a>Estado da sessão

O estado de sessão é um recurso do ASP.NET Core que você pode usar para salvar e armazenar dados de usuário enquanto o usuário navega seu aplicativo Web. Estado de sessão consiste em uma tabela de hash ou dicionário no servidor, persiste dados em solicitações de um navegador. Os dados da sessão são apoiados por um cache.

ASP.NET Core mantém o estado de sessão, fornecendo um cookie que contém a ID de sessão, que é enviada para o servidor com cada solicitação de cliente. O servidor usa a ID de sessão para buscar os dados da sessão. Como o cookie de sessão é específico para o navegador, você não pode compartilhar sessões entre navegadores. Cookies de sessão são excluídos somente quando termina a sessão do navegador. Se um cookie for recebido de uma sessão expirada, uma nova sessão que usa o mesmo cookie de sessão é criada. 

O servidor mantém uma sessão por um tempo limitado, após a última solicitação. Você pode definir o tempo limite da sessão ou use o valor padrão de 20 minutos. Estado da sessão é ideal para armazenar dados de usuário que são específico para uma sessão específica, mas não precisam ser mantidos permanentemente. Dados são excluídos do armazenamento de backup ou quando você chamar `Session.Clear` ou quando a sessão expira no repositório de dados. O servidor não sabe quando o navegador for fechado ou quando o cookie de sessão é excluído.

> [!WARNING]
> Não armazene dados confidenciais na sessão. O cliente não pode fechar o navegador e limpar o cookie de sessão (e alguns navegadores atividade cookies de sessão em windows). Além disso, uma sessão não pode ser restrita a um único usuário; o próximo usuário pode continuar com a mesma sessão.

O provedor de sessão na memória armazena dados de sessão no servidor local. Se você planeja executar seu aplicativo web em um farm de servidores, você deve usar sessões Autoadesivas para ligar cada sessão para um servidor específico. A plataforma Windows Azure Web Sites padrão sessões Autoadesivas (Application Request Routing ou ARR). Entretanto, sessões Autoadesivas podem afetar a escalabilidade e complicar as atualizações de aplicativo web. Uma opção melhor é usar o Redis ou distribuídas do SQL Server armazena em cache, que não exigem sessões Autoadesivas. Para obter mais informações, consulte [trabalhando com um Cache distribuído](xref:performance/caching/distributed). Para obter detalhes sobre como configurar provedores de serviços, consulte [sessão Configurando](#configuring-session) posteriormente neste artigo.

<a name="temp"></a>
## <a name="tempdata"></a>TempData

ASP.NET MVC de núcleo expõe o [TempData](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) propriedade em uma [controlador](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.controller?view=aspnetcore-2.0). Essa propriedade armazena dados até eles serem lidos. Os métodos `Keep` e `Peek` podem ser usados para examinar os dados sem exclusão. `TempData`é particularmente útil para redirecionamento, quando dados são necessários para mais de uma única solicitação. `TempData`é implementado por provedores de TempData, por exemplo, usar cookies ou estado de sessão.

<a name="tempdata-providers"></a>
### <a name="tempdata-providers"></a>Provedores de TempData

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

No ASP.NET Core 2.0 e posterior, o provedor de TempData baseada em cookie é usado por padrão para armazenar TempData em cookies.

Os dados do cookie são codificados com o [Base64UrlTextEncoder](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder?view=aspnetcore-2.0). Porque o cookie é criptografado e em partes, o cookie único tamanho limite encontrado no núcleo do ASP.NET 1. x não se aplica. Os dados do cookie não são compactados porque a compactação de dados criptografados pode levar a problemas de segurança, como o [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) e [violação](https://wikipedia.org/wiki/BREACH_(security_exploit)) ataques. Para obter mais informações sobre o provedor de TempData baseada em cookie, consulte [CookieTempDataProvider](https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc.ViewFeatures/ViewFeatures/CookieTempDataProvider.cs).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

No ASP.NET Core 1.0 e 1.1, o provedor de TempData de estado de sessão é o padrão.

--------------

<a name="choose-temp"></a>
### <a name="choosing-a-tempdata-provider"></a>Escolha de um provedor de TempData

Escolha de um provedor de TempData envolve várias considerações, como:

1. O aplicativo já usa o estado da sessão para outras finalidades? Nesse caso, usar o provedor de TempData de estado de sessão tem sem custo adicional para o aplicativo (além do tamanho dos dados).
2. O aplicativo usa TempData somente com moderação, para quantidades relativamente pequenas de dados (até 500 bytes)? Se assim, o provedor de TempData cookie adicionará um pequeno custo para cada solicitação que transporta TempData. Caso contrário, o provedor de TempData de estado de sessão pode ser útil evitar o ciclo uma grande quantidade de dados em cada solicitação até que o TempData é consumido.
3. O aplicativo é executado em um farm da web (vários servidores)? Nesse caso, não há nenhuma configuração adicional necessária para usar o provedor de TempData do cookie.

> [!NOTE]
> A maioria dos clientes da web (como navegadores da web) impõem limites sobre o tamanho máximo de cada cookie, o número total de cookies ou ambos. Portanto, ao usar o provedor de TempData cookies, verifique se que o aplicativo não excede esses limites. Considere o tamanho total dos dados, considerando as sobrecargas de criptografia e agrupamento.

<a name="config-temp"></a>
### <a name="configure-the-tempdata-provider"></a>Configurar o provedor de TempData

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

O provedor de TempData baseada em cookie é habilitado por padrão. O seguinte `Startup` código da classe configura o provedor de TempData baseadas em sessão:

[!code-csharp[](app-state/sample/src/WebAppSessionDotNetCore2.0App/StartupTempDataSession.cs?name=snippet_TempDataSession&highlight=4,6,11)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

O seguinte `Startup` código da classe configura o provedor de TempData baseadas em sessão:

[!code-csharp[](app-state/sample/src/WebAppSession/StartupTempDataSession.cs?name=snippet_TempDataSession&highlight=4,9)]

---

A ordem é essencial para componentes de middleware. No exemplo anterior, uma exceção do tipo `InvalidOperationException` ocorre quando `UseSession` é invocado após `UseMvcWithDefaultRoute`. Consulte [ordenação de Middleware](xref:fundamentals/middleware#ordering) para obter mais detalhes.

> [!IMPORTANT]
> Se o destino do .NET Framework e usando o provedor baseadas em sessão, adicione o [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session) pacote NuGet ao seu projeto.

## <a name="query-strings"></a>Cadeias de caracteres de consulta

Você pode passar uma quantidade limitada de dados de uma solicitação para outro, adicionando-a cadeia de caracteres de consulta da nova solicitação. Isso é útil para capturar o estado de uma maneira persistente que permite que os links com estado inserido deve ser compartilhado por email ou por redes sociais. No entanto, por esse motivo, você nunca deve usar cadeias de caracteres de consulta para dados confidenciais. Além de ser facilmente compartilhados, inclusive dados em cadeias de caracteres de consulta pode criar oportunidades de [falsificação de solicitação entre sites (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) ataques, que podem enganar os usuários a visitar sites mal-intencionados enquanto autenticado. Os invasores podem roubar dados do usuário de seu aplicativo ou executar ações mal-intencionadas em nome do usuário. Qualquer estado preservado application ou session deve proteger contra ataques CSRF. Para obter mais informações sobre ataques CSRF, consulte [ataques impedindo intersite solicitar CSRF (falsificação XSRF /) no núcleo do ASP.NET](../security/anti-request-forgery.md).

## <a name="post-data-and-hidden-fields"></a>Dados de postagem e campos ocultos

Dados podem ser salvos em campos de formulário oculto e lançados novamente na próxima solicitação. Isso é comum em formulários de várias páginas. No entanto, como o cliente pode potencialmente adulterar os dados, o servidor deve sempre validá-lo novamente. 

## <a name="cookies"></a>Cookies

Cookies fornecem uma maneira de armazenar dados específicos do usuário em aplicativos da web. Porque os cookies são enviados com cada solicitação, seu tamanho deve ser reduzido ao mínimo. Idealmente, apenas um identificador deve ser armazenado em um cookie com os dados reais armazenados no servidor. A maioria dos navegadores restringir cookies 4096 bytes. Além disso, somente um número limitado dos cookies está disponível para cada domínio.  

Como os cookies estão sujeitos a falsificações, eles devem ser validados no servidor. Embora a durabilidade do cookie em um cliente esteja sujeito a expiração e a intervenção do usuário, elas geralmente são a forma mais durável de persistência de dados no cliente.

Cookies são usados para personalização, onde o conteúdo é personalizado para um usuário conhecido. Como o usuário é identificado apenas e não autenticado na maioria dos casos, você normalmente pode proteger um cookie ao armazenar o nome de usuário, nome da conta ou uma ID de usuário exclusiva (como um GUID) no cookie. Você pode usar o cookie para acessar a infraestrutura de personalização de usuário de um site.

## <a name="httpcontextitems"></a>HttpContext

O `Items` coleção é um bom local para armazenar dados que é necessário somente durante processamento de uma determinada solicitação. O conteúdo da coleção é descartado após cada solicitação. O `Items` coleção melhor é usada como uma maneira de componentes ou middleware para comunicar-se quando eles operam em pontos diferentes durante uma solicitação e não têm nenhuma maneira direta para passar parâmetros. Para obter mais informações, consulte [trabalhando com HttpContext](#working-with-httpcontextitems), mais adiante neste artigo.

## <a name="cache"></a>Cache

O cache é uma maneira eficiente de armazenar e recuperar dados. Você pode controlar o tempo de vida de itens em cache com base na hora e outras considerações. Saiba mais sobre [cache](../performance/caching/index.md).

<a name="session"></a>
## <a name="working-with-session-state"></a>Trabalhando com o estado de sessão

### <a name="configuring-session"></a>Configuração de sessão

O `Microsoft.AspNetCore.Session` pacote fornece middleware para gerenciar o estado da sessão. Para habilitar o middleware de sessão, `Startup` deve conter:

- Qualquer uma da [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) caches de memória. O `IDistributedCache` implementação é usada como um repositório de backup para a sessão.
- [AddSession](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions#Microsoft_Extensions_DependencyInjection_SessionServiceCollectionExtensions_AddSession_Microsoft_Extensions_DependencyInjection_IServiceCollection_) chamar, que requer o pacote do NuGet "Microsoft.AspNetCore.Session".
- [UseSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) chamar.

O código a seguir mostra como configurar o provedor de sessão na memória.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](app-state/sample/src/WebAppSessionDotNetCore2.0App/Startup.cs?highlight=11-19,24)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](app-state/sample/src/WebAppSession/Startup.cs?highlight=11-19,24)]

---

Você pode fazer referência a sessão de `HttpContext` quando ele é instalado e configurado.

Se você tentar acessar `Session` antes de `UseSession` tiver sido chamado, a exceção `InvalidOperationException: Session has not been configured for this application or request` é lançada.

Se você tentar criar um novo `Session` (ou seja, nenhum cookie de sessão foi criado) depois que você já começou a gravar o `Response` fluxo, a exceção `InvalidOperationException: The session cannot be established after the response has started` é lançada. A exceção pode ser encontrada no log do servidor web; ele não será exibido no navegador.

### <a name="loading-session-asynchronously"></a>Carregamento de sessão de forma assíncrona 

O provedor de sessão padrão no ASP.NET Core carrega o registro da sessão de subjacente [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) repositório de forma assíncrona somente se o [ISession.LoadAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession#Microsoft_AspNetCore_Http_ISession_LoadAsync) método for chamado explicitamente antes  o `TryGetValue`, `Set`, ou `Remove` métodos. Se `LoadAsync` não for chamado pela primeira vez, a base de registro de sessão é carregado de forma síncrona, que pode afetar a capacidade de dimensionar do aplicativo.

Para que aplicativos impor esse padrão, encapsule o [DistributedSessionStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsessionstore) e [DistributedSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsession) implementações com versões que geram uma exceção se o `LoadAsync` método não é chamado antes de `TryGetValue`, `Set`, ou `Remove`. Registre as versões encapsuladas no contêiner de serviços.

### <a name="implementation-details"></a>Detalhes de implementação

Sessão usa um cookie para controlar e identificar as solicitações de um navegador único. Por padrão, esse cookie é denominado ". AspNet.Session"e usa um caminho de"/". Como o padrão de cookie não especificar um domínio, ele não ficam disponíveis para o script do lado do cliente na página (como `CookieHttpOnly` padrão é `true`).

Para substituir os padrões de sessão, use `SessionOptions`:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](app-state/sample/src/WebAppSessionDotNetCore2.0App/StartupCopy.cs?name=snippet1&highlight=8-12)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](app-state/sample/src/WebAppSession/StartupCopy.cs?name=snippet1&highlight=8-12)]

---

O servidor usa o `IdleTimeout` propriedade para determinar quanto tempo uma sessão pode ficar ociosa antes de seu conteúdo está sendo abandonado. Essa propriedade é independente da expiração do cookie. Cada solicitação que passa por meio do middleware de sessão (lida ou gravada para) redefine o tempo limite.

Porque `Session` é *não bloqueio*, se duas solicitações tentam modificar o conteúdo da sessão, o último deles substitui o primeiro. `Session`é implementado como um *sessão coerente*, que significa que todo o conteúdo é armazenado juntos. Duas solicitações que estão modificando diferentes partes da sessão (chaves diferentes) ainda podem afetar uns aos outros.

### <a name="setting-and-getting-session-values"></a>Configuração e obter valores de sessão

Sessão é acessada por meio de `Session` propriedade `HttpContext`. Esta propriedade é um [ISession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession) implementação.

O exemplo a seguir mostra a configuração e a obtenção de um inteiro e uma cadeia de caracteres:

[!code-csharp[Main](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?range=8-27,49)]

Se você adicionar os seguintes métodos de extensão, você pode definir e obter objetos serializáveis a sessão:

[!code-csharp[Main](app-state/sample/src/WebAppSession/Extensions/SessionExtensions.cs)]

O exemplo a seguir mostra como definir e obter um objeto serializável:

[!code-csharp[Main](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?name=snippet2)]


## <a name="working-with-httpcontextitems"></a>Trabalhando com HttpContext

O `HttpContext` abstração oferece suporte para uma coleção de dicionário do tipo `IDictionary<object, object>`, chamado `Items`. Essa coleção está disponível desde o início de uma *HttpRequest* e serão descartados no final de cada solicitação. Você pode acessá-lo, atribuindo um valor a uma entrada de chave, ou solicitando o valor para uma determinada chave.

No exemplo abaixo, [Middleware](middleware.md) adiciona `isVerified` para o `Items` coleção.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Mais tarde no pipeline, outro middleware pode acessá-lo:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync("Verified request? " + 
        context.Items["isVerified"]);
});
```

Para o middleware que será usado apenas por um único aplicativo, `string` chaves são aceitáveis. No entanto, o middleware que será compartilhado entre aplicativos deve usar chaves de objeto exclusivo para evitar qualquer possibilidade de colisões de chaves. Se você estiver desenvolvendo o middleware que deve trabalhar em vários aplicativos, use uma chave de objeto exclusivo definida em sua classe de middleware conforme mostrado abaixo:

```csharp
public class SampleMiddleware
{
    public static readonly object SampleKey = new Object();

    public async Task Invoke(HttpContext httpContext)
    {
        httpContext.Items[SampleKey] = "some value";
        // additional code omitted
    }
}
```

Outro código pode acessar o valor armazenado em `HttpContext.Items` usando a chave exposta pela classe middleware:

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        string value = HttpContext.Items[SampleMiddleware.SampleKey];
    }
}
```

Essa abordagem também tem a vantagem de eliminar a repetição de "magic cadeias de caracteres" em vários locais no código.

<a name="appstate-errors"></a>

## <a name="application-state-data"></a>Dados de estado do aplicativo

Use [injeção de dependência](xref:fundamentals/dependency-injection) para tornar dados disponíveis a todos os usuários:

1. Definir um serviço que contém os dados (por exemplo, uma classe denominada `MyAppData`).

```csharp
public class MyAppData
{
    // Declare properties/methods/etc.
} 
```
2. Adicione a classe de serviço para `ConfigureServices` (por exemplo `services.AddSingleton<MyAppData>();`).
3. Consuma a classe de serviço de dados em cada controlador:

```csharp
public class MyController : Controller
{
    public MyController(MyAppData myService)
    {
        // Do something with the service (read some data from it, 
        // store it in a private field/property, etc.)
    }
} 
```

## <a name="common-errors-when-working-with-session"></a>Erros comuns ao trabalhar com a sessão

* "Não é possível resolver o serviço para o tipo 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' ao tentar ativar 'Microsoft.AspNetCore.Session.DistributedSessionStore'."

  Isso geralmente é causado por falha ao configurar pelo menos um `IDistributedCache` implementação. Para obter mais informações, consulte [trabalhando com um Cache distribuído](xref:performance/caching/distributed) e [no cache de memória](xref:performance/caching/memory).

* No caso de que a sessão de middleware não conseguir manter uma sessão (por exemplo: se o banco de dados não estiver disponível), ele registra a exceção e ignora. A solicitação continuará normalmente, o que leva a um comportamento imprevisível muito.

Um exemplo típico:

Alguém armazena um carrinho de compras na sessão. O usuário adiciona um item, mas a confirmação falhará. O aplicativo não sabe sobre a falha para relatar a mensagem "o item foi adicionado", que não é true.

A maneira recomendada para verificar esses erros é chamar `await feature.Session.CommitAsync();` pelo código do aplicativo quando você terminar gravação à sessão. Em seguida, você pode fazer o que você gosta com o erro. Ele funciona da mesma forma, ao chamar `LoadAsync`.


### <a name="additional-resources"></a>Recursos adicionais


* [ASP.NET Core 1. x: exemplo de código usado neste documento](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/sample/src/WebAppSession)
* [ASP.NET Core 2. x: exemplo de código usado neste documento](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/sample/src/WebAppSessionDotNetCore2.0App)
