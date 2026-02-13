# 📄 Dokumentation: Logik-Implementierung "Aufenthaltsermittlung"

Dieses Modul dokumentiert die technische Umsetzung der Teilaktualisierung für den Workflow **Verfügungen und Reinschriften**. Es beschreibt die Skript-Logik zur automatisierten Steuerung von behördlichen Anfragen (Einwohnermeldeamt/Polizei) und die dynamische Generierung der zugehörigen Korrespondenz.

## 🎯 Zielsetzung des Updates

Das Update optimiert die Verarbeitungskette innerhalb der Dialog-Struktur `lbl_Aufenthaltsermittlung`. Ziel ist die fehlerfreie Zuweisung von Empfängern und die automatisierte Bereinigung von Auswahllisten vor der Dokumentenerstellung.

## 🛠️ Technische Kernfunktionen

### 1. Dynamisches Listen-Management (Data Cleaning)

Um Redundanzen oder fehlerhafte Datensätze zu vermeiden, wurde eine Bereinigungsroutine implementiert:

* 
**Iteration**: Eine `While`-Schleife iteriert durch die Liste `gsl_MerkAuswahl4610`, um die Anzahl der Einträge zu prüfen.


* **Conditional Removal**: Einträge werden basierend auf Index und Werten (`Sys::HasValue`) validiert. Ungültige oder zu löschende Empfänger werden mittels `Sys::ListRemove` und der Funktion `M_EMPF_Loeschen` dynamisch aus der Verarbeitungskette entfernt.


* 
**Zähler-Logik**: Ein interner Zähler (`n_Zahl`) überwacht den Status der Verarbeitung (`OnClose`-Event), um sicherzustellen, dass nur relevante Datensätze in die Endausgabe gelangen.



### 2. Entscheidungslogik für Behördenanfragen

Das System unterscheidet intelligent zwischen verschiedenen Empfängertypen durch Abfrage der Input-Masken (`M_INPUT_WertHolen`):

* 
**Einwohnermeldeamt (EMA)**: Prüfung des Parameters `chk_Einwohnermeldeamt_3`.


* 
**Polizei**: Prüfung des Parameters `chk_Polizei_4`.


* 
**Rücklaufsteuerung**: Automatische Setzung von Wiedervorlagen (`2gs_WV_keinDatum`) mit dem Vermerk "nach Rückkunft der Akte", falls eine Aufenthaltsermittlung eingeleitet wurde.



### 3. Reinschriften-Steuerung (Output Management)

Die Generierung der Reinschriften erfolgt kontextsensitiv und berücksichtigt länderspezifische Anforderungen:

* 
**Länderweiche**: Ein `Switch (Sys::Variant)`-Block ermöglicht unterschiedliche Verarbeitungswege für Bundesländer (z. B. Bayern vs. BW/RP/SH).


* 
**Makro-Aufrufe**: Nutzung spezialisierter Makros wie `M_EMPF_Reinschriften04` zur Erzeugung der finalen Dokumente basierend auf der bereinigten `MerkAuswahl`.



## ⚠️ Wichtiger Konfigurations-Hinweis

Für die korrekte Funktion der Iterationsschleifen ist eine spezifische Einstellung der Laufzeitumgebung zwingend erforderlich:

> 
> **Critical Fix**: In der Konfiguration der `While`-Schleife für die Listenverarbeitung (`n_i < Sys::ListLength`) muss die Option **"Gedächtnislos"** deaktiviert (weggeklickt) werden. Dies verhindert, dass der Schleifenzähler bei Re-Entry ungewollt zurückgesetzt wird und garantiert die Prozessstabilität.
> 
> 

## 📂 Zugehörige Formulare

Die Logik korrespondiert mit dem Formular-Template:

* 
**F_85_1*** 



---

*Dieses Dokument dient als technische Referenz für die Wartung der Skriptlogik und die Qualitätssicherung der automatisieren Verfügungserstellung.*
