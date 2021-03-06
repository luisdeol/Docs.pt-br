---
title: "Configuração de logon externo do Facebook no núcleo do ASP.NET"
author: rick-anderson
description: "Este tutorial demonstra a integração da autenticação de usuário de conta do Facebook em um aplicativo existente do ASP.NET Core."
keywords: "ASP.NET Core, Facebook, login, autenticação"
ms.author: riande
manager: wpickett
ms.date: 08/01/2017
ms.topic: article
ms.assetid: 8c65179b-688c-4af1-8f5e-1862920cda95
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/facebook-logins
ms.openlocfilehash: 058670b4f699288e1acbe76bae08dcebf69346b8
ms.sourcegitcommit: 198fb0488e961048bfa376cf58cb853ef1d1cb91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="configuring-facebook-authentication"></a>Configurar a autenticação do Facebook

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial mostra como permitir que os usuários entrem com a conta do Facebook deles usando um projeto de amostra do ASP.NET Core 2.0 criado na [página anterior](index.md). Vamos começar criando um Facebook App ID seguindo as [etapas oficiais](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Criar o aplicativo no Facebook

*  Navegue até o [aplicativo Facebook desenvolvedores](https://developers.facebook.com/apps/) página e entre. Se você ainda não tiver uma conta do Facebook, use o **inscrever-se para o Facebook** link na página de logon para criar uma.

* Clique no botão **adicionar um novo aplicativo** no canto superior direito para criar uma nova ID de aplicativo.

   ![Abra o Facebook para o portal de desenvolvedores no Microsoft Edge](index/_static/FBMyApps.png)

* Preencha o formulário e clique no botão **criar ID do aplicativo**.

   ![Criar um formulário de nova ID de aplicativo](index/_static/FBNewAppId.png)

* Na página **selecionar um produto** , clique em **Set Up** no painel **Facebook Login**.

   ![Página de instalação do produto](index/_static/FBProductSetup.png)
  
* O **Quickstart** assistente iniciará com **escolher uma plataforma** como a primeira página. Ignorar o assistente agora clicando o **configurações** link no menu à esquerda:

   ![Início rápido do Skip](index/_static/FBSkipQuickStart.png)

* Você verá o **configurações do cliente OAuth** página:

![Página de configurações de OAuth do cliente](index/_static/FBOAuthSetup.png)

* Insira o URI de desenvolvimento com */signin-facebook* acrescentados no **válido URIs de redirecionamento OAuth** campo (por exemplo: `https://localhost:44320/signin-facebook`). A autenticação do Facebook configurada mais tarde neste tutorial automaticamente manipulará as solicitações no */signin-facebook* rota para implementar o fluxo do OAuth.

* Clique em **salvar alterações**.

* Clique o **painel** link no painel de navegação esquerdo. 

    Nessa página, anote o `App ID` e `App Secret`. Você adicionará ambos em seu aplicativo ASP.NET Core na próxima seção:

   ![Painel do desenvolvedor do Facebook](index/_static/FBDashboard.png)

* Ao implantar o site que você precise revisá o **logon do Facebook** página de instalação e registrar um novo URI público.

## <a name="store-facebook-app-id-and-app-secret"></a>Armazenar a ID do aplicativo Facebook e o segredo do aplicativo

Vincular as configurações confidenciais como Facebook `App ID` e `App Secret` para sua configuração de aplicativo usando o [Manager segredo](xref:security/app-secrets). Para os fins deste tutorial, nomeie os tokens `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret`.

Execute os seguintes comandos para armazenar com segurança `App ID` e `App Secret` usando o Gerenciador de segredo:

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>Configurar a autenticação do Facebook

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Adicione o serviço do Facebook no `ConfigureServices` método o *Startup.cs* arquivo:

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE[default settings configuration](includes/default-settings.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Instalar o [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) pacote.

* Para instalar este pacote com 2017 do Visual Studio, clique com botão direito no projeto e selecione **gerenciar pacotes NuGet**.
* Para instalar o .NET Core CLI, execute o seguinte no diretório do projeto:

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

Adicionar o middleware do Facebook no `Configure` método *Startup.cs* arquivo:

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

Consulte o [FacebookOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.facebookoptions) referência de API para obter mais informações sobre opções de configuração com suporte a autenticação do Facebook. Opções de configuração podem ser usadas para:

* Solicite informações diferentes sobre o usuário.
* Adicione argumentos de cadeia de caracteres de consulta para personalizar a experiência de logon.

## <a name="sign-in-with-facebook"></a>Entrar com o Facebook

Execute o aplicativo e clique em **login**. Você verá uma opção para entrar com o Facebook.

![Aplicativo Web: usuário não autenticado](index/_static/DoneFacebook.png)

Quando você clica na **Facebook**, você será redirecionado para o Facebook para autenticação:

![Página de autenticação do Facebook](index/_static/FBLogin.png)

Endereço de email e o perfil público de solicitações de autenticação do Facebook por padrão:

![Página de autenticação do Facebook](index/_static/FBLoginDone.png)

Depois que você insira suas credenciais de Facebook, que você será redirecionado para o site onde você pode definir seu email.

Agora você está conectado usando suas credenciais do Facebook:

![Aplicativo Web: usuário autenticado](index/_static/Done.png)

## <a name="troubleshooting"></a>Solução de problemas

* **ASP.NET Core 2. x somente:** identidade se não está configurada por meio da chamada `services.AddIdentity` na `ConfigureServices`, tentar autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*. O modelo de projeto usado neste tutorial garante que isso é feito.
* Se o banco de dados do site não tiver sido criado, aplicando a migração inicial, você obtém *uma operação de banco de dados falhou ao processar a solicitação* erro. Toque em **aplicar migrações** para criar o banco de dados e a atualização para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com o Facebook. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados no [página anterior](index.md).

* Depois de publicar seu site da web para o aplicativo web do Azure, você deve redefinir o `AppSecret` no portal do desenvolvedor do Facebook.

* Definir o `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret` como configurações de aplicativo no portal do Azure. O sistema de configuração é configurado para ler as chaves de variáveis de ambiente.
