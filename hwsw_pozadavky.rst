.. DC3 documentation master file, created by
   sphinx-quickstart on Mon Aug  8 17:09:32 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Minimální HW a SW požadavky
===============================

Aplikace DC3 je webová aplikace běžící na webovém serveru IIS a v prostředí .NET a využívající relační databázi jako zdroj dat. 
Pro její běh je nutné splnit některé minimální HW a SW požadavky na straně databázových a aplikačních (webových) serverů.

.. note:: Aplikaci DC3 je možné provozovat jak na jednom serveru společně s databází MS SQL, tak na oddělených serverech pro databázi a aplikaci. Záleží na rozhodnutí zákazníka, jak chce mít řešenou architekturu a také na předpokládaném zatížení. U zákazníků, kde se předpokládá vyšší zátěž a přenosy dat je doporučeno mít DB odděleně od aplikačního serveru. 

.. contents:: V této části:
  :local:
  :depth: 1

Databázový server
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

HW
----------
Doporučená konfigurace pro databázový server je

 - 4 CPU
 - RAM 6 GB a vyšší
 - HDD min. 100 GB (DB + zálohy)
 
SW
---------
Momentálně jsou podporovány následující databázové stroje:

- Microsoft SQL Server 2016+
- Microfost SQL Azure
- Oracle 12c+ 

V případě, že je použit databázový server Oracle, je nutné zajistit, aby uživatel **DC3** měl nagrantovaná následující oprávnění:

.. code-block:: sql

   GRANT CONNECT,RESOURCE,UNLIMITED TABLESPACE TO "DC3";
   GRANT CREATE VIEW TO "DC3";

Aplikační server
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

HW
----------
Doporučená konfigurace pro aplikační server je

 - 4 CPU
 - RAM 6 GB a vyšší
 - HDD min. 60 GB
 - OS MS Windows Server 2016 a vyšší

SW
---------
Před vlastní instalací je nutné se ujistit, zda jsou na aplikačním serveru nainstalovány následující komponenty:

- .NET Core 5.0.7 Windows Hosting installer (`odkaz ke stažení <https://dotnet.microsoft.com/download/dotnet/thank-you/runtime-aspnetcore-5.0.7-windows-hosting-bundle-installer>`__)

.. note:: V případě, že je v aplikaci DC3 využíván modul pro přímé skenování dokumentů **DC.DC3.Worker.ScanProcess** je třeba mít na aplikačním serveru nainstalovaný také plný .NET Framework 4.7.2 (`odkaz ke stažení <https://dotnet.microsoft.com/download/thank-you/net472>`__).

Aplikační server dále musí být nakonfigurován v roli **Web server (IIS)**. V této roli je třeba zapnout minimálně následující balíčky:

- Web Server

  - Common HTTP Features
  
    - Default Document
    - Directory Browsing
    - HTTP Errors
    - Static Content
    - HTTP Redirection
    
  - Health and Diagnostics
  
    - HTTP Logging
    - Logging Tools
    - Request Monitor
    
  - Performance
  
    - Static Content Compression
        
  - Security
  
    - Request Filtering
    - Client Certificate Mapping Authentication    
    - IIS Client Certificate Mapping Authentication     
    - Windows Authentication      
    
  - Application Development
                                 
    - .NET Extensibility 4.5
    - Application Initialization     
    - ASP.NET 4.5
    - ISAPI Extensions
    - ISAPI Filters
    - WebSocket Protocol    
    
- Management Tools

  - IIS Management Console

.. image:: /img/RoleIIS.png  




