.. DC3 documentation master file, created by
   sphinx-quickstart on Mon Aug  8 17:09:32 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Instalace DC3 na produkční prostředí
=====================================

Následující text popisuje, jakým způsobem provést prvotní čistou instalaci DC3 do produkčního prostředí včetně vygenerování prázné databáze. 
V případě, že potřebujete provést aktualizaci aplikace nebo obnovení ze zálohy, pokračujte na kapitolu :doc:`Aktualizace a obnovení DC3 <aktualizace-a-obnoveni>` 

.. note:: Pokud si nejste jisti svým postupem nebo v případě složitějších instalací, kontaktujte vždy konzultanta společnosti DATACENTRUM systems & consulting a.s. 

.. contents:: V této části:
  :local:
  :depth: 1
  

Instalační soubory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Překlad zdrojových kódů aplikace zajišťují automatizované buildy v prostředí Microsoft Azure DevOps, jejichž výsledkem je zkompilovaný adresář **DC3**, obsahující instalační soubory.

- Celý obsah adresáře zkopírujte na disk na produkčním serveru. Obvykle **C:\\Program Files\\DataCentrum\\DC3**

.. image:: /img/SetupFiles.PNG

Certifikáty
-------------

Dále je nutné nakopírovat na produkční server adresář s certifikáty, které jsou použity pro běh interní služby zajišťující práci s databází.
Instalace cetifikátů je popsána v následující kapitole

.. image:: /img/CertFiles.PNG
                 
Instalace DC3 Worker Service
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Součástí DC3 je Windows služba, která zajišťuje veškeré úlohy běžící na pozadí systému např. spouštění plánovaných úloh (DbModulů) nebo rozesílání notifikací.
Službu je nutné nainstalovat do systému pomocí Utility **SC.exe**.

- Spustit tak příkazový řádek s právy **správce**.

.. image:: /img/CmdAdmin.PNG

- V příkazovém řádku přejít do adresáře s **nakopírovanými soubory DC3**.

- Pomocí **SC.exe** nainstalovat službu **DC.DC3.Service.exe** umístěnou v instalačním adresáři DC3.

- Instalaci provedeme zadáním příkazu ve tvaru **sc create DC3WorkerHost binPath= "C:\\Program Files\DataCentrum\DC3\DC.DC3.Service.exe" DisplayName= "DC3 Worker Host Service"**

.. image:: /img/SC.PNG

- Služba byla úspěšně naistalována a objeví se v seznamu Windows služeb.

.. image:: /img/Services.PNG

- Pomocí pravého tlačítka -> Vlastnosti ještě nastavit, aby se služba spouštěla automaticky (výchozí je Manual).

.. image:: /img/Automatic.PNG

.. _pripojeni_label:

Konfigurace připojení k DB
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Aplikace DC3 komunikuje s datábází přes ADO.NET. Pro zprovoznění této komunikace je třeba v aplikaci nastavit správně připojovací řetězec k hlavní databázi.
Dále může DC3 provádět import / export dat z / do jiných systémů. V takovém případě, je nutné zadat připojovací řetězce i k těmto systémům.

- Otevřít soubor **appsettings.json**
- V sekci **DefaultConnection** zadat správný připojovací řetězec k DB
- Případně upravit další sekce typu connection
- Připojovací řetězce k databázím se liší svým zápisem v závislosti na typu použité databáze (**MS SQL** nebo **Oracle**).

Oracle:

.. image:: /img/Appsettings_oracle.PNG

MS SQL:

.. image:: /img/Appsettings_sql.PNG

Zašifrování připojovacího řetězce
------------------------------------

Ve výchozím stavu může připojovací řetězec v sobě obsahovat citlivé informace jako přihlašovací jméno a heslo k databázi.
Proto je možné řetězec nechat v případě potřeby zašifrovat.

- Stisknout klávesu **Win + R** ->  zadat **cmd** a spustit tak příkazový řádek. 
- V příkazovém řádku přejít do adresáře s instalačními soubory DC3.
- Zadat příkaz **DC.DC3.exe dc3 encryptSettings > encrypt.txt**
- Příkaz vygeneruje soubor **enctypt.txt** se zašifrovanými připojovacími řetězci.
- Ze souboru je možné vykopírovat část **EnctyptedData:** a vložit ji do souboru **appsettings.json** k odpovídajícím připojením.

.. image:: /img/Encrypt.PNG

.. image:: /img/EncryptedSettings.PNG

Příprava databáze
------------------------------------

Pokud se DC3 instaluje do nové prázdné databáze, je třeba, aby v ní byl nejprve spuštěn inicializační script obsahující 3 tabulky pro verzování DB. Před vlastní instalací spusťte tedy script
**DatabaseVersion.sql**, který je součástí instalačních balíčků. Pro MS SQL a Oracle existují samostatné verze tohoto scriptu.

V případě potřeby je také někdy nutné ještě spustit script pro nagrantování práv. Script **GrantsDC3.sql** je rovněž součástí instalačního balíčku.


Konfigurace SMTP
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

DC3 umožňuje zasílat mailové notifikace a jiné zprávy. Pro využití této funkce je nutné nastavit mailový SMTP server a ověření k němu.

- Otevřít soubor **appsettings.json**
- Upravit sekci **MailSettings**

.. image:: /img/smtp.PNG


Konfigurace IIS
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

DC3 je webová aplikace a potřebuje pro svůj běh webový server. Následující text popisuje nastavení při použití webového serveru IIS.

- Spustit **IIS Management Console**
- Založit nový aplikační pool **Application pools** -> **Add Application Pool** s názvem *DC3Pool*
- Přepnout verzi .NET na **No Managed Code**

.. image:: /img/Pool.PNG

- Přejít do **Advanced Settings** a nastavit volbu **Load User Profile** na **True**

.. image:: /img/PoolAdvance.PNG

- Založit novou webovou aplikaci a nasměrovat ji na adresář s instalačními soubory DC3.

.. image:: /img/AddApp.PNG

- Vybrat správný pool

.. image:: /img/AddApp2.PNG

- V nastavení aplikace přejít do části **Authentication** a nastavit požadované typy autentikace. Pokud je plánováno ověřovat se do aplikace přes SSO, pak je nutné nastavit Windows autentikaci na **Enabled**

.. image:: /img/Authentication.PNG

Instalace certifikátů
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Pro správný běh windows služby a importních modulů DC3 je nutné nainstalovat dva certifikáty.

- **DC3 CA** - kořenový certifikát CA
- **DC3Worker** - osobní certifikát pod kterým běži systémová identita DC3Worker.

Automatická instalace (doporučeno)
------------------------------------

- V instalačních souborech DC3 vyhledat složku **Certificate**.
- Složky obsahuje oba požadované certifikáty a také soubor **InstallCert.cmd**
- V příkazové řádce spustit soubor **InstallCert.cmd**
- Import certifikátů by měl proběhnout automaticky. Pokud se z nějakého důvodu nezdaří a importní soubor vypíše chybu, pak je nutné import provést manuálně.

Manuální instalace
---------------------

- Stisknout klávesu **Win + R** ->  zadat **mmc** a spustit tak konzoli.

- Do konzole přidat přes **Menu** -> **Add/Remove Snap-in...** položku **Certificates** pro tento počítač

.. image:: /img/Cert1.PNG

- Zobrazí se seznam instalovaných certifikátů v tomto počítači

- V levé části rozkliknout větev **Trusted Root Certification Authorities** -> **Pravé tlačítko** -> **All Tasks** -> **Import..**

- Naimportovat certifikát **DC3CACert.cer**

.. image:: /img/Cert2.PNG

- V levé části rozkliknout větev **Personal** -> **Pravé tlačítko** -> **All Tasks** -> **Import..**

- Naimportovat certifikát **DC3WorkerCert.pfx**. Heslo k certifikátu je uloženo v souboru **InstallCert.cmd**

.. image:: /img/Cert3.PNG

Vygenerování databáze
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. warning:: Následující uvedený postup způsobí kompletní přemazání databáze a její přípravu do výchozího stavu. Aplikujte tento postup pouze v případě že provádíte novou instalaci nebo jste si jisti, že chcete provést kompletní reset databáze.

.. note:: Předpokladem pro následující postup je existující databáze na db serveru, ke které se lze připojit s údaji uvedenými v :ref:`pripojeni_label`. 

- Stisknout klávesu **Win + R** ->  zadat **cmd** a spustit tak příkazový řádek.

- V příkazovém řádku přejít do adresáře s instalačními soubory DC3.

- Zadat příkaz **DC.DC3.Tools.exe updateDatabase InitDatabase,Dochazka,DCTest,Reindex** nebo **DC.DC3.exe updateDatabase InitDatabase,Dochazka,DCTest,Reindex** (část příkazu za InitDatabase se může lišit v závislosti na vybraném zákazníkovi).

- Příkaz spustí vygenerování prázdné databáze (příkaz InitDatabase) a náseldné plnění daty specifickými pro zákazníka (DCTest). Nakonec je spuštěn reindex databáze.

- Po doběhnutí je DB připravena ke spuštění DC3.

Konfigurace PublicUrl
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Do konfiguračního souboru **appsettings.json** je třeba dále doplnit URL, pod kterou se bude na aplikaci přistupovat.

- Otevřít soubor **appsettings.json**
- Do sekce **AppSettings** -> **PublicUrl** doplnit URL, pod kterou se bude na aplikaci přistupovat (např. https://muj-server.cz/DC3). 

.. image:: /img/PublicUrl.PNG

Konfigurace služby DC.DC3.Service
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Součástí aplikace je služba výše nainstalovaná DC3 Worker host service. Pro tuto službu je nutné zadat do konfigurace URL cestu, kde je hostována IIS webová aplikace DC3.

- Otevřít soubor **appsettings.json**
- Do sekce **ServiceSettings** -> **ApplicationBaseUrl** doplnit URL, pod kterou je nakonfigurována DC3 na IIS (typicky http://localhost/DC3)

.. image:: /img/ServiceUrl.PNG

Spuštění aplikace a služby
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Pokud byl správně dodržen výše uvedený postup instalace, měla by být aplikace DC3 připravena ke spuštění.

- V IIS konzoli spustit pool **DC3Pool**
- V seznamu Windows služeb spustit **DC3 Worker Host Service**
- Spustit webový prohlížeč a přejít na adresu **http://localhost/DC3** \*

\* Adresa uvedená při konfiguraci IIS.

.. image:: /img/DC3.PNG
