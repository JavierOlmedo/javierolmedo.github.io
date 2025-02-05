---
layout: single
title: '<span class="post">Obtener información con The Harvester</span>'
excerpt: "Ejemplo de cómo obtener información emails, subdominos, etc sobre una determinada empresa u organismo con la herramienta pasiva The Harvester"
date: 2015-03-09
header:
  teaser: /assets/images/obtener-informacion-con-the-harvester/obtener-informacion-con-the-harvester_banner.png
  teaser_home_page: true
  icon: /assets/images/post.svg
categories:
  - tool
  - osint
tags:
  - thehardvester
  - tool
  - github
  - osint
  - email
  - domain
toc: true
toc_label: "Content"
toc_sticky: true
show_time: true
lang: es
---

<!-- BANNER -->
<a href="/assets/images/obtener-informacion-con-the-harvester/obtener-informacion-con-the-harvester_banner.png">
    <img src="/assets/images/obtener-informacion-con-the-harvester/obtener-informacion-con-the-harvester_banner.png" alt="Obtener información con The Harvester">
</a>

Antes de realizar cualquier intento de penetrar en un sistema informático, es necesario la **recolección de información** sobre la empresa u organismo que se quiere penetrar o auditar, intentar cualquier ataque sin tener información sería **"ir dando palos de ciego"** sin sentido, aunque parezca que es una pérdida de tiempo en un principio, durante la fase de explotación echaremos de menos no haber recolectado los suficientes datos.

Siempre suelo utilizar una **herramienta esencial**, [The Harvester](https://github.com/laramies/theHarvester).

## ¿Qué es The Harvester?

Es una herramienta para conseguir información sobre **emails, subdominios, hosts, nombres de empleados, puertos abiertos, banners, etc.** desde fuentes públicas como son los motores de búsqueda, servidores PGP, la red social LinkedIn y la base de datos de SHODAN (Buscador parecido a Google pero con la diferencia que no indexa contenido, si no que registra cualquier dispositivo conectado a Internet).


<a href="/assets/images/obtener-informacion-con-the-harvester/obtener-informacion-con-the-harvester_001.png"><img src="/assets/images/obtener-informacion-con-the-harvester/obtener-informacion-con-the-harvester_001.png"></a>
Ayuda de la herramientas TheHarvester

## ¿Cómo podemos utilizarla?

Podemos **descargar la herramienta** desde [aquí](https://github.com/laramies/theHarvester). Para utilizarla os dejo la sintaxis:

```
*******************************************************************
*                                                                 *
* | |_| |__   ___    /\  /\__ _ _ ____   _____  ___| |_ ___ _ __  *
* | __| '_ \ / _ \  / /_/ / _` | '__\ \ / / _ \/ __| __/ _ \ '__| *
* | |_| | | |  __/ / __  / (_| | |   \ V /  __/\__ \ ||  __/ |    *
*  \__|_| |_|\___| \/ /_/ \__,_|_|    \_/ \___||___/\__\___|_|    *
*                                                                 *
* theHarvester Ver. 3.0.6                                         *
* Coded by Christian Martorella                                   *
* Edge-Security Research                                          *
* cmartorella@edge-security.com                                   *
*******************************************************************

   -d: Domain to search or company name
   -b: data source: baidu, bing, bingapi, censys, crtsh, dogpile,
                    google, google-certificates, googleCSE, googleplus, google-profiles,
                    hunter, linkedin, netcraft, pgp, threatcrowd,
                    twitter, vhost, virustotal, yahoo, all
   -g: use Google dorking instead of normal Google search
   -s: start in result number X (default: 0)
   -v: verify host name via DNS resolution and search for virtual hosts
   -f: save the results into an HTML and XML file (both)
   -n: perform a DNS reverse query on all ranges discovered
   -c: perform a DNS brute force for the domain name
   -t: perform a DNS TLD expansion discovery
   -e: use this DNS server
   -p: port scan the detected hosts and check for Takeovers (80,443,22,21,8080)
   -l: limit the number of results to work with(Bing goes from 50 to 50 results,
        Google 100 to 100, and PGP doesn't use this option)
   -h: use SHODAN database to query discovered hosts

Examples:
         theharvester -d microsoft.com -l 500 -b google -f myresults.html
         theharvester -d microsoft.com -b pgp, virustotal
         theharvester -d microsoft -l 200 -b linkedin
         theharvester -d microsoft.com -l 200 -g -b google
         theharvester -d apple.com -b googleCSE -l 500 -s 300
         theharvester -d cornell.edu -l 100 -b bing -h
```

Para poner un ejemplo, vamos a buscar información sobre Microsoft, utilizando como fuente Google, para no obtener demasiados resultados, limitaremos la búsqueda a solo 10 emails, **procederíamos de la siguiente manera**:

```shell
theharvester -d domain -l 10 -b google
```

Consiguiendo los siguientes datos:

<a href="/assets/images/obtener-informacion-con-the-harvester/obtener-informacion-con-the-harvester_002.png"><img src="/assets/images/obtener-informacion-con-the-harvester/obtener-informacion-con-the-harvester_002.png"></a>
Obtener emails de Microsoft con The Harvester

Una vez obtenidos estos datos, **ya tenemos un punto de apoyo** para empezar nuestro ataque (o seguir buscando otros datos de interés como veremos más adelante), también podríamos buscar un poquito más, para intentar relacionar uno de los correos obtenido con un alto cargo en la empresa (LinkedIn nos vendrá muy bien, así que si no tienes cuenta créate una) e intentar algo de ingeniería social.

Bueno, hasta aquí la entrada de hoy, si te ha gustado **no olvides compartirlo**, compartir conocimiento es uno de los principales motivos por los cuales he creado el blog.

Saludos!!