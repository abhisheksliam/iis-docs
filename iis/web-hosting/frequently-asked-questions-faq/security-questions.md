---
title: "Security Questions | Microsoft Docs"
author: walterov
description: "For certain workloads, Full trust may be required (e.g., making unmanaged calls, using reflection, etc.). For most web apps, it is typically not necessary. M..."
ms.author: iiscontent
manager: soshir
ms.date: 12/22/2007
ms.topic: article
ms.assetid: 1ab1535f-bb97-4008-b657-efd429723624
ms.technology: iis-hosting
ms.prod: iis
msc.legacyurl: /learn/web-hosting/frequently-asked-questions-faq/security-questions
msc.type: authoredcontent
---
Security Questions
====================
by [Walter Oliver](https://github.com/walterov)

#### Should Full Trust be allowed?

For certain workloads, Full trust may be required (e.g., making unmanaged calls, using reflection, etc.). For most web apps, it is typically not necessary. Microsoft recommends using Medium Trust (at the Server level). Using feature delegation, you can allow your individual site owners to override your "default" trust level and go to Full Trust or to a custom trust level that is derived from Medium trust. That way, the site owners will be making an explicit decision.

#### Can we use Access databases?

Yes - like in IIS 6.0, you will need to create custom trust derived from medium trust - or use full trust.

#### Should we change the anonymous user? (how safe is the predefined user?)

There is no easy answer to this question. IIS provides an anonymous user token or even impersonates the thread before it hands control to scripting technologies like ASP, PHP or ASP.NET. Once that happens IIS is out of the picture and how scripting technologies use this token is not up to IIS.

Classic ASP, for example, uses the impersonated thread. The Classic ASP sandbox is built on the assumption that no Classic ASP API can un-impersonate the thread running as the authenticated user.

ASP.NET, on the other hand, does not use the anonymous user by default. All of its code runs un-impersonated (i.e., as the process identity). Of course, you can turn impersonation on in web.config files (&lt;identity&gt; section). But ASP.NET is a powerful scripting technology and spawning a new thread (runs un-impersonated by default) or reverting the current thread back to the process identity is not hard to do. Even in medium trust there are ways to do it and it is not known if anyone ever evaluated how secure the medium trust ASP.NET sandbox is with regards to thread impersonation. PHP is unexplored territory in this regard, too.

To reiterate: IIS has no way to prevent scripting technologies from reverting back to the process identity or spawning new threads as the process identity.

The benefits of having an anonymous user is that it is another layer of defense. Malicious code might not have found a way (yet) to run its code as the process identity. IIS code which runs as the process identity has to have write access to certain resources (e.g. the IIS compression cache, Temporary ASP.Net files etc.). If the malicious code does not know how to revert to the process identity, it cannot interfere with resources that allow write access for the process identity. Custom code running as the anonymous users does not need these permissions.

#### If we should not allow full trust and use a modified medium trust config, what is the recommended config?

We don't have a "recommended config" that accommodates access DB. What you have in IIS 6.0 should still apply in IIS 7.0 because ASP.NET has not changed (yet).

If you need to configure a custom trust policy, there are a number of good articles on web:

[https://msdn.microsoft.com/en-us/library/wyts434y.aspx](https://msdn.microsoft.com/en-us/library/wyts434y.aspx)

[https://msdn.microsoft.com/en-us/library/ms998341.aspx#paght000020\_step3](https://msdn.microsoft.com/en-us/library/ms998341.aspx#paght000020_step3)

[https://msdn.microsoft.com/en-us/library/ms998326.aspx#paght000017\_step2](https://msdn.microsoft.com/en-us/library/ms998326.aspx#paght000017_step2)