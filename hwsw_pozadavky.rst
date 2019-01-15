.. DC3 documentation master file, created by
   sphinx-quickstart on Mon Aug  8 17:09:32 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Minimální HW a SW požadavky
===============================

Aplikace DC3 je webová aplikace běžící na webovém serveru IIS a v prostředí .NET a využívající relační databázi jako zdroj dat. 
Pro její běh je nutné splnit některé minimální HW a SW požadavky na straně databázových a aplikačních (webových) serverů.

.. contents:: V této části:
  :local:
  :depth: 1

Databázový server
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Momentálně jsou podporovány následující databázové stroje:

- Microsoft SQL Server 2008+
- Microfost SQL Azure
- Oracle 11g + 

V případě, že je použit databázový server Oracle, je nutné zajistit, aby uživatel **DC3** měl nagrantovaná následující oprávnění:

.. code-block:: sql

   GRANT CONNECT,RESOURCE,UNLIMITED TABLESPACE TO "DC3";
   GRANT CREATE VIEW TO "DC3";

Aplikační server
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Před vlastní instalací je nutné se ujistit, zda jsou na aplikačním serveru nainstalovány následující komponenty:

- Microsoft .NET Framework verze 4.6.2 (`odkaz ke stažení <https://www.microsoft.com/en-us/download/details.aspx?id=53344>`__)
- .NET Core 1.1.0 Windows Hosting installer (`odkaz ke stažení <https://aka.ms/dotnetcore_windowshosting_1_1_0>`__)
- SQLSysClrTypes-x64.msi (`odkaz ke stažení <http://go.microsoft.com/fwlink/?LinkID=188392&clcid=0x409>`__)
- ReportViewer.msi (`odkaz ke stažení <https://www.microsoft.com/en-us/download/details.aspx?id=45496>`__)
- Oracle Client 11g x64\* (`odkaz ke stažení <http://www.oracle.com/technetwork/database/enterprise-edition/downloads/112010-win64soft-094461.html>`__)

\*Pouze pokud je jako databázový server použit Oracle.

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



