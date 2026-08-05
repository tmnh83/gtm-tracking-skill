# GTM Tracking Skill

Arbeitsregeln, Architektur-Standards und ein Fallstricke-Katalog für alle Themen rund um Google Tag Manager (Web und Server-Side), Stape, GA4, Consent Mode v2, Meta CAPI, TikTok Events API sowie Google Ads und Microsoft Ads Conversion Tracking.

Der Kern ist kein allgemeines Tutorial, sondern ein Regelwerk aus der Praxis: Es legt fest, in welcher Reihenfolge widersprüchliche Signale zu bewerten sind (z. B. was zählt mehr, ein Container-Export oder die Doku eines Tag-Templates), wie vor jedem Umbau eine Bestandsaufnahme aussieht, welche Zielarchitektur für webGTM- und sGTM-Setups gilt, und welche real aufgetretenen Fehler bei Tracking-Implementierungen wiederkehren.

Die Datei ist bewusst so aufgebaut, dass sie sowohl für schnelle Einzelfragen ("wo gehört event_id hin") als auch für vollständige Audits und Container-Umbauten nutzbar ist, mit klar getrennten Ausgabeformaten je nach Anfrageart.

## Inhalt

- **Leitprinzipien:** Feste Rangfolge bei widersprüchlichen Signalen, von "Messung vor Annahme" bis "Kundenentscheidung vor Optimierung"
- **Triage:** Unterscheidung zwischen Punktfrage, Einzelsymptom und vollständigem Audit, um den Aufwand zur Anfrage passend zu skalieren
- **Diagnose:** Konkrete Bestandsaufnahme- und Verifikationsschritte vor jedem Umbau, inklusive Vorgehen bei nicht feuernden Tags, blockierendem Consent und abweichenden Zahlen zwischen Plattformen
- **Architektur-Standards:** Zielarchitektur für CMP, webGTM, sGTM (Stape), GA4, Event-Deduplizierung und User-ID-Handling, dazu klare Platzierungsregeln, Consent-Architektur und Namenskonventionen für Tags, Trigger und Variablen
- **Fallstricke-Katalog:** Tabelle mit real aufgetretenen Fehlern und dem jeweiligen Prüfschritt zur Verifikation, ergänzt um Plattform-Spezifika für Shopify, Shopware 6, WooCommerce, Smartstore und Odoo
- **Ausgabeformate:** Feste Struktur für Audit-Befunde (Befund, Auswirkung, Ursache, Empfehlung, Aufwand), Konfigurationsanleitungen als Tabelle, vollständigen und lauffähigen Code sowie kundenverständliche Texte
- **Was nicht zu tun ist:** Explizite Liste vermeidbarer Fehler, etwa Consent-Workarounds, stille Fixes oder unverifizierte Annahmen als Fakt
- **Werkzeuge:** Übersicht der eingesetzten Tools für Container-Analyse, Live-Prüfung im Browser und plattformseitige Gegenprüfung

## Verwendung

Als Referenz für Tracking-Setups, Audits und Debugging gedacht, nicht als Ersatz für Rechtsberatung oder projektspezifische Dokumentation. Der Fallstricke-Katalog ist bewusst nicht abgeschlossen und wird laufend um neue, real aufgetretene Fälle ergänzt.
