---
uid: mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
title: Criar MVC 5 aplicativo com Facebook, Twitter, LinkedIn e Google OAuth2 Sign-on (c#) | Microsoft Docs
author: Rick-Anderson
description: "Este tutorial mostra como criar um aplicativo web ASP.NET MVC 5 que permite que os usuários façam logon usando OAuth 2.0 com as credenciais de um autenti externa..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/03/2015
ms.topic: article
ms.assetid: 81ee500f-fc37-40d6-8722-f1b64720fbb6
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
msc.type: authoredcontent
ms.openlocfilehash: aaa061e61b9bab5b33083851624f0487b2cf6473
ms.sourcegitcommit: ccf08615ad59bc6f654560de33b93396113a2eb0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2017
---
<a name="create-an-aspnet-mvc-5-app-with-facebook-twitter-linkedin-and-google-oauth2-sign-on-c"></a><span data-ttu-id="e0b62-103">Criar um aplicativo ASP.NET MVC 5 com o Facebook, Twitter, LinkedIn e Google OAuth2 Sign-on (c#)</span><span class="sxs-lookup"><span data-stu-id="e0b62-103">Create an ASP.NET MVC 5 App with Facebook, Twitter, LinkedIn and Google OAuth2 Sign-on (C#)</span></span>
====================
<span data-ttu-id="e0b62-104">Por [Rick Anderson](https://github.com/Rick-Anderson)</span><span class="sxs-lookup"><span data-stu-id="e0b62-104">by [Rick Anderson](https://github.com/Rick-Anderson)</span></span>

> <span data-ttu-id="e0b62-105">Este tutorial mostra como criar um aplicativo web ASP.NET MVC 5 que permite aos usuários fazer logon usando [OAuth 2.0](http://oauth.net/2/) com as credenciais de um provedor de autenticação externa, como Facebook, Twitter, LinkedIn, Microsoft ou Google.</span><span class="sxs-lookup"><span data-stu-id="e0b62-105">This tutorial shows you how to build an ASP.NET MVC 5 web application that enables users to log in using [OAuth 2.0](http://oauth.net/2/) with credentials from an external authentication provider, such as Facebook, Twitter, LinkedIn, Microsoft, or Google.</span></span> <span data-ttu-id="e0b62-106">Para simplificar, este tutorial concentra-se sobre como trabalhar com as credenciais do Facebook e do Google.</span><span class="sxs-lookup"><span data-stu-id="e0b62-106">For simplicity, this tutorial focuses on working with credentials from Facebook and Google.</span></span>
> 
> <span data-ttu-id="e0b62-107">Habilitar essas credenciais em sites da web fornece uma vantagem significativa pois milhões de usuários já têm contas com esses provedores externos.</span><span class="sxs-lookup"><span data-stu-id="e0b62-107">Enabling these credentials in your web sites provides a significant advantage because millions of users already have accounts with these external providers.</span></span> <span data-ttu-id="e0b62-108">Esses usuários podem ser mais decididos a inscrever-se para o seu site se eles não precisam criar e lembre-se de um novo conjunto de credenciais.</span><span class="sxs-lookup"><span data-stu-id="e0b62-108">These users may be more inclined to sign up for your site if they do not have to create and remember a new set of credentials.</span></span>
> 
> <span data-ttu-id="e0b62-109">Consulte também [aplicativo ASP.NET MVC 5 com SMS e o email de autenticação de dois fatores](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication.md).</span><span class="sxs-lookup"><span data-stu-id="e0b62-109">See also [ASP.NET MVC 5 app with SMS and email Two-Factor Authentication](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication.md).</span></span>
> 
> <span data-ttu-id="e0b62-110">O tutorial também mostra como adicionar dados de perfil do usuário e como usar a API de associação para adicionar funções.</span><span class="sxs-lookup"><span data-stu-id="e0b62-110">The tutorial also shows how to add profile data for the user, and how to use the Membership API to add roles.</span></span> <span data-ttu-id="e0b62-111">Este tutorial foi escrito por [Rick Anderson](https://blogs.msdn.com/rickAndy) (siga-me no Twitter: [ @RickAndMSFT ](https://twitter.com/RickAndMSFT) ).</span><span class="sxs-lookup"><span data-stu-id="e0b62-111">This tutorial was written by [Rick Anderson](https://blogs.msdn.com/rickAndy) ( Please follow me on Twitter: [@RickAndMSFT](https://twitter.com/RickAndMSFT) ).</span></span>


<a id="start"></a>
## <a name="getting-started"></a><span data-ttu-id="e0b62-112">Guia de Introdução</span><span class="sxs-lookup"><span data-stu-id="e0b62-112">Getting Started</span></span>

<span data-ttu-id="e0b62-113">Comece instalando e executando [Visual Studio Express 2013 para Web](https://go.microsoft.com/fwlink/?LinkId=299058) ou [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566).</span><span class="sxs-lookup"><span data-stu-id="e0b62-113">Start by installing and running [Visual Studio Express 2013 for Web](https://go.microsoft.com/fwlink/?LinkId=299058) or [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566).</span></span> <span data-ttu-id="e0b62-114">Instalar o Visual Studio [2013 atualização 3](https://go.microsoft.com/fwlink/?LinkId=390521) ou superior.</span><span class="sxs-lookup"><span data-stu-id="e0b62-114">Install Visual Studio [2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390521) or higher.</span></span> <span data-ttu-id="e0b62-115">Para obter ajuda com Dropbox, GitHub, Linkedin, Instagram, buffer, a equipe de vendas, o fluxo, pilha de Exchange, Tripit, twitch, Twitter, Yahoo e muito mais, consulte [uma parar guia](http://www.oauthforaspnet.com/).</span><span class="sxs-lookup"><span data-stu-id="e0b62-115">For help with Dropbox, GitHub, Linkedin, Instagram, buffer, salesforce, STEAM, Stack Exchange, Tripit, twitch, Twitter, Yahoo and more, see this [one stop guide](http://www.oauthforaspnet.com/).</span></span>

> [!NOTE]
> <span data-ttu-id="e0b62-116">Você deve instalar o Visual Studio [2013 atualização 3](https://go.microsoft.com/fwlink/?LinkId=390521) ou superior para usar o Google OAuth 2 e depurar localmente, sem avisos de SSL.</span><span class="sxs-lookup"><span data-stu-id="e0b62-116">You must install Visual Studio [2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390521) or higher to use Google OAuth 2 and to debug locally without SSL warnings.</span></span>


<span data-ttu-id="e0b62-117">Clique em **novo projeto** do **iniciar** página, ou você pode usar o menu e selecione **arquivo**e, em seguida, **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-117">Click **New Project** from the **Start** page, or you can use the menu and select **File**, and then **New Project**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image1.png)  
 

<a id="1st"></a>
## <a name="creating-your-first-application"></a><span data-ttu-id="e0b62-118">Criando seu primeiro aplicativo</span><span class="sxs-lookup"><span data-stu-id="e0b62-118">Creating Your First Application</span></span>

<span data-ttu-id="e0b62-119">Clique em **novo projeto**, em seguida, selecione **Visual C#** à esquerda, em seguida, **Web** e, em seguida, selecione **aplicativo Web ASP.NET**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-119">Click **New Project**, then select **Visual C#** on the left, then **Web** and then select **ASP.NET Web Application**.</span></span> <span data-ttu-id="e0b62-120">Nomeie o projeto "MvcAuth" e, em seguida, clique em **Okey**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-120">Name your project "MvcAuth" and then click **OK**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image2.png)

<span data-ttu-id="e0b62-121">No **novo projeto ASP.NET** caixa de diálogo, clique em **MVC**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-121">In the **New ASP.NET Project** dialog, click **MVC**.</span></span> <span data-ttu-id="e0b62-122">Se a autenticação não é **contas de usuário individuais**, clique no **alterar autenticação** botão e selecione **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-122">If the Authentication is not **Individual User Accounts**, click the **Change Authentication** button and select **Individual User Accounts**.</span></span> <span data-ttu-id="e0b62-123">Verificando **Host na nuvem**, o aplicativo será muito fácil hospedar no Azure.</span><span class="sxs-lookup"><span data-stu-id="e0b62-123">By checking **Host in the cloud**, the app will be very easy to host in Azure.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image3.png)

<span data-ttu-id="e0b62-124">Se você selecionou **Host na nuvem**, conclua a caixa de diálogo Configurar.</span><span class="sxs-lookup"><span data-stu-id="e0b62-124">If you selected **Host in the cloud**, complete the configure dialog.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image4.png)


### <a name="use-nuget-to-update-to-the-latest-owin-middleware"></a><span data-ttu-id="e0b62-125">Usar o NuGet para atualizar para o middleware OWIN mais recente</span><span class="sxs-lookup"><span data-stu-id="e0b62-125">Use NuGet to update to the latest OWIN middleware</span></span>

<span data-ttu-id="e0b62-126">Usar o NuGet package manager para atualizar o [middleware OWIN](../../../aspnet/overview/owin-and-katana/getting-started-with-owin-and-katana.md).</span><span class="sxs-lookup"><span data-stu-id="e0b62-126">Use the NuGet package manager to update the [OWIN middleware](../../../aspnet/overview/owin-and-katana/getting-started-with-owin-and-katana.md).</span></span> <span data-ttu-id="e0b62-127">Selecione **atualizações** no menu à esquerda.</span><span class="sxs-lookup"><span data-stu-id="e0b62-127">Select **Updates** in the left menu.</span></span> <span data-ttu-id="e0b62-128">Você pode clicar no **Atualizar tudo** botão ou você pode procurar apenas pacotes OWIN (mostrados na próxima imagem):</span><span class="sxs-lookup"><span data-stu-id="e0b62-128">You can click on the **Update All** button or you can search for only OWIN packages (shown in the next image):</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image5.png)

<span data-ttu-id="e0b62-129">Na imagem abaixo, apenas pacotes OWIN são mostrados:</span><span class="sxs-lookup"><span data-stu-id="e0b62-129">In the image below, only OWIN packages are shown:</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image6.png)

<span data-ttu-id="e0b62-130">Do pacote Manager Console (PMC), você pode inserir o `Update-Package` comando, que atualizará todos os pacotes.</span><span class="sxs-lookup"><span data-stu-id="e0b62-130">From the Package Manager Console (PMC), you can enter the `Update-Package` command, which will update all packages.</span></span>

<span data-ttu-id="e0b62-131">Pressione **F5** ou **Ctrl + F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e0b62-131">Press **F5** or **Ctrl+F5** to run the application.</span></span> <span data-ttu-id="e0b62-132">Na imagem abaixo, o número da porta for 1234.</span><span class="sxs-lookup"><span data-stu-id="e0b62-132">In the image below, the port number is 1234.</span></span> <span data-ttu-id="e0b62-133">Quando você executa o aplicativo, você verá um número de porta diferente.</span><span class="sxs-lookup"><span data-stu-id="e0b62-133">When you run the application, you'll see a different port number.</span></span>

<span data-ttu-id="e0b62-134">Dependendo do tamanho da janela do navegador, talvez seja necessário clicar no ícone de navegação para ver o **início**, **sobre**, **contato**, **registrar**e **login** links.</span><span class="sxs-lookup"><span data-stu-id="e0b62-134">Depending on the size of your browser window, you might need to click the navigation icon to see the **Home**, **About**, **Contact**, **Register** and **Log in** links.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image7.png)  
![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image8.png) 

<a id="ssl"></a>
## <a name="setting-up-ssl-in-the-project"></a><span data-ttu-id="e0b62-135">Configurar o SSL no projeto</span><span class="sxs-lookup"><span data-stu-id="e0b62-135">Setting up SSL in the Project</span></span>

<span data-ttu-id="e0b62-136">Para se conectar a provedores de autenticação, como Google e Facebook, você precisará configurar o IIS Express para usar SSL.</span><span class="sxs-lookup"><span data-stu-id="e0b62-136">To connect to authentication providers like Google and Facebook, you will need to set up IIS-Express to use SSL.</span></span> <span data-ttu-id="e0b62-137">É importante manter com SSL após o logon e não solte o HTTP, o cookie de logon é apenas como segredo como seu nome de usuário e senha e sem o uso de SSL que você está enviando em texto não criptografado pela rede.</span><span class="sxs-lookup"><span data-stu-id="e0b62-137">It's important to keep using SSL after login and not drop back to HTTP, your login cookie is just as secret as your username and password, and without using SSL you're sending it in clear-text across the wire.</span></span> <span data-ttu-id="e0b62-138">Além disso, você já passou tempo para executar o handshake e proteja o canal (que é a maior parte do que o torna HTTPS mais lento do que o HTTP) antes do pipeline do MVC é executado, portanto é redirecionada para HTTP depois que você está conectado não fazer a solicitação atual ou futuro solicitações muito mais rápidas.</span><span class="sxs-lookup"><span data-stu-id="e0b62-138">Besides, you've already taken the time to perform the handshake and secure the channel (which is the bulk of what makes HTTPS slower than HTTP) before the MVC pipeline is run, so redirecting back to HTTP after you're logged in won't make the current request or future requests much faster.</span></span>

1. <span data-ttu-id="e0b62-139">Em **Solution Explorer**, clique no **MvcAuth** projeto.</span><span class="sxs-lookup"><span data-stu-id="e0b62-139">In **Solution Explorer**, click the **MvcAuth** project.</span></span>
2. <span data-ttu-id="e0b62-140">Pressione a tecla F4 para mostrar as propriedades do projeto.</span><span class="sxs-lookup"><span data-stu-id="e0b62-140">Hit the F4 key to show the project properties.</span></span> <span data-ttu-id="e0b62-141">Como alternativa, a partir de **exibição** menu, você pode selecionar **janela propriedades**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-141">Alternatively, from the **View** menu you can select **Properties Window**.</span></span>
3. <span data-ttu-id="e0b62-142">Alterar **SSL habilitado** como True.</span><span class="sxs-lookup"><span data-stu-id="e0b62-142">Change **SSL Enabled** to True.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image9.png)
4. <span data-ttu-id="e0b62-143">Copie a URL de SSL (que será `https://localhost:44300/` , a menos que você criou outros projetos SSL).</span><span class="sxs-lookup"><span data-stu-id="e0b62-143">Copy the SSL URL (which will be `https://localhost:44300/` unless you've created other SSL projects).</span></span>
5. <span data-ttu-id="e0b62-144">Em **Solution Explorer**, clique com botão direito do **MvcAuth** do projeto e selecione **propriedades**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-144">In **Solution Explorer**, right click the **MvcAuth** project and select **Properties**.</span></span>
6. <span data-ttu-id="e0b62-145">Selecione o **Web** guia e, em seguida, cole a URL de SSL para o **Url do projeto** caixa.</span><span class="sxs-lookup"><span data-stu-id="e0b62-145">Select the **Web** tab, and then paste the SSL URL into the **Project Url** box.</span></span> <span data-ttu-id="e0b62-146">Salve o arquivo (Ctl + S).</span><span class="sxs-lookup"><span data-stu-id="e0b62-146">Save the file (Ctl+S).</span></span> <span data-ttu-id="e0b62-147">Você precisará essa URL para configurar aplicativos de autenticação do Facebook e do Google.</span><span class="sxs-lookup"><span data-stu-id="e0b62-147">You will need this URL to configure Facebook and Google authentication apps.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image10.png)
7. <span data-ttu-id="e0b62-148">Adicionar o [RequireHttps](https://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx) de atributo para o `Home` controlador para exigir que todas as solicitações deve usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e0b62-148">Add the [RequireHttps](https://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx) attribute to the `Home` controller to require all requests must use HTTPS.</span></span> <span data-ttu-id="e0b62-149">Uma abordagem mais segura é adicionar a [RequireHttps](https://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx) filtro para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e0b62-149">A more secure approach is to add the [RequireHttps](https://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx) filter to the application.</span></span> <span data-ttu-id="e0b62-150">Consulte a seção &quot;proteger o aplicativo com o SSL e o atributo autorizar&quot; na minha tutoral [criar um aplicativo ASP.NET MVC com autenticação e o banco de dados SQL e implantar o serviço de aplicativo do Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="e0b62-150">See the section &quot;Protect the Application with SSL and the Authorize Attribute&quot; in my tutoral [Create an ASP.NET MVC app with auth and SQL DB and deploy to Azure App Service](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data).</span></span> <span data-ttu-id="e0b62-151">Uma parte do controlador Home é mostrada abaixo.</span><span class="sxs-lookup"><span data-stu-id="e0b62-151">A portion of the Home controller is shown below.</span></span>

    [!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample1.cs?highlight=1)]
8. <span data-ttu-id="e0b62-152">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e0b62-152">Press CTRL+F5 to run the application.</span></span> <span data-ttu-id="e0b62-153">Se você instalou o certificado no passado, você pode ignorar o restante desta seção e ir para [criar um aplicativo do Google OAuth 2 e conectar o aplicativo ao projeto](#goog), caso contrário, siga as instruções para confiar autoassinado certificado que o IIS Express gerou.</span><span class="sxs-lookup"><span data-stu-id="e0b62-153">If you've installed the certificate in the past, you can skip the rest of this section and jump to [Creating a Google app for OAuth 2 and connecting the app to the project](#goog), otherwise, follow the instructions to trust the self-signed certificate that IIS Express has generated.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image11.png)
9. <span data-ttu-id="e0b62-154">Leitura de **aviso de segurança** caixa de diálogo e clique **Sim** se você deseja instalar o certificado que representa o localhost.</span><span class="sxs-lookup"><span data-stu-id="e0b62-154">Read the **Security Warning** dialog and then click **Yes** if you want to install the certificate representing localhost.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image12.png)
10. <span data-ttu-id="e0b62-155">IE mostra o *início* página e não houver nenhum aviso de SSL.</span><span class="sxs-lookup"><span data-stu-id="e0b62-155">IE shows the *Home* page and there are no SSL warnings.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image13.png)
11. <span data-ttu-id="e0b62-156">Google Chrome também aceita o certificado e mostrará o conteúdo HTTPS sem aviso.</span><span class="sxs-lookup"><span data-stu-id="e0b62-156">Google Chrome also accepts the certificate and will show HTTPS content without a warning.</span></span> <span data-ttu-id="e0b62-157">Firefox usa seu próprio repositório de certificados, portanto ele exibirá um aviso.</span><span class="sxs-lookup"><span data-stu-id="e0b62-157">Firefox uses its own certificate store, so it will display a warning.</span></span> <span data-ttu-id="e0b62-158">Em nosso aplicativo você pode clicar com segurança **, entender os riscos**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-158">For our application you can safely click **I Understand the Risks**.</span></span>   
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image14.png)

<a id="goog"></a>
## <a name="creating-a-google-app-for-oauth-2-and-connecting-the-app-to-the-project"></a><span data-ttu-id="e0b62-159">Criar um aplicativo do Google OAuth 2 e conectar o aplicativo ao projeto</span><span class="sxs-lookup"><span data-stu-id="e0b62-159">Creating a Google app for OAuth 2 and connecting the app to the project</span></span>

1. <span data-ttu-id="e0b62-160">Navegue até o [Console de desenvolvedores do Google](https://console.developers.google.com/).</span><span class="sxs-lookup"><span data-stu-id="e0b62-160">Navigate to the [Google Developers Console](https://console.developers.google.com/).</span></span>
1. <span data-ttu-id="e0b62-161">Se você não criou um projeto antes de, selecione **credenciais** na guia à esquerda e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-161">If you haven't created a project before, select **Credentials** in the left tab, and then select **Create**.</span></span>
1. <span data-ttu-id="e0b62-162">Na guia à esquerda, clique em **credenciais**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-162">In the left tab, click **Credentials**.</span></span>
1. <span data-ttu-id="e0b62-163">Clique em **criar credenciais** , em seguida, **ID do cliente OAuth**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-163">Click **Create credentials** then **OAuth client ID**.</span></span> 

    1. <span data-ttu-id="e0b62-164">No **criar ID do cliente** caixa de diálogo, mantenha o padrão **aplicativo Web** para o tipo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e0b62-164">In the **Create Client ID** dialog, keep the default **Web application** for the application type.</span></span>
    2. <span data-ttu-id="e0b62-165">Definir o **JavaScript autorizado** origens para a URL de SSL usado acima (`https://localhost:44300/` , a menos que você criou outros projetos SSL)</span><span class="sxs-lookup"><span data-stu-id="e0b62-165">Set the **Authorized JavaScript** origins to the SSL URL you used above (`https://localhost:44300/` unless you've created other SSL projects)</span></span>
    3. <span data-ttu-id="e0b62-166">Definir o **URI de redirecionamento autorizado** para:</span><span class="sxs-lookup"><span data-stu-id="e0b62-166">Set the **Authorized redirect URI** to:</span></span>  
         `https://localhost:44300/signin-google`
5. <span data-ttu-id="e0b62-167">Clique no item de menu de tela de consentimento de OAuth e definir seu nome de produto e de endereço de email.</span><span class="sxs-lookup"><span data-stu-id="e0b62-167">Click the OAuth Consent screen menu item, then set your email address and product name.</span></span> <span data-ttu-id="e0b62-168">Quando você tiver concluído do formulário, clique em **salvar**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-168">When you have completed the form click **Save**.</span></span>
6. <span data-ttu-id="e0b62-169">Clique no item de menu de biblioteca, pesquisar **API do Google +**, clique nele e pressione habilitar.</span><span class="sxs-lookup"><span data-stu-id="e0b62-169">Click the Library menu item, search **Google+ API**, click on it then press Enable.</span></span>
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image15.png)  
  
 <span data-ttu-id="e0b62-170">A imagem abaixo mostra as APIs habilitadas.</span><span class="sxs-lookup"><span data-stu-id="e0b62-170">The image below shows the enabled APIs.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image16.png)
7. <span data-ttu-id="e0b62-171">No Gerenciador de API de APIs do Google, visite o **credenciais** guia para obter o **ID do cliente**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-171">From the Google APIs API Manager, visit the **Credentials** tab to obtain the **Client ID**.</span></span> <span data-ttu-id="e0b62-172">Download para salvar um arquivo JSON com segredos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e0b62-172">Download to save a JSON file with application secrets.</span></span> <span data-ttu-id="e0b62-173">Copie e cole o **ClientId** e **ClientSecret** no `UseGoogleAuthentication` método encontrado no *Startup.Auth.cs* arquivo o *App_Start* pasta.</span><span class="sxs-lookup"><span data-stu-id="e0b62-173">Copy and paste the **ClientId** and **ClientSecret** into the `UseGoogleAuthentication` method found in the *Startup.Auth.cs* file in the *App_Start* folder.</span></span> <span data-ttu-id="e0b62-174">O **ClientId** e **ClientSecret** valores mostrados abaixo são exemplos e não funcionam.</span><span class="sxs-lookup"><span data-stu-id="e0b62-174">The **ClientId** and **ClientSecret** values shown below are samples and don't work.</span></span>

    [!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample2.cs?highlight=37-39)]

    > [!WARNING]
    > <span data-ttu-id="e0b62-175">Segurança - nunca armazenar os dados confidenciais em seu código-fonte.</span><span class="sxs-lookup"><span data-stu-id="e0b62-175">Security - Never store sensitive data in your source code.</span></span> <span data-ttu-id="e0b62-176">A conta e as credenciais são adicionadas ao código acima para manter o exemplo simples.</span><span class="sxs-lookup"><span data-stu-id="e0b62-176">The account and credentials are added to the code above to keep the sample simple.</span></span> <span data-ttu-id="e0b62-177">Consulte [práticas recomendadas para a implantação de senhas e outros dados confidenciais em ASP.NET e o serviço de aplicativo do Azure](../../../identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure.md).</span><span class="sxs-lookup"><span data-stu-id="e0b62-177">See [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service](../../../identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure.md).</span></span>
8. <span data-ttu-id="e0b62-178">Pressione **CTRL + F5** para compilar e executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e0b62-178">Press **CTRL+F5** to build and run the application.</span></span> <span data-ttu-id="e0b62-179">Clique o **login** link.</span><span class="sxs-lookup"><span data-stu-id="e0b62-179">Click the **Log in** link.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image17.png)
9. <span data-ttu-id="e0b62-180">Em **utilize outro serviço para fazer logon no**, clique em **Google**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-180">Under **Use another service to log in**, click **Google**.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image18.png)

    > [!NOTE]
    > <span data-ttu-id="e0b62-181">Se você perder qualquer uma das etapas acima, você obterá um erro HTTP 401.</span><span class="sxs-lookup"><span data-stu-id="e0b62-181">If you miss any of the steps above you will get a HTTP 401 error.</span></span> <span data-ttu-id="e0b62-182">Verifique novamente as etapas acima.</span><span class="sxs-lookup"><span data-stu-id="e0b62-182">Recheck your steps above.</span></span> <span data-ttu-id="e0b62-183">Se você perder uma configuração necessária (por exemplo **nome do produto**), adicionar o item e salve o ausentes, pode levar alguns minutos para que a autenticação funcione.</span><span class="sxs-lookup"><span data-stu-id="e0b62-183">If you miss a required setting (for example **product name**), add the missing item and save, it can take a few minutes for authentication to work.</span></span>
10. <span data-ttu-id="e0b62-184">Você será redirecionado para o site do google onde você irá inserir suas credenciais.</span><span class="sxs-lookup"><span data-stu-id="e0b62-184">You will be redirected to the google site where you will enter your credentials.</span></span>   
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image19.png)
11. <span data-ttu-id="e0b62-185">Depois de inserir suas credenciais, você deverá conceder permissões para o aplicativo web que você acabou de criar:</span><span class="sxs-lookup"><span data-stu-id="e0b62-185">After you enter your credentials, you will be prompted to give permissions to the web application you just created:</span></span>
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image20.png)
12. <span data-ttu-id="e0b62-186">Clique em **aceitar**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-186">Click **Accept**.</span></span> <span data-ttu-id="e0b62-187">Você agora será redirecionado para a **registrar** página do aplicativo MvcAuth onde você pode registrar sua conta do Google.</span><span class="sxs-lookup"><span data-stu-id="e0b62-187">You will now be redirected back to the **Register** page of the MvcAuth application where you can register your Google account.</span></span> <span data-ttu-id="e0b62-188">Você tem a opção de alterar o nome do registro de email local usado para a conta do Gmail, mas você geralmente deseja manter o alias de email padrão (ou seja, aquele usado para autenticação).</span><span class="sxs-lookup"><span data-stu-id="e0b62-188">You have the option of changing the local email registration name used for your Gmail account, but you generally want to keep the default email alias (that is, the one you used for authentication).</span></span> <span data-ttu-id="e0b62-189">Clique em **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-189">Click **Register**.</span></span>  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image21.png)

<a id="fb"></a>
## <a name="creating-the-app-in-facebook-and-connecting-the-app-to-the-project"></a><span data-ttu-id="e0b62-190">Criando o aplicativo no Facebook e conectar o aplicativo ao projeto</span><span class="sxs-lookup"><span data-stu-id="e0b62-190">Creating the app in Facebook and connecting the app to the project</span></span>

<span data-ttu-id="e0b62-191">Para a autenticação do Facebook OAuth2, você precisa copiar algumas configurações para o projeto de um aplicativo que você criar no Facebook.</span><span class="sxs-lookup"><span data-stu-id="e0b62-191">For Facebook OAuth2 authentication, you need to copy to your project some settings from an application that you create in Facebook.</span></span>

1. <span data-ttu-id="e0b62-192">No seu navegador, navegue até [https://developers.facebook.com/apps](https://developers.facebook.com/apps) e faça logon inserindo suas credenciais do Facebook.</span><span class="sxs-lookup"><span data-stu-id="e0b62-192">In your browser, navigate to [https://developers.facebook.com/apps](https://developers.facebook.com/apps) and log in by entering your Facebook credentials.</span></span>
2. <span data-ttu-id="e0b62-193">Se você já não estiver registrado como um desenvolvedor de Facebook, clique em **registrar como um desenvolvedor** e siga as instruções para registrar.</span><span class="sxs-lookup"><span data-stu-id="e0b62-193">If you aren't already registered as a Facebook developer, click **Register as a Developer** and follow the directions to register.</span></span>
3. <span data-ttu-id="e0b62-194">Sobre o **aplicativos** , clique em **criar novo aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-194">On the **Apps** tab, click **Create New App**.</span></span>

    ![Criar novo aplicativo](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image22.png)
4. <span data-ttu-id="e0b62-196">Insira um **nome do aplicativo** e **categoria**, em seguida, clique em **criar aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-196">Enter an **App Name** and **Category**, then click **Create App**.</span></span>

    <span data-ttu-id="e0b62-197">Isso deve ser exclusivo em Facebook.</span><span class="sxs-lookup"><span data-stu-id="e0b62-197">This must be unique across Facebook.</span></span> <span data-ttu-id="e0b62-198">O **aplicativo Namespace** é a parte da URL que seu aplicativo usará para acessar o aplicativo do Facebook para autenticação (por exemplo, https://apps.facebook.com/ {aplicativo Namespace}).</span><span class="sxs-lookup"><span data-stu-id="e0b62-198">The **App Namespace** is the part of the URL that your App will use to access the Facebook application for authentication (for example, https://apps.facebook.com/{App Namespace}).</span></span> <span data-ttu-id="e0b62-199">Se você não especificar um **aplicativo Namespace**, o **ID do aplicativo** será usado para a URL.</span><span class="sxs-lookup"><span data-stu-id="e0b62-199">If you don't specify an **App Namespace**, the **App ID** will be used for the URL.</span></span> <span data-ttu-id="e0b62-200">O **ID do aplicativo** é um número longo gerada pelo sistema que você verá na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="e0b62-200">The **App ID** is a long system-generated number that you will see in the next step.</span></span>

    ![Criar caixa de diálogo Novo aplicativo](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image23.png)
5. <span data-ttu-id="e0b62-202">Envie a verificação de segurança padrão.</span><span class="sxs-lookup"><span data-stu-id="e0b62-202">Submit the standard security check.</span></span>

    ![Verificação de segurança](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image24.png)
6. <span data-ttu-id="e0b62-204">Selecione **configurações** para a barra de menus à esquerda![ Barra de menus do desenvolvedor do Facebook](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image25.png)</span><span class="sxs-lookup"><span data-stu-id="e0b62-204">Select **Settings** for the left menu bar.![Facebook Developer's menu bar](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image25.png)</span></span>
7. <span data-ttu-id="e0b62-205">No **básica** seção configurações da página selecionar **Adicionar plataforma** para especificar que você está adicionando um aplicativo de site.</span><span class="sxs-lookup"><span data-stu-id="e0b62-205">On the **Basic** settings section of the page select **Add Platform** to specify that you are adding a website application.</span></span> <span data-ttu-id="e0b62-206">![Configurações básicas](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image26.png)</span><span class="sxs-lookup"><span data-stu-id="e0b62-206">![Basic Settings](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image26.png)</span></span>
8. <span data-ttu-id="e0b62-207">Selecione **site** entre as opções de plataforma.</span><span class="sxs-lookup"><span data-stu-id="e0b62-207">Select **Website** from the platform choices.</span></span>  
  
    ![Opções de plataforma](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image27.png)
9. <span data-ttu-id="e0b62-209">Anote sua **ID do aplicativo** e **segredo do aplicativo** para que você pode adicionar ambos em seu aplicativo MVC posteriormente neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="e0b62-209">Make a note of your **App ID** and your **App Secret** so that you can add both into your MVC application later in this tutorial.</span></span> <span data-ttu-id="e0b62-210">Além disso, adicione a URL do Site (`https://localhost:44300/`) para testar seu aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="e0b62-210">Also, Add your Site URL (`https://localhost:44300/`) to test your MVC application.</span></span> <span data-ttu-id="e0b62-211">Além disso, adicionar um **Contact Email**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-211">Also, add a **Contact Email**.</span></span> <span data-ttu-id="e0b62-212">Em seguida, selecione **salvar alterações**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-212">Then, select **Save Changes**.</span></span>   

    ![Página de detalhes do aplicativo básico](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image28.png)

    > [!NOTE]
    > <span data-ttu-id="e0b62-214">Observe que você só poderá se autenticar usando o alias de email que você registrou.</span><span class="sxs-lookup"><span data-stu-id="e0b62-214">Note that you will only be able to authenticate using the email alias you have registered.</span></span> <span data-ttu-id="e0b62-215">Outras contas de teste e os usuários não poderão registrar.</span><span class="sxs-lookup"><span data-stu-id="e0b62-215">Other users and test accounts will not be able to register.</span></span> <span data-ttu-id="e0b62-216">Você pode conceder outro acesso de contas do Facebook para o aplicativo do Facebook **funções de desenvolvedor** guia.</span><span class="sxs-lookup"><span data-stu-id="e0b62-216">You can grant other Facebook accounts access to the application on the Facebook **Developer Roles** tab.</span></span>
10. <span data-ttu-id="e0b62-217">No Visual Studio, abra *aplicativo\_Start\Startup.Auth.cs*.</span><span class="sxs-lookup"><span data-stu-id="e0b62-217">In Visual Studio, open *App\_Start\Startup.Auth.cs*.</span></span>
11. <span data-ttu-id="e0b62-218">Copie e cole o **AppId** e **segredo do aplicativo** para o `UseFacebookAuthentication` método.</span><span class="sxs-lookup"><span data-stu-id="e0b62-218">Copy and paste the **AppId** and **App Secret** into the `UseFacebookAuthentication` method.</span></span> <span data-ttu-id="e0b62-219">O **AppId** e **segredo do aplicativo** valores mostrados abaixo são exemplos e não funcionará.</span><span class="sxs-lookup"><span data-stu-id="e0b62-219">The **AppId** and **App Secret** values shown below are samples and will not work.</span></span>

    [!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample3.cs?highlight=33-35,38-39)]
12. <span data-ttu-id="e0b62-220">Clique em **salvar alterações**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-220">Click **Save Changes**.</span></span>
13. <span data-ttu-id="e0b62-221">Pressione **CTRL + F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e0b62-221">Press **CTRL+F5** to run the application.</span></span>


<span data-ttu-id="e0b62-222">Selecione **login** para exibir a página de logon.</span><span class="sxs-lookup"><span data-stu-id="e0b62-222">Select **Log in** to display the Login page.</span></span> <span data-ttu-id="e0b62-223">Clique em **Facebook** em **Use outro serviço para fazer logon.**</span><span class="sxs-lookup"><span data-stu-id="e0b62-223">Click **Facebook** under **Use another service to log in.**</span></span>

<span data-ttu-id="e0b62-224">Insira suas credenciais do Facebook.</span><span class="sxs-lookup"><span data-stu-id="e0b62-224">Enter your Facebook credentials.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image29.png)

<span data-ttu-id="e0b62-225">Você será solicitado a conceder permissão para o aplicativo acesse seu perfil público e a lista de amigos.</span><span class="sxs-lookup"><span data-stu-id="e0b62-225">You will be prompted to grant permission for the application to access your public profile and friend list.</span></span>

![Detalhes do aplicativo Facebook](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image30.png)

<span data-ttu-id="e0b62-227">Você está agora conectado.</span><span class="sxs-lookup"><span data-stu-id="e0b62-227">You are now logged in.</span></span> <span data-ttu-id="e0b62-228">Agora você pode registrar essa conta com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e0b62-228">You can now register this account with the application.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image31.png)

<span data-ttu-id="e0b62-229">Quando você se registra, uma entrada é adicionada para o *usuários* tabela do banco de dados de associação.</span><span class="sxs-lookup"><span data-stu-id="e0b62-229">When you register, an entry is added to the *Users* table of the membership database.</span></span>

<a id="mdb"></a>
## <a name="examine-the-membership-data"></a><span data-ttu-id="e0b62-230">Examinar os dados de associação</span><span class="sxs-lookup"><span data-stu-id="e0b62-230">Examine the Membership Data</span></span>

<span data-ttu-id="e0b62-231">No **exibição** menu, clique em **Server Explorer**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-231">In the **View** menu, click **Server Explorer**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image32.png)

<span data-ttu-id="e0b62-232">Expanda **DefaultConnection (MvcAuth)**, expanda **tabelas**, clique com botão direito **AspNetUsers** e clique em **Mostrar dados da tabela**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-232">Expand **DefaultConnection (MvcAuth)**, expand **Tables**, right click **AspNetUsers** and click **Show Table Data**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image33.png)

![dados da tabela aspnetusers](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image34.png)

<a id="ap"></a>
## <a name="adding-profile-data-to-the-user-class"></a><span data-ttu-id="e0b62-234">Adicionando dados de perfil para a classe de usuário</span><span class="sxs-lookup"><span data-stu-id="e0b62-234">Adding Profile Data to the User Class</span></span>

<span data-ttu-id="e0b62-235">Nesta seção você adicionará data de nascimento e cidade nos dados do usuário durante o registro, conforme mostrado na imagem a seguir.</span><span class="sxs-lookup"><span data-stu-id="e0b62-235">In this section you'll add birth date and home town to the user data during registration, as shown in the following image.</span></span>

![reg com cidade e Bday](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image35.png)

<span data-ttu-id="e0b62-237">Abra o *Models\IdentityModels.cs* de arquivo e adicionar propriedades de cidade de página inicial e a data de nascimento:</span><span class="sxs-lookup"><span data-stu-id="e0b62-237">Open the *Models\IdentityModels.cs* file and add birth date and home town properties:</span></span>

[!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample4.cs?highlight=3-4)]

<span data-ttu-id="e0b62-238">Abra o *Models\AccountViewModels.cs* de arquivo e o conjunto de propriedades de cidade de data e a página inicial de origem `ExternalLoginConfirmationViewModel`.</span><span class="sxs-lookup"><span data-stu-id="e0b62-238">Open the *Models\AccountViewModels.cs* file and the set birth date and home town properties in `ExternalLoginConfirmationViewModel`.</span></span>

[!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample5.cs?highlight=8-9)]

<span data-ttu-id="e0b62-239">Abra o *Controllers\AccountController.cs* de arquivos e adicionar código de cidade de página inicial e a data de nascimento do `ExternalLoginConfirmation` método de ação, conforme mostrado:</span><span class="sxs-lookup"><span data-stu-id="e0b62-239">Open the *Controllers\AccountController.cs* file and add code for birth date and home town in the `ExternalLoginConfirmation` action method as shown:</span></span>

[!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample6.cs?highlight=21-23)]

<span data-ttu-id="e0b62-240">Adicionar a data de nascimento e cidade para o *Views\Account\ExternalLoginConfirmation.cshtml* arquivo:</span><span class="sxs-lookup"><span data-stu-id="e0b62-240">Add birth date and home town to the *Views\Account\ExternalLoginConfirmation.cshtml* file:</span></span>

[!code-cshtml[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample7.cshtml?highlight=27-40)]

<span data-ttu-id="e0b62-241">Exclua o banco de dados de associação para registrar sua conta do Facebook com o aplicativo novamente e verifique se que você pode adicionar a nova data de nascimento e informações de perfil de cidade.</span><span class="sxs-lookup"><span data-stu-id="e0b62-241">Delete the membership database so you can again register your Facebook account with your application and verify you can add the new birth date and home town profile information.</span></span>

<span data-ttu-id="e0b62-242">De **Solution Explorer**, clique no **Mostrar todos os arquivos** ícone e o botão direito do mouse *adicionar\_Data\aspnet-MvcAuth -&lt;dateStamp&gt;. mdf* e clique em **excluir**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-242">From **Solution Explorer**, click the **Show All Files** icon, then right click *Add\_Data\aspnet-MvcAuth-&lt;dateStamp&gt;.mdf* and click **Delete**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image36.png)

<span data-ttu-id="e0b62-243">Do **ferramentas** menu, clique em **Gerenciador de pacote do NuGet**, em seguida, clique em **Package Manager Console** (PMC).</span><span class="sxs-lookup"><span data-stu-id="e0b62-243">From the **Tools** menu, click **NuGet Package Manger**, then click **Package Manager Console** (PMC).</span></span> <span data-ttu-id="e0b62-244">Digite os seguintes comandos no PMC.</span><span class="sxs-lookup"><span data-stu-id="e0b62-244">Enter the following commands in the PMC.</span></span>

1. <span data-ttu-id="e0b62-245">Enable-Migrations</span><span class="sxs-lookup"><span data-stu-id="e0b62-245">Enable-Migrations</span></span>
2. <span data-ttu-id="e0b62-246">Migração adicionar Init</span><span class="sxs-lookup"><span data-stu-id="e0b62-246">Add-Migration Init</span></span>
3. <span data-ttu-id="e0b62-247">Atualizar banco de dados</span><span class="sxs-lookup"><span data-stu-id="e0b62-247">Update-Database</span></span>

<span data-ttu-id="e0b62-248">Execute o aplicativo e usar o FaceBook e do Google para efetuar login e registrar alguns usuários.</span><span class="sxs-lookup"><span data-stu-id="e0b62-248">Run the application and use FaceBook and Google to log in and register some users.</span></span>

## <a name="examine-the-membership-data"></a><span data-ttu-id="e0b62-249">Examinar os dados de associação</span><span class="sxs-lookup"><span data-stu-id="e0b62-249">Examine the Membership Data</span></span>

<span data-ttu-id="e0b62-250">No **exibição** menu, clique em **Server Explorer**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-250">In the **View** menu, click **Server Explorer**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image37.png)

<span data-ttu-id="e0b62-251">Clique com botão direito **AspNetUsers** e clique em **Mostrar dados da tabela**.</span><span class="sxs-lookup"><span data-stu-id="e0b62-251">Right click **AspNetUsers** and click **Show Table Data**.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image38.png)

<span data-ttu-id="e0b62-252">O `HomeTown` e `BirthDate` campos são mostrados abaixo.</span><span class="sxs-lookup"><span data-stu-id="e0b62-252">The `HomeTown` and `BirthDate` fields are shown below.</span></span>

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image39.png)

<a id="off"></a>
## <a name="logging-off-your-app-and-logging-in-with-another-account"></a><span data-ttu-id="e0b62-253">Fazer logoff do seu aplicativo e fazer logon com outra conta</span><span class="sxs-lookup"><span data-stu-id="e0b62-253">Logging off your App and Logging in With Another Account</span></span>

<span data-ttu-id="e0b62-254">Se o logon ao seu aplicativo com o Facebook e, em seguida, faça logoff e tente fazer logon novamente com uma conta diferente do Facebook (usando o mesmo navegador), imediatamente efetuará à conta do Facebook anterior que você usou.</span><span class="sxs-lookup"><span data-stu-id="e0b62-254">If you log on to your app with Facebook,, and then log out and try to log in again with a different Facebook account (using the same browser), you will be immediately logged in to the previous Facebook account you used.</span></span> <span data-ttu-id="e0b62-255">Para usar outra conta, você precisa navegar para o Facebook e fazer logoff no Facebook.</span><span class="sxs-lookup"><span data-stu-id="e0b62-255">In order to use another account, you need to navigate to Facebook and log out at Facebook.</span></span> <span data-ttu-id="e0b62-256">A mesma regra se aplica a qualquer outro 3ª parte provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="e0b62-256">The same rule applies to any other 3rd party authentication provider.</span></span> <span data-ttu-id="e0b62-257">Como alternativa, você pode fazer logon com outra conta usando um navegador diferente.</span><span class="sxs-lookup"><span data-stu-id="e0b62-257">Alternatively, you can log in with another account by using a different browser.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e0b62-258">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="e0b62-258">Next Steps</span></span>

<span data-ttu-id="e0b62-259">Consulte [apresentando os provedores de segurança Yahoo e OAuth do LinkedIn para OWIN](http://www.jerriepelser.com/blog/introducing-the-yahoo-linkedin-oauth-security-providers-for-owin/) por Jerrie Pelser para obter instruções Yahoo e LinkedIn.</span><span class="sxs-lookup"><span data-stu-id="e0b62-259">See [Introducing the Yahoo and LinkedIn OAuth security providers for OWIN](http://www.jerriepelser.com/blog/introducing-the-yahoo-linkedin-oauth-security-providers-for-owin/) by Jerrie Pelser for Yahoo and LinkedIn instructions.</span></span> <span data-ttu-id="e0b62-260">Consulte do Jerrie muito botões de login social para ASP.NET MVC 5 obter habilitar logon social botões.</span><span class="sxs-lookup"><span data-stu-id="e0b62-260">See Jerrie's Pretty social login buttons for ASP.NET MVC 5 to get enable social login buttons.</span></span>

<span data-ttu-id="e0b62-261">Siga o tutorial [criar um aplicativo ASP.NET MVC com autenticação e o banco de dados SQL e implantar o serviço de aplicativo do Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data), que continua neste tutorial e mostra o seguinte:</span><span class="sxs-lookup"><span data-stu-id="e0b62-261">Follow my tutorial [Create an ASP.NET MVC app with auth and SQL DB and deploy to Azure App Service](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data), which continues this tutorial and shows the following:</span></span>

1. <span data-ttu-id="e0b62-262">Como implantar seu aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="e0b62-262">How to deploy your app to Azure.</span></span>
2. <span data-ttu-id="e0b62-263">Como proteger seu aplicativo com funções.</span><span class="sxs-lookup"><span data-stu-id="e0b62-263">How to secure you app with roles.</span></span>
3. <span data-ttu-id="e0b62-264">Como proteger seu aplicativo com o [RequireHttps](https://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) e [autorizar](https://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) filtros.</span><span class="sxs-lookup"><span data-stu-id="e0b62-264">How to secure your app with the [RequireHttps](https://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) and [Authorize](https://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) filters.</span></span>
4. <span data-ttu-id="e0b62-265">Como usar a API de associação para adicionar usuários e funções.</span><span class="sxs-lookup"><span data-stu-id="e0b62-265">How to use the membership API to add users and roles.</span></span>

<span data-ttu-id="e0b62-266">Deixe comentários em como você gostou neste tutorial e nós poderíamos melhorar.</span><span class="sxs-lookup"><span data-stu-id="e0b62-266">Please leave feedback on how you liked this tutorial and what we could improve.</span></span> <span data-ttu-id="e0b62-267">Você também pode solicitar novos tópicos em [Mostrar-Me como com código](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).</span><span class="sxs-lookup"><span data-stu-id="e0b62-267">You can also request new topics at [Show Me How With Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).</span></span> <span data-ttu-id="e0b62-268">Você ainda pode solicitar e vote em novos recursos a serem adicionadas ao ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e0b62-268">You can even ask for and vote on new features to be added to ASP.NET.</span></span> <span data-ttu-id="e0b62-269">Por exemplo, você poderá votar para uma ferramenta de [criar e gerenciar usuários e funções.](http://aspnet.uservoice.com/forums/41199-general-asp-net/suggestions/5646857-asp-net-identity-membership-db-tool-to-mangage-use)</span><span class="sxs-lookup"><span data-stu-id="e0b62-269">For example, you can vote for a tool to [create and manage users and roles.](http://aspnet.uservoice.com/forums/41199-general-asp-net/suggestions/5646857-asp-net-identity-membership-db-tool-to-mangage-use)</span></span>

<span data-ttu-id="e0b62-270">Para uma boa explicação de como funcionam os serviços de autenticação externa do ASP.NET, consulte de Robert McMurray [serviços de autenticação externos](https://asp.net/web-api/overview/security/external-authentication-services).</span><span class="sxs-lookup"><span data-stu-id="e0b62-270">For an good explanation of how ASP.NET External Authentication Services work, see Robert McMurray's [External Authentication Services](https://asp.net/web-api/overview/security/external-authentication-services).</span></span> <span data-ttu-id="e0b62-271">Artigo de Robert também entra em detalhes em como habilitar a autenticação da Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="e0b62-271">Robert's article also goes into detail in enabling Microsoft and Twitter authentication.</span></span> <span data-ttu-id="e0b62-272">Tom Dykstra do excelente [tutorial EF/MVC](../getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) mostra como trabalhar com o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e0b62-272">Tom Dykstra's excellent [EF/MVC tutorial](../getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) shows how to work with the Entity Framework.</span></span>