---
title: Host ASP.NET Core no Linux com Nginx
description: "Descreve como configurar Nginx como um proxy reverso no Ubuntu 16.04 para encaminhar o tráfego HTTP para um aplicativo Web do ASP.NET Core em execução no Kestrel."
keywords: ASP.NET Core, Linux, nginx, Ubuntu, Proxy Reverso
author: rick-anderson
ms.author: riande
manager: wpickett
ms.date: 08/21/2017
ms.topic: article
ms.assetid: 1c33e576-33de-481a-8ad3-896b94fde0e3
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/linuxproduction
ms.openlocfilehash: 7c7b949fc922c605aa4554c158200a4123c4eb1c
ms.sourcegitcommit: fc98e93464ccf37d9904e89a71cdddbd4bbdb86a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="set-up-a-hosting-environment-for-aspnet-core-on-linux-with-nginx-and-deploy-to-it"></a><span data-ttu-id="f6260-104">Configurar um ambiente de hospedagem para o ASP.NET Core no Linux com Nginx e implantar nele</span><span class="sxs-lookup"><span data-stu-id="f6260-104">Set up a hosting environment for ASP.NET Core on Linux with Nginx, and deploy to it</span></span>

<span data-ttu-id="f6260-105">Por [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="f6260-105">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="f6260-106">Este guia explica como configurar um ambiente do ASP.NET Core pronto para produção em um servidor do Ubuntu 16.04.</span><span class="sxs-lookup"><span data-stu-id="f6260-106">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 Server.</span></span>

<span data-ttu-id="f6260-107">**Observação:** para Ubuntu 14.04, o supervisord é recomendado como uma solução para monitorar o processo do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f6260-107">**Note:** For Ubuntu 14.04, supervisord is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="f6260-108">O systemd não está disponível no Ubuntu 14.04.</span><span class="sxs-lookup"><span data-stu-id="f6260-108">systemd is not available on Ubuntu 14.04.</span></span> [<span data-ttu-id="f6260-109">Consulte a versão anterior deste documento</span><span class="sxs-lookup"><span data-stu-id="f6260-109">See previous version of this document</span></span>](https://github.com/aspnet/Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)

<span data-ttu-id="f6260-110">Este guia:</span><span class="sxs-lookup"><span data-stu-id="f6260-110">This guide:</span></span>

* <span data-ttu-id="f6260-111">Coloca um aplicativo ASP.NET Core existente em um servidor proxy reverso</span><span class="sxs-lookup"><span data-stu-id="f6260-111">Places an existing ASP.NET Core application behind a reverse proxy server</span></span>
* <span data-ttu-id="f6260-112">Configura o servidor proxy reverso para encaminhar solicitações ao servidor Web Kestrel</span><span class="sxs-lookup"><span data-stu-id="f6260-112">Sets up the reverse proxy server to forward requests to the Kestrel web server</span></span>
* <span data-ttu-id="f6260-113">Assegura que o aplicativo Web seja executado na inicialização como um daemon</span><span class="sxs-lookup"><span data-stu-id="f6260-113">Ensures the web application runs on startup as a daemon</span></span>
* <span data-ttu-id="f6260-114">Configura uma ferramenta de gerenciamento de processo para ajudar a reiniciar o aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="f6260-114">Configures a process management tool to help restart the web application</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f6260-115">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f6260-115">Prerequisites</span></span>

1. <span data-ttu-id="f6260-116">Acesso a um Servidor Ubuntu 16.04 com uma conta de usuário padrão com privilégio sudo</span><span class="sxs-lookup"><span data-stu-id="f6260-116">Access to an Ubuntu 16.04 Server with a standard user account with sudo privilege</span></span>
2. <span data-ttu-id="f6260-117">Um aplicativo ASP.NET Core existente</span><span class="sxs-lookup"><span data-stu-id="f6260-117">An existing ASP.NET Core application</span></span>

## <a name="copy-over-your-app"></a><span data-ttu-id="f6260-118">Copiar seu aplicativo</span><span class="sxs-lookup"><span data-stu-id="f6260-118">Copy over your app</span></span>

<span data-ttu-id="f6260-119">Executar `dotnet publish` do ambiente de desenvolvimento para empacotar um aplicativo em um diretório autocontido que pode ser executado no servidor.</span><span class="sxs-lookup"><span data-stu-id="f6260-119">Run `dotnet publish` from the dev environment to package an app into a self-contained directory that can run on the server.</span></span>

<span data-ttu-id="f6260-120">Copie o aplicativo do ASP.NET Core para o servidor usando qualquer ferramenta (SCP, FTP, etc.) que se integre ao fluxo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="f6260-120">Copy the ASP.NET Core app to the server using whatever tool (SCP, FTP, etc.) integrates into your workflow.</span></span> <span data-ttu-id="f6260-121">Teste o aplicativo, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="f6260-121">Test the app, for example:</span></span>
 - <span data-ttu-id="f6260-122">Da linha de comando, execute `dotnet yourapp.dll`</span><span class="sxs-lookup"><span data-stu-id="f6260-122">From the command line, run `dotnet yourapp.dll`</span></span>
 - <span data-ttu-id="f6260-123">Em um navegador, navegue até `http://<serveraddress>:<port>` para verificar se o aplicativo funciona no Linux.</span><span class="sxs-lookup"><span data-stu-id="f6260-123">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux.</span></span> 
 
## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="f6260-124">Configurar um servidor proxy reverso</span><span class="sxs-lookup"><span data-stu-id="f6260-124">Configure a reverse proxy server</span></span>

<span data-ttu-id="f6260-125">Um proxy reverso é uma configuração comum para atender a aplicativos Web dinâmicos.</span><span class="sxs-lookup"><span data-stu-id="f6260-125">A reverse proxy is a common setup for serving dynamic web applications.</span></span> <span data-ttu-id="f6260-126">Um proxy reverso encerra a solicitação HTTP e a encaminha para o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6260-126">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core application.</span></span>

### <a name="why-use-a-reverse-proxy-server"></a><span data-ttu-id="f6260-127">Por que usar um servidor proxy reverso?</span><span class="sxs-lookup"><span data-stu-id="f6260-127">Why use a reverse proxy server?</span></span>

<span data-ttu-id="f6260-128">O Kestrel é ótimo para servir conteúdo dinâmico do ASP.NET Core; no entanto, as partes de atendimento à Web não são tão ricas em recursos quanto servidores como IIS, Apache ou Nginx.</span><span class="sxs-lookup"><span data-stu-id="f6260-128">Kestrel is great for serving dynamic content from ASP.NET Core; however, the web serving parts aren’t as feature rich as servers like IIS, Apache, or Nginx.</span></span> <span data-ttu-id="f6260-129">Um servidor proxy reverso pode descarregar trabalho como servir conteúdo estático, armazenar solicitações em cache, compactar solicitações e terminar SSL do servidor HTTP.</span><span class="sxs-lookup"><span data-stu-id="f6260-129">A reverse proxy server can offload work like serving static content, caching requests, compressing requests, and SSL termination from the HTTP server.</span></span> <span data-ttu-id="f6260-130">Um servidor proxy reverso pode residir em um computador dedicado ou pode ser implantado junto com um servidor HTTP.</span><span class="sxs-lookup"><span data-stu-id="f6260-130">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="f6260-131">Para os fins deste guia, uma única instância de Nginx é usada.</span><span class="sxs-lookup"><span data-stu-id="f6260-131">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="f6260-132">Ela é executada no mesmo servidor, junto com o servidor HTTP.</span><span class="sxs-lookup"><span data-stu-id="f6260-132">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="f6260-133">De acordo com suas necessidades, você pode escolher uma configuração diferente.</span><span class="sxs-lookup"><span data-stu-id="f6260-133">Based on your requirements, you may choose a different setup.</span></span>

<span data-ttu-id="f6260-134">Já que as solicitações são encaminhadas pelo proxy reverso, use o middleware `ForwardedHeaders` do pacote `Microsoft.AspNetCore.HttpOverrides`.</span><span class="sxs-lookup"><span data-stu-id="f6260-134">Because requests are forwarded by reverse proxy, use the `ForwardedHeaders` middleware from the `Microsoft.AspNetCore.HttpOverrides` package.</span></span> <span data-ttu-id="f6260-135">Este middleware atualiza `Request.Scheme`, usando o cabeçalho `X-Forwarded-Proto`, de forma que URIs de redirecionamento e outras políticas de segurança funcionam corretamente.</span><span class="sxs-lookup"><span data-stu-id="f6260-135">This middleware updates `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="f6260-136">Ao configurar um servidor proxy reverso, o middleware de autenticação precisa de `UseForwardedHeaders` para ser executado primeiro.</span><span class="sxs-lookup"><span data-stu-id="f6260-136">When setting up a reverse proxy server, the authentication middleware needs `UseForwardedHeaders` to run first.</span></span> <span data-ttu-id="f6260-137">Essa ordenação garantirá que o middleware de autenticação possa consumir os valores afetados e gerar URIs de redirecionamento corretos.</span><span class="sxs-lookup"><span data-stu-id="f6260-137">This ordering ensures that the authentication middleware can consume the affected values and generate correct redirect URIs.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f6260-138">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f6260-138">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="f6260-139">Invoque o método `UseForwardedHeaders` (no método `Configure` de *Startup.cs*) antes de chamar `UseAuthentication` ou outro middleware de esquema de autenticação semelhante:</span><span class="sxs-lookup"><span data-stu-id="f6260-139">Invoke the `UseForwardedHeaders` method (in the `Configure` method of *Startup.cs*) before calling `UseAuthentication` or similar authentication scheme middleware:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f6260-140">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f6260-140">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="f6260-141">Invoque o método `UseForwardedHeaders` (no método `Configure` de *Startup.cs*) antes de chamar `UseIdentity` e `UseFacebookAuthentication` ou outro middleware de esquema de autenticação semelhante:</span><span class="sxs-lookup"><span data-stu-id="f6260-141">Invoke the `UseForwardedHeaders` method (in the `Configure` method of *Startup.cs*) before calling `UseIdentity` and `UseFacebookAuthentication` or similar authentication scheme middleware:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseIdentity();
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

### <a name="install-nginx"></a><span data-ttu-id="f6260-142">Instalar o Nginx</span><span class="sxs-lookup"><span data-stu-id="f6260-142">Install Nginx</span></span>

```bash
sudo apt-get install nginx
```

> [!NOTE]
> <span data-ttu-id="f6260-143">Se você planeja instalar os módulos de Nginx opcionais, pode ser solicitado que você compile o Nginx da origem.</span><span class="sxs-lookup"><span data-stu-id="f6260-143">If you plan to install optional Nginx modules, you may be required to build Nginx from source.</span></span>

<span data-ttu-id="f6260-144">Use `apt-get` para instalar o Nginx.</span><span class="sxs-lookup"><span data-stu-id="f6260-144">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="f6260-145">O instalador cria um script de inicialização System V que executa o Nginx como daemon na inicialização do sistema.</span><span class="sxs-lookup"><span data-stu-id="f6260-145">The installer creates a System V init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="f6260-146">Já que Nginx foi instalado pela primeira vez, inicie-o explicitamente executando:</span><span class="sxs-lookup"><span data-stu-id="f6260-146">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="f6260-147">Verifique se um navegador exibe a página de aterrissagem padrão do Nginx.</span><span class="sxs-lookup"><span data-stu-id="f6260-147">Verify a browser displays the default landing page for Nginx.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="f6260-148">Configurar o Nginx</span><span class="sxs-lookup"><span data-stu-id="f6260-148">Configure Nginx</span></span>

<span data-ttu-id="f6260-149">Para configurar o Nginx como um proxy inverso para encaminhar solicitações para o nosso aplicativo ASP.NET Core, modifique `/etc/nginx/sites-available/default`.</span><span class="sxs-lookup"><span data-stu-id="f6260-149">To configure Nginx as a reverse proxy to forward requests to our ASP.NET Core application, modify `/etc/nginx/sites-available/default`.</span></span> <span data-ttu-id="f6260-150">Abra-o em um editor de texto arquivo e substitua o conteúdo pelo mostrado a seguir:</span><span class="sxs-lookup"><span data-stu-id="f6260-150">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen 80;
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $http_host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

<span data-ttu-id="f6260-151">Esse arquivo de configuração do Nginx encaminha tráfego público de entrada da porta `80` para a porta `5000`.</span><span class="sxs-lookup"><span data-stu-id="f6260-151">This Nginx configuration file forwards incoming public traffic from port `80` to port `5000`.</span></span>

<span data-ttu-id="f6260-152">Depois que você terminar de fazer alterações em sua configuração de Nginx, você pode executar `sudo nginx -t` para verificar a sintaxe dos seus arquivos de configuração.</span><span class="sxs-lookup"><span data-stu-id="f6260-152">Once you have completed making changes to your Nginx configuration, you can run `sudo nginx -t` to verify the syntax of your configuration files.</span></span> <span data-ttu-id="f6260-153">Se o teste do arquivo de configuração for bem-sucedido, você poderá pedir ao Nginx que acompanhe as alterações executando `sudo nginx -s reload`.</span><span class="sxs-lookup"><span data-stu-id="f6260-153">If the configuration file test is successful, you can ask Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

## <a name="monitoring-our-application"></a><span data-ttu-id="f6260-154">Monitorando nosso aplicativo</span><span class="sxs-lookup"><span data-stu-id="f6260-154">Monitoring our application</span></span>

<span data-ttu-id="f6260-155">O Nginx agora está configurado para encaminhar solicitações feitas a `http://yourhost:80` ao aplicativo ASP.NET Core em execução no Kestrel em `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="f6260-155">Nginx is now setup to forward requests made to `http://yourhost:80` on to the ASP.NET Core application running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="f6260-156">No entanto, o Nginx não está configurado para gerenciar o processo do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f6260-156">However, Nginx is not set up to manage the Kestrel process.</span></span> <span data-ttu-id="f6260-157">Você pode usar *systemd* e criar um arquivo de serviço para iniciar e monitorar o aplicativo Web subjacente.</span><span class="sxs-lookup"><span data-stu-id="f6260-157">You can use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="f6260-158">*systemd* é um sistema de inicialização que fornece muitos recursos poderosos para iniciar, parar e gerenciar processos.</span><span class="sxs-lookup"><span data-stu-id="f6260-158">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="f6260-159">Criar o arquivo de serviço</span><span class="sxs-lookup"><span data-stu-id="f6260-159">Create the service file</span></span>

<span data-ttu-id="f6260-160">Crie o arquivo de definição de serviço:</span><span class="sxs-lookup"><span data-stu-id="f6260-160">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

<span data-ttu-id="f6260-161">A seguir, um exemplo de arquivo de serviço para nosso aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f6260-161">The following is an example service file for our application:</span></span>

```ini
[Unit]
Description=Example .NET Web API Application running on Ubuntu

[Service]
WorkingDirectory=/var/aspnetcore/hellomvc
ExecStart=/usr/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
Restart=always
RestartSec=10  # Restart service after 10 seconds if dotnet service crashes
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="f6260-162">**Observação:** se o usuário *www-data* não é usado pela sua configuração, o usuário definido aqui deve ser criado primeiro e a propriedade adequada dos arquivos deve ser concedida a ele.</span><span class="sxs-lookup"><span data-stu-id="f6260-162">**Note:** If the user *www-data* is not used by your configuration, the user defined here must be created first and given proper ownership for files.</span></span>

<span data-ttu-id="f6260-163">Salve o arquivo e habilite o serviço.</span><span class="sxs-lookup"><span data-stu-id="f6260-163">Save the file, and enable the service.</span></span>

```bash
systemctl enable kestrel-hellomvc.service
```

<span data-ttu-id="f6260-164">Inicie o serviço e verifique se ele está em execução.</span><span class="sxs-lookup"><span data-stu-id="f6260-164">Start the service and verify that it is running.</span></span>

```
systemctl start kestrel-hellomvc.service
systemctl status kestrel-hellomvc.service

● kestrel-hellomvc.service - Example .NET Web API Application running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-hellomvc.service
            └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

<span data-ttu-id="f6260-165">Com o proxy reverso configurado e o Kestrel gerenciado por systemd, o aplicativo Web está totalmente configurado e pode ser acessado por meio de um navegador no computador local em `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="f6260-165">With the reverse proxy configured and Kestrel managed through systemd, the web application is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="f6260-166">Ele também é acessível por meio de um computador remoto, bloqueando qualquer firewall que o possa estar bloqueando.</span><span class="sxs-lookup"><span data-stu-id="f6260-166">It is also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="f6260-167">Inspecionando os cabeçalhos de resposta, o cabeçalho `Server` mostra o aplicativo do ASP.NET Core sendo servido por Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f6260-167">Inspecting the response headers, the `Server` header shows the ASP.NET Core application being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a><span data-ttu-id="f6260-168">Exibindo logs</span><span class="sxs-lookup"><span data-stu-id="f6260-168">Viewing logs</span></span>

<span data-ttu-id="f6260-169">Já que o aplicativo Web usando Kestrel é gerenciado usando `systemd`, todos os eventos e os processos são registrados em um diário centralizado.</span><span class="sxs-lookup"><span data-stu-id="f6260-169">Since the web application using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="f6260-170">No entanto, esse diário contém todas as entradas para todos os serviços e processos gerenciados pelo `systemd`.</span><span class="sxs-lookup"><span data-stu-id="f6260-170">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="f6260-171">Para exibir os itens específicos de `kestrel-hellomvc.service`, use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f6260-171">To view the `kestrel-hellomvc.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-hellomvc.service
```

<span data-ttu-id="f6260-172">Para obter mais filtragem, opções de tempo como `--since today`, `--until 1 hour ago` ou uma combinação desses fatores pode reduzir a quantidade de entradas retornadas.</span><span class="sxs-lookup"><span data-stu-id="f6260-172">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-our-application"></a><span data-ttu-id="f6260-173">Protegendo nosso aplicativo</span><span class="sxs-lookup"><span data-stu-id="f6260-173">Securing our application</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="f6260-174">Habilitar AppArmor</span><span class="sxs-lookup"><span data-stu-id="f6260-174">Enable AppArmor</span></span>

<span data-ttu-id="f6260-175">O LSM (Módulos de Segurança do Linux) é uma estrutura que é parte do kernel do Linux desde o Linux 2.6.</span><span class="sxs-lookup"><span data-stu-id="f6260-175">Linux Security Modules (LSM) is a framework that is part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="f6260-176">O LSM dá suporte a diferentes implementações de módulos de segurança.</span><span class="sxs-lookup"><span data-stu-id="f6260-176">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="f6260-177">O [AppArmor](https://wiki.ubuntu.com/AppArmor) é um LSM que implementa um sistema de controle de acesso obrigatório que permite restringir o programa a um conjunto limitado de recursos.</span><span class="sxs-lookup"><span data-stu-id="f6260-177">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="f6260-178">Verifique se o AppArmor está habilitado e configurado corretamente.</span><span class="sxs-lookup"><span data-stu-id="f6260-178">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configuring-our-firewall"></a><span data-ttu-id="f6260-179">Configurando nosso firewall</span><span class="sxs-lookup"><span data-stu-id="f6260-179">Configuring our firewall</span></span>

<span data-ttu-id="f6260-180">Feche todas as portas externas que não estão em uso.</span><span class="sxs-lookup"><span data-stu-id="f6260-180">Close off all external ports that are not in use.</span></span> <span data-ttu-id="f6260-181">Firewall descomplicado (ufw) fornece um front-end para `iptables` fornecendo uma interface de linha de comando para configurar o firewall.</span><span class="sxs-lookup"><span data-stu-id="f6260-181">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a command line interface for configuring the firewall.</span></span> <span data-ttu-id="f6260-182">Verifique se `ufw` está configurado para permitir o tráfego em quaisquer portas que você precise.</span><span class="sxs-lookup"><span data-stu-id="f6260-182">Verify that `ufw` is configured to allow traffic on any ports you need.</span></span>

```bash
sudo apt-get install ufw
sudo ufw enable

sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

### <a name="securing-nginx"></a><span data-ttu-id="f6260-183">Protegendo o Nginx</span><span class="sxs-lookup"><span data-stu-id="f6260-183">Securing Nginx</span></span>

<span data-ttu-id="f6260-184">A distribuição padrão do Nginx não habilita o SSL.</span><span class="sxs-lookup"><span data-stu-id="f6260-184">The default distribution of Nginx doesn't enable SSL.</span></span> <span data-ttu-id="f6260-185">Para habilitar recursos de segurança adicionais, compile da origem.</span><span class="sxs-lookup"><span data-stu-id="f6260-185">To enable additional security features, build from source.</span></span>

#### <a name="download-the-source-and-install-the-build-dependencies"></a><span data-ttu-id="f6260-186">Baixe a origem e instale as dependências de build</span><span class="sxs-lookup"><span data-stu-id="f6260-186">Download the source and install the build dependencies</span></span>

```bash
# Install the build dependencies
sudo apt-get update
sudo apt-get install build-essential zlib1g-dev libpcre3-dev libssl-dev libxslt1-dev libxml2-dev libgd2-xpm-dev libgeoip-dev libgoogle-perftools-dev libperl-dev

# Download nginx 1.10.0 or latest
wget http://www.nginx.org/download/nginx-1.10.0.tar.gz
tar zxf nginx-1.10.0.tar.gz
```

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="f6260-187">Alterar o nome da resposta do Nginx</span><span class="sxs-lookup"><span data-stu-id="f6260-187">Change the Nginx response name</span></span>

<span data-ttu-id="f6260-188">Edite *src/http/ngx_http_header_filter_module.c*:</span><span class="sxs-lookup"><span data-stu-id="f6260-188">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```c
static char ngx_http_server_string[] = "Server: Your Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Your Web Server" CRLF;
```

#### <a name="configure-the-options-and-build"></a><span data-ttu-id="f6260-189">Configurar as opções e compilar</span><span class="sxs-lookup"><span data-stu-id="f6260-189">Configure the options and build</span></span>

<span data-ttu-id="f6260-190">A biblioteca PCRE é necessária para expressões regulares.</span><span class="sxs-lookup"><span data-stu-id="f6260-190">The PCRE library is required for regular expressions.</span></span> <span data-ttu-id="f6260-191">Expressões regulares são usadas na diretiva local para o ngx_http_rewrite_module.</span><span class="sxs-lookup"><span data-stu-id="f6260-191">Regular expressions are used in the location directive for the ngx_http_rewrite_module.</span></span> <span data-ttu-id="f6260-192">O http_ssl_module adiciona suporte a protocolo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f6260-192">The http_ssl_module adds HTTPS protocol support.</span></span>

<span data-ttu-id="f6260-193">Considere o uso de um firewall de aplicativo Web como *ModSecurity* para proteger seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f6260-193">Consider using a web application firewall like *ModSecurity* to harden your application.</span></span>

```bash
./configure
--with-pcre=../pcre-8.38
--with-zlib=../zlib-1.2.8
--with-http_ssl_module
--with-stream
--with-mail=dynamic
```

#### <a name="configure-ssl"></a><span data-ttu-id="f6260-194">Configurar o SSL</span><span class="sxs-lookup"><span data-stu-id="f6260-194">Configure SSL</span></span>

* <span data-ttu-id="f6260-195">Configure o servidor para ouvir tráfego HTTPS na porta `443` especificando um certificado válido emitido por uma AC (autoridade de certificação) confiável.</span><span class="sxs-lookup"><span data-stu-id="f6260-195">Configure your server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="f6260-196">Aprimore sua segurança, empregando algumas das práticas descritas no arquivo */etc/nginx/nginx.conf* a seguir.</span><span class="sxs-lookup"><span data-stu-id="f6260-196">Harden your security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="f6260-197">Exemplos incluem a escolha de uma criptografia mais forte e o redirecionamento de todo o tráfego por meio de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f6260-197">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="f6260-198">Adicionar um cabeçalho `HTTP Strict-Transport-Security` (HSTS) garante que todas as solicitações subsequentes feitas pelo cliente sejam apenas via HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f6260-198">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS only.</span></span>

* <span data-ttu-id="f6260-199">Não adicione o cabeçalho de Segurança de Transporte Estrito nem escolha um `max-age` apropriado se você planeja desabilitar o SSL no futuro.</span><span class="sxs-lookup"><span data-stu-id="f6260-199">Do not add the Strict-Transport-Security header or chose an appropriate `max-age` if you plan to disable SSL in the future.</span></span>

<span data-ttu-id="f6260-200">Adicione o arquivo de configuração */etc/nginx/proxy.conf*:</span><span class="sxs-lookup"><span data-stu-id="f6260-200">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[Main](linuxproduction/proxy.conf)]

<span data-ttu-id="f6260-201">Edite o arquivo de configuração */etc/nginx/nginx.conf*.</span><span class="sxs-lookup"><span data-stu-id="f6260-201">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="f6260-202">O exemplo contém ambas as seções `http` e `server` em um arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="f6260-202">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[Main](../publishing/linuxproduction/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="f6260-203">Proteger o Nginx de clickjacking</span><span class="sxs-lookup"><span data-stu-id="f6260-203">Secure Nginx from clickjacking</span></span>
<span data-ttu-id="f6260-204">Clickjacking é uma técnica mal-intencionada para coletar cliques de um usuário infectado.</span><span class="sxs-lookup"><span data-stu-id="f6260-204">Clickjacking is a malicious technique to collect an infected user's clicks.</span></span> <span data-ttu-id="f6260-205">O clickjacking engana a vítima (visitante) fazendo-a clicar em um site infectado.</span><span class="sxs-lookup"><span data-stu-id="f6260-205">Clickjacking tricks the victim (visitor) into clicking on an infected site.</span></span> <span data-ttu-id="f6260-206">Use X-FRAME-OPTIONS para proteger o site.</span><span class="sxs-lookup"><span data-stu-id="f6260-206">Use X-FRAME-OPTIONS to secure your site.</span></span>

<span data-ttu-id="f6260-207">Edite o arquivo *nginx.conf*:</span><span class="sxs-lookup"><span data-stu-id="f6260-207">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="f6260-208">Adicione a linha `add_header X-Frame-Options "SAMEORIGIN";` e salve o arquivo, depois reinicie o Nginx.</span><span class="sxs-lookup"><span data-stu-id="f6260-208">Add the line `add_header X-Frame-Options "SAMEORIGIN";` and save the file, then restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="f6260-209">Detecção de tipo MIME</span><span class="sxs-lookup"><span data-stu-id="f6260-209">MIME-type sniffing</span></span>

<span data-ttu-id="f6260-210">Esse cabeçalho evita que a maioria dos navegadores faça detecção MIME de uma resposta distante do tipo de conteúdo declarado, visto que o cabeçalho instrui o navegador para não substituir o tipo de conteúdo de resposta.</span><span class="sxs-lookup"><span data-stu-id="f6260-210">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="f6260-211">Com a opção `nosniff`, se o servidor informa que o conteúdo é "text/html", o navegador renderiza-a como "text/html".</span><span class="sxs-lookup"><span data-stu-id="f6260-211">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="f6260-212">Edite o arquivo *nginx.conf*:</span><span class="sxs-lookup"><span data-stu-id="f6260-212">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="f6260-213">Adicione a linha `add_header X-Content-Type-Options "nosniff";` e salve o arquivo, depois reinicie o Nginx.</span><span class="sxs-lookup"><span data-stu-id="f6260-213">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>