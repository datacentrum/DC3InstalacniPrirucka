.. DC3 documentation master file, created by
   sphinx-quickstart on Mon Aug  8 17:09:32 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Aktualizace a obnovení DC3
=====================================

Následující text popisuje, jakým způsobem provést aktualizaci aplikace nebo její obnovení ze zálohy.
V případě, že potřebujete provést čistou instalaci aplikace nebo vygenerování prázdné databáze, pokračujte na kapitolu :doc:`Instalace DC3 na produkční prostředí </Instalace/postup_instalace>` 

.. note:: Pokud není uvedeno jinak, měl by pro aktualizaci DC3 postačovat následující postup. V některých případech však postup aktualizace může být odlišný a měl by 
          být vždy součástí dodávky aktualizačních souborů.

.. warning:: Pokud si nejste jisti svým postupem nebo v případě složitějších instalací, kontaktujte vždy konzultanta společnosti DATACENTRUM systems & consulting a.s. 

.. contents:: V této části:
  :local:
  :depth: 1

Postup aktualizace
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Pro standartní aktualizaci DC3 na novější verzi postupujte podle následujích kroků.
  
1. Stopnout službu **DC3 Worker Host Service**.
2. Stopnout aplikační pool na IIS **DC3Pool**.
3. Rozbalit někam na disk aktualizační balíček (obvykle Main.zip).
4. Přehrát všechny soubory z balíčku.
5. Spustit DC.DC3.exe a počkat až doběhne. V případě chyby dále nepokračovat a chybu nejprve vyřešit.
6. Spustit aplikační pool na IIS **DC3Pool** a službu **DC3 Worker host service** přesně v tomto pořadí.
7. Zkontrolovat aplikační **Event viewer** a nejnovější záznamy v tabulce TraceLog. Pokud vše proběhlo v pořádku, aplikace se spustí bez chyb.

.. warning:: V případě že by součástí akutalizačního balíčku byly soubory **appsettings.json** nebo **DC.DC3.Service.exe.config**, nesmí být tyto
             soubory přehrány, ale musí být přehrát pouze část jejich obsahu. O tom, co přehrát se poraďte s konzultantem spol. DATACENTRUM systems & consulting, a.s.



   



