.. DC3 documentation master file, created by
   sphinx-quickstart on Mon Aug  8 17:09:32 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Instalace DC3 na produkční prostředí
=====================================

Následující text popisuje, jakým způsobem provést prvotní čistou instalaci DC3 do produkčního prostředí včetně vygenerování prázné databáze. 
V případě, že potřebujete provést aktualizaci aplikace nebo obnovení ze zálohy, pokračujte na kapitolu :doc:`Aktualizace a obnovení DC3 </aktualizace-a-obnoveni>` 

.. note:: Pokud si nejste jisti svým postupem nebo v případě složitějších instalací, kontaktujte vždy konzultanta společnosti DATACENTRUM systems & consulting a.s. 

.. contents:: V této části:
  :local:
  :depth: 1
  

Instalační soubory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Přehlad zdrojových kódů aplikace zajišťují automatizované buildy v prostředí Microsoft Team Foundation Server, jejichž výsledkem je zkompilovaný adresář **Main**,
obsahující instalační soubory.

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
Službu je nutné nainstalovat do systému pomocí .NET Utility **InstallUtil.exe**.

- Stisknout klávesu **Win + R** ->  zadat **cmd** a spustit tak příkazový řádek.

.. image:: /img/RunCmd.PNG

- V příkazovém řádku přejít do adresáře **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319**

.. image:: /img/Cmd.PNG

- Pomocí **InstalUtil.exe** nainstalovat službu **DC.DC3.Service.exe** umístěnou v instalačním adresáři DC3.

.. image:: /img/InstsallUtil.PNG

- Služba byla úspěšně naistalována a objeví se v seznamu Windows služeb.

.. image:: /img/Services.PNG

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

- Otevřít soubor **DC.DC3.Service.exe.config** a nastavit v něm stejnou URL pod kterou poběží webová aplikace (typicky http://localhost/DC3).

.. image:: /img/ServiceSettings.PNG

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

- Zadat příkaz **DC.DC3.exe dc3 updateDatabase InitDatabase,CNB,Reindex** (část příkazu za InitDatabase se může lišit v závislosti na vybraném zákazníkovi).

- Příkaz spustí vygenerování prázdné databáze (příkaz InitDatabase) a náseldné plnění daty specifickými pro zákazníka (CNB). Nakonec je spuštěn reindex databáze.

- Po doběhnutí je DB připravena ke spuštění DC3.

Spuštění služeb
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Pokud byl správně dodržen výše uvedený postup instalace, měla by být aplikace DC3 připravena ke spuštění.

- V IIS konzoli spustit pool **DC3Pool**
- V seznamu Windows služeb spustit **DC3 Worker Host Service**
- Spustit webový prohlížeč a přejít na adresu **http://localhost/DC3** \*

\* Adresa uvedená při konfiguraci IIS.

.. image:: /img/DC3.PNG
