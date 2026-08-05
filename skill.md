

Datenschutz

Abrechnung

Nutzung

Fähigkeiten

Claude Code

Cowork

Claude in Chrome
Desktop-App

Allgemein

Erweiterungen

Entwickler
Anpassen

Skills

Konnektoren

Plugins

Speicher
gtm-tracking
von Sie


Mehr anzeigenArbeitsregeln, Architektur-Standards und Fallstricke-Katalog für alle GTM-, Server-Side-Tracking- und Consent-Themen. Immer anwenden, wenn es um Google Tag Manager (webGTM oder sGTM), Stape, GA4, Consent Mode v2, Cookiebot, Meta CAPI, TikTok Events API, Google Ads oder Microsoft Ads Conversion Tracking, dataLayer-Pushes, Enhanced Conversions, Event-Deduplizierung oder Tracking-Debugging geht. Auch anwenden bei Formulierungen wie "warum feuert der Tag nicht", "Event kommt nicht an", "Container aufräumen", "Tracking-Setup prüfen", "Consent greift nicht", "Zahlen weichen ab" oder wenn ein GTM-Container-Export (JSON) zur Analyse übergeben wird. Input: Screenshots, Container-Exports, dataLayer-Dumps, URLs. Output: Diagnose, Konfigurationsanleitung, Code, oder Kundentext.



---
name: gtm-tracking
description: "Arbeitsregeln, Architektur-Standards und Fallstricke-Katalog für alle GTM-, Server-Side-Tracking- und Consent-Themen. Immer anwenden, wenn es um Google Tag Manager (webGTM oder sGTM), Stape, GA4, Consent Mode v2, Cookiebot, Meta CAPI, TikTok Events API, Google Ads oder Microsoft Ads Conversion Tracking, dataLayer-Pushes, Enhanced Conversions, Event-Deduplizierung oder Tracking-Debugging geht. Auch anwenden bei Formulierungen wie \"warum feuert der Tag nicht\", \"Event kommt nicht an\", \"Container aufräumen\", \"Tracking-Setup prüfen\", \"Consent greift nicht\", \"Zahlen weichen ab\" oder wenn ein GTM-Container-Export (JSON) zur Analyse übergeben wird. Input: Screenshots, Container-Exports, dataLayer-Dumps, URLs. Output: Diagnose, Konfigurationsanleitung, Code, oder Kundentext."
---
 
# GTM und Server-Side Tracking: Arbeitsregeln
 
Regeln sind gekennzeichnet:
**[V] verbindlich** = wird nicht ohne ausdrückliche Ansage abgewichen.
**[B] bewährt** = Muster aus abgeschlossenen Projekten, im Einzelfall begründet abweichbar.
 
---
 
## 0. Leitprinzipien
 
Bei widersprüchlichen Signalen gilt diese Reihenfolge. Höhere Stufe schlägt niedrigere. **[V]**
 
1. **Messung vor Annahme.** Was tatsächlich beobachtet wurde, schlägt jede Erwartung, auch die eigene.
2. **Container-Export vor Template-Wissen.** Was im Export steht, schlägt die Doku des Tag-Templates.
3. **Network-Request vor UI-Anzeige.** Der echte Request-Body schlägt den GTM-Preview-Properties-Panel und jedes Dashboard.
4. **Consent-Status vor Tag-Logik.** Ein Tag, der ohne Consent feuert, ist nie durch bessere Datenqualität zu rechtfertigen.
5. **Kundenentscheidung vor Optimierung.** Erst argumentieren, dann umsetzen. Nie an einer bewussten Kundenentscheidung vorbei bauen.
Ergänzend die Grundhaltung: erst diagnostizieren, dann bauen. Die Prämisse einer Anfrage kann falsch sein.
Wenn "bau mir X" das Problem nicht löst oder ein neues erzeugt, wird das vor der Umsetzung angesprochen.
Anbieteraussagen (Stape, Meta, Plugin-Hersteller, CMP-Doku) sind Quellen, keine Autoritäten, und werden verifiziert.
 
---
 
## 1. Triage: wie umfangreich antworten
 
Nicht jede Anfrage braucht den vollen Audit-Workflow. Zuerst einordnen. **[V]**
 
| Fall | Vorgehen |
|---|---|
| Punktfrage ("wo gehört event_id hin", "welcher Regex fängt die Stape-Events") | Direkt beantworten. Kein Bestandsaufnahme-Block, keine Checkliste. |
| Einzelnes Symptom ("Tag feuert nicht", "Event kommt nicht an") | Diagnose-Block gezielt, nur die relevanten Prüfschritte. Maximal zwei Rückfragen, bevor eine erste Hypothese steht. |
| Setup, Umbau, Audit, Container-Export | Voller Ablauf aus Block 2 bis 5, Ausgabe im Audit-Format. |
 
Im Zweifel die kleinere Stufe wählen und anbieten hochzuskalieren, nicht umgekehrt.
 
---
 
## 2. Diagnose
 
### 2.1 Bestandsaufnahme vor jedem Umbau **[V]**
 
- Welche Container laufen tatsächlich auf der Seite (Tag Assistant), nicht welche laufen sollten.
- Welche CMP, in welchem Blockingmode, mit welchen Consent-Defaults, und wie viele Quellen setzen Defaults.
- Ist der sGTM live, mit welchen Tags, und läuft schon echter Traffic.
- Gibt es Tracking außerhalb des GTM (hardcoded gtag.js, Google-Tag-ID im Theme, Plugin-eigene Pixel).
- Bei Container-Export: Tags, Trigger, Variablen zählen, unreferenzierte Elemente identifizieren,
  Consent-Settings pro Tag dumpen. Referenzzählung über Vorkommen von `{{Name}}` in Tags, Triggern und Variablen.
### 2.2 Verifikationsschritte statt Vermutungen **[V]**
 
- Feuert ein Tag nicht: zuerst Consent-Status, dann Trigger-Bedingung, dann Variablenwert, dann Tag-Konfiguration.
- Blockt ein Consent-Check wirklich: durch explizites Denial testen, nicht aus der Feldbezeichnung schließen.
- Geht ein Parameter tatsächlich raus: Request-Body inspizieren.
- Ist ein Timing-Wert (z.B. `wait_for_update`) zu niedrig: tatsächliche CMP-Ladezeit im Network-Tab messen.
  Wenn ein höherer Wert das Problem "löst", ist meist der Consent-Update-Mechanismus defekt, nicht das Timing.
- Weichen Zahlen ab: zuerst Modellierung prüfen (Google Ads modelliert unabhängig von GA4), dann Deduplizierung
  (`event_id` identisch auf beiden Pfaden), dann Attributionsfenster, dann doppelte Streams, erst danach Implementierung.
---
 
## 3. Architektur-Standards
 
### 3.1 Zielarchitektur **[B]**
 
| Ebene | Standard |
|---|---|
| CMP | Cookiebot, Consent Mode v2, explizites Opt-in |
| webGTM | ein Container pro Kunde, auch bei mehreren Domains |
| sGTM | Stape, Custom Loader auf eigener Subdomain, obfuskierter Collect-Pfad |
| GA4 | eine Property, ein Datastream, auch bei Website plus Shop |
| Transport | Stape Data Tag vom webGTM in den sGTM, dort plattformspezifische CAPI-Tags |
| Deduplizierung | Stape `Unique Event ID` als `event_id`, geteilt zwischen Browser- und Server-Pfad |
| User-ID | `stape_uid`-Cookie via CookieMonster-Tag im sGTM, ausgelesen als `{{1PC - stape_uid}}` |
 
### 3.2 Platzierungsregeln **[V]**
 
- `user_id` in die **Shared Event Settings** des GA4 Config Tags. Nicht in die Configuration Parameters,
  nicht in die `config_settings`-Variable.
- `event_id` als **Event-Parameter in die GA4 Event Tags**, die auf demselben dataLayer-Event feuern wie der
  Meta-Pixel-Tag. Nicht in die Shared Event Settings.
- Race Condition beim ersten Pageview (Cookie noch nicht gesetzt, `user_id` leer) ist eine akzeptierte
  Einschränkung, kein Bug. Wird kommuniziert, nicht repariert.
- `first_party_collection: true` sowie `x-fb-ck-fbp` und `x-fb-ck-fbc` nur setzen, wenn der sGTM sie
  tatsächlich verarbeitet. Sonst dekorative Konfiguration.
### 3.3 Consent-Architektur **[V]**
 
1. Ein Consent-Mechanismus, nicht zwei. Hybride aus Consent Mode v2 plus manuellem Gating über
   `cookie_consent_update`-Compound-Trigger sind ein Warnsignal. Entweder Consent Checks auf Tag-Ebene oder
   durchgängiges Trigger-Gating, aber nicht pro Tag unterschiedlich.
2. Inkonsistente Trigger über vergleichbare Tags hinweg sind immer ein Befund, kein Stilproblem.
3. `cookie_consent_update` als Firing-Trigger ist legitim, weil Cookiebot das Event bei jedem Pageload mit dem
   gespeicherten Zustand feuert und zusätzlich bei jeder Änderung.
4. Doppelte Consent-Defaults erkennen: Shop-Plugins pushen `consent default` teils hardcoded ins Template,
   Cookiebot mit `data-blockingmode="auto"` setzt zusätzlich einen eigenen. Bewusst auf eine Quelle reduzieren.
5. Bei Stape-Templates blockt in der Praxis nur **"Additional Consent Checks"** zuverlässig, nicht
   "Built-In Consent Checks". Bei Marketing-Tags dort `ad_storage` setzen und per Denial-Test verifizieren.
6. Default Consent Mode in Drittanbieter-Templates prüfen. Opt-out als Template-Default ist für DACH nicht
   haltbar und wird auf Opt-in gestellt.
7. Tracking außerhalb des GTM ist ein Consent-Befund, weil es ohne Consent-Prüfung feuert. Als Befund melden,
   Nachweis über Tag Assistant plus Network-Tab führen, Bewertung dem Kunden und dessen Rechtsberatung überlassen.
### 3.4 Namenskonventionen **[B]**
 
Bestehende Kunden-Container behalten ihre Konvention. Nicht mitten im Container umstellen.
 
- Tags: `[Plattform] Event`, z.B. `[GA4] EEC - purchase`, `[Meta] Event - AddToCart`, `[MS Ads] Consent Mode Default`, `[cHTML] Fraud Blocker`
- Trigger: `ce - ` Custom Event, `pv - ` Pageview, `tg - ` Trigger Group, `dc - ` Data Client (sGTM)
- Variablen: `dlv - `, `1pc - `, `jsv - `, `LT - ` Lookup Table, `UPD - ` User Provided Data
Ordner werden genutzt, neue Elemente kommen nicht in die Wurzel.
 
### 3.5 Konsolidierung **[B]**
 
Wenn mehrere Tags dieselbe Struktur haben und sich nur im Eventnamen unterscheiden: Lookup Table plus ein Tag,
nicht Tag Nummer n+1. Ein Regex-Trigger statt vieler Einzeltrigger. Nach dem Umbau unreferenzierte Trigger und
Variablen entfernen, aber erst nach Referenzprüfung.
 
---
 
## 4. Fallstricke und Verifikation
 
Real aufgetretene Fehler. Bei Setup oder Audit gezielt danach suchen. Der Katalog wird nach jedem größeren
Projekt ergänzt, er ist bewusst nicht abgeschlossen.
 
| Fallstrick | Prüfung |
|---|---|
| `_fbp` und `_fbc` in den gtag-Parametern vertauscht | `x-fb-ck-fbp` muss `{{1pc - _fbp}}` sein, `x-fb-ck-fbc` muss `{{1pc - _fbc}}`. `_fbp` ist Browser-ID, `_fbc` Click-ID. Vertauscht bekommt Meta CAPI meist einen leeren Wert. |
| Zwei GA4-Streams für dieselbe Property aktiv | Entsteht, wenn nacheinander zwei Agenturen je einen Stream ergänzt haben. Alle Daten doppelt. |
| gtag.js parallel zum GTM eingebunden | Tag Assistant zeigt zwei Implementierungen. Doppelte Pageviews plus Consent-Lücke. |
| Stape-Eventnamen weichen vom GA4-Standard ab | `add_payment_info` heißt `payment_info_stape` (ohne `add_`), `view_item_list` heißt `view_collection_stape`. Regex-Trigger je Event mit `^` und `$`, kein Sammelmuster. |
| Shop-Plugin feuert Events doppelt | z.B. FunnelKit pusht `begin_checkout` als Custom-Push und als GA4-eventModel mit `send_to`. Im Plugin deaktivieren, nicht im GTM wegfiltern. |
| Service-Worker-Iframes verschmutzen GA4-Pfade | Zentraler Ausnahme-Trigger statt Inline-Filter in jedem Trigger. |
| `gtm.formInteract` ohne erkennbare Ursache | Kommt von einem Trigger vom Typ Formularübermittlung, unabhängig von Built-in-Formularvariablen. Auch ein zweiter Container teilt sich denselben dataLayer. |
| Confirmation-Page direkt aufrufbar | Pageview-Trigger auf die Danke-URL reicht nicht. Zweite Bedingung ergänzen, z.B. Referrer oder sessionStorage-Wert aus dem Flow. |
| PII roh im dataLayer | z.B. Frontend-Listener von CRM-Tools. Wird eskaliert, nicht umgangen. |
| Dynamisch benannte Cookies fluten das CMP | z.B. `dnd_cf7_token_{UUID}` von Contact Form 7. Cookiebot unterstützt keine Wildcards bei selbstdeklarierten Cookies. |
| Telefonnummern im falschen Format für Enhanced Conversions | Normalisierung auf E.164 über Custom-JavaScript-Variable vor der User-Provided-Data-Übergabe. |
| Preview funktioniert nicht im Checkout | Typisch in Shopify-Sandbox-Kontexten. Ersatzweise `dataLayer` in der Konsole und Collect-Endpunkt im Network-Tab. |
 
### Plattform-Spezifika
 
**Shopify:** Checkout läuft über Custom Web Pixel in isolierter Sandbox, Consent-Signale aus der Sandbox sind das
Kernproblem. Consent-Update über GTM-Custom-HTML erzeugt beim ersten Pageview von Returning Usern ein
Timing-Fenster, das aktiv kommuniziert wird. Stapes Empfehlung, Pixel-Permissions auf "Not required" zu setzen,
verschiebt die Verantwortung auf die serverseitige Consent-Prüfung und überträgt die IP bereits vor Consent.
 
**Shopware 6:** Häufig mehrere Consent-Quellen (Plugin plus CMP). dataLayer-Struktur der Plugins prüfen, bevor
eigene DLVs gebaut werden.
 
**WooCommerce:** Nicht-Standard-Checkouts sind der Normalfall. Eigene `add_to_cart`-Pushes über Event-Delegation
auf `document`, nicht `querySelectorAll` auf `DOMContentLoaded`, sonst greifen dynamisch injizierte Elemente nicht.
DOM-Fallback für Name und Preis einplanen.
 
**Smartstore:** Kein brauchbarer nativer dataLayer, Datenübergabe zwischen Seiten über sessionStorage. Der Stape
"User Data Collector From Page" zieht Name, Mail und Telefon oft direkt aus der Seite.
 
**Odoo:** Für CRM-Offline-Conversions relevant. Voraussetzung: `fbp` und `fbc` werden bereits beim ersten
Formular-Submit in Odoo-Felder geschrieben.
 
---
 
## 5. Ausgabeformate
 
### 5.1 Audit- und Diagnosebefund **[V]**
 
Je Befund, in dieser Reihenfolge, ohne Fließtext-Ausschmückung:
 
- **Befund:** was konkret beobachtet wurde, mit Fundstelle (Tag-Name, Trigger-ID, Variablenname, URL)
- **Auswirkung:** was das für Daten, Kampagnen oder Compliance bedeutet
- **Ursache:** soweit belegt. Wenn Hypothese, als Hypothese kennzeichnen plus Verifikationsschritt
- **Empfehlung:** konkrete Handlung, keine Richtungsangabe
- **Aufwand:** grobe Größenordnung, nur wenn nach Aufwand gefragt ist oder der Befund Umsetzung nach sich zieht
Befunde nach Schweregrad sortiert, nicht nach Fundreihenfolge. Was blockierend ist, steht oben.
 
### 5.2 Konfigurationsanleitung **[V]**
 
Als Tabelle Feld/Wert, nicht als Prosa. Trigger, Tag und Variablen getrennt. Immer mit einem Prüfpunkt am Ende:
woran ist im Preview erkennbar, dass es funktioniert.
 
### 5.3 Code **[V]**
 
Vollständig und lauffähig, kein Fragment mit ausgelassenen Stellen. Dazu jeweils:
 
- welche Annahmen der Code über DOM, dataLayer oder Plugin-Verhalten trifft
- wo er einzusetzen ist (Custom HTML Tag, Theme, Plugin, Web Pixel)
- ein konkreter Prüfpunkt zur Verifikation
Wenn eine Annahme nicht belegt ist, wird sie benannt und nicht stillschweigend eingebaut.
 
### 5.4 Kundentexte
 
Gilt zusätzlich `voice-dna`. Tracking-spezifisch nur:
Technische Befunde in Auswirkung übersetzen, nicht in Fachbegriffen liefern. Bekannte Einschränkungen
(Timing-Fenster, Modellierung, Race Conditions) aktiv benennen, bevor der Kunde nachfragt, warum Zahlen
unvollständig aussehen.
 
### 5.5 Schreibweise in allen Ausgaben **[V]**
 
Deutsche Texte mit echten Umlauten (ä, ö, ü, ß), nie ae, oe, ue, ss. Keine Gedankenstriche (en-dash, em-dash),
stattdessen Bindestrich, Doppelpunkt oder neuer Satz.
 
---
 
## 6. Was nicht tun **[V]**
 
- Keine Consent-Workarounds. Ein Tag wird nicht zum Feuern gebracht, indem der Consent-Check gelockert wird.
- Keine stillen Fixes. Was geändert wurde, wird benannt, auch wenn es nebenbei mitrepariert wurde.
- Keine unverifizierten Annahmen als Fakt formulieren. Hypothese wird als Hypothese gekennzeichnet.
- Container nicht ohne Rückfrage veröffentlichen. Der Hinweis, dass Änderungen im Workspace liegen und erst nach
  Publish wirken, gehört in jede Umsetzungsantwort.
- Tags, Trigger oder Variablen nicht ohne vorherige Referenzprüfung löschen.
- Historische Daten nicht als mitrepariert darstellen. Änderungen wirken ab Publish, Altdaten bleiben verzerrt
  und sind separat über Explorationen mit Regex-Ausschluss oder BigQuery zu behandeln.
- Keine Rechtsberatung formulieren. Sachlage und Risiko darstellen, Entscheidung beim Kunden.
## 7. Bei Unklarheit
 
Reihenfolge, wenn Informationen fehlen oder sich widersprechen:
 
1. Lässt sich die Frage durch Prüfung im vorhandenen Material klären (Export, Screenshot, dataLayer-Dump)? Dann prüfen.
2. Lässt sie sich durch eine benennbare Messung klären? Dann die Messung konkret vorgeben statt zu raten.
3. Bleibt eine Entscheidung offen, die den Lösungsweg verzweigt? Dann gezielt eine Frage stellen und dabei bereits
   die erste Hypothese liefern, nicht nur fragen.
4. Ist die Unklarheit fachlich nicht auflösbar (Kundenpräferenz, Budget, Rechtsauslegung)? Optionen mit Konsequenzen
   darstellen und eine Empfehlung geben.
Nie mehr als zwei Rückfragen, bevor eine erste inhaltliche Einschätzung geliefert wird.
 
---
 
## 8. Werkzeuge
 
| Zweck | Tool |
|---|---|
| Container lesen, ändern, aufräumen | GTM MCP Server. Beim Update `fingerprint` mitgeben, sonst schlägt der Write fehl. Änderungen landen im Workspace. |
| Container-Export analysieren | JSON-Export plus Python, Referenzzählung über `{{Name}}`-Vorkommen |
| Live-Prüfung im Browser | GTM Preview, Tag Assistant, Network-Tab, `dataLayer` in der Konsole |
| Serverseite | Stape Dashboard, sGTM Preview |
| Plattform-Gegenprüfung | GA4 DebugView und Realtime, Meta Events Manager, TikTok Events Manager |
| Crawling und Seitenstruktur | Screaming Frog |
