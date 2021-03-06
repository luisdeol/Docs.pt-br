---
title: "Cenários de reconhecimento não DI para proteção de dados no ASP.NET Core"
author: rick-anderson
description: "Saiba como dar suporte a cenários de proteção de dados em que você não pode ou não quiser usar um serviço fornecido pela injeção de dependência."
keywords: "ASP.NET Core, proteção de dados, injeção de dependência, DataProtectionProvider"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a7d8a962-80ff-48e3-96f6-8472b7ba2df9
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 375eecf649819dce8f1c2ba30e1cb6451d1c1253
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2017
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>Cenários de reconhecimento não DI para proteção de dados no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

O sistema de proteção de dados do ASP.NET Core é normalmente [adicionada a um contêiner de serviço](xref:security/data-protection/consumer-apis/overview) e consumido por componentes dependentes por meio de injeção de dependência (DI). No entanto, há casos em que isso não é possível ou desejado, especialmente ao importar o sistema para um aplicativo existente.

Para dar suporte a esses cenários, o [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) pacote fornece um tipo concreto, [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), que oferece uma maneira simples de usar a proteção de dados sem depender de injeção de dependência. O `DataProtectionProvider` tipo implementa [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider). Construindo `DataProtectionProvider` requer apenas fornecer um [DirectoryInfo](/dotnet/api/system.io.directoryinfo) instância para indicar onde as chaves de criptografia do provedor devem ser armazenadas, como mostrado no exemplo de código a seguir:

[!code-none[Main](non-di-scenarios/_static/nodisample1.cs)]

Por padrão, o `DataProtectionProvider` tipo concreto não criptografa a chave primas persisti-los antes do sistema de arquivos. Isso é para dar suporte a cenários onde os pontos de desenvolvedor para um compartilhamento de rede e o sistema de proteção de dados não é possível deduzir automaticamente um mecanismo de criptografia de chave apropriado em repouso.

Além disso, o `DataProtectionProvider` tipo concreto não [isolar os aplicativos](xref:security/data-protection/configuration/overview#per-application-isolation) por padrão. Todos os aplicativos usando o mesmo diretório principal podem compartilhar cargas, contanto que seus [finalidade parâmetros](xref:security/data-protection/consumer-apis/purpose-strings) corresponder.

O [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) construtor aceita um retorno de chamada de configuração opcional que pode ser usado para ajustar os comportamentos do sistema. O exemplo a seguir demonstra o isolamento de restauração com uma chamada explícita para [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname). O exemplo também demonstra como configurar o sistema para criptografar automaticamente chaves persistentes usando Windows DPAPI. Se o diretório aponta para um compartilhamento UNC, você poderá distribuir um certificado compartilhado por todos os computadores relevantes e para configurar o sistema para usar a criptografia baseada em certificado com uma chamada para [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).

[!code-none[Main](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> Instâncias de `DataProtectionProvider` tipo concreto são caros de criar. Se um aplicativo mantém várias instâncias desse tipo e se eles estão usando o mesmo diretório de armazenamento de chaves, pode afetar o desempenho do aplicativo. Se você usar o `DataProtectionProvider` tipo, recomendamos que você cria esse tipo de uma vez e reutilizá-la tanto quanto possível. O `DataProtectionProvider` tipo e todos os [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) instâncias criadas a partir dela são thread-safe para chamadores vários.
