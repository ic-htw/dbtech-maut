# Datenbanktechnologien - Maut

Dieses Repository beinhaltet den kompletten Satz an Übungsaufgaben für das Modul "Datenbanktechnologien" an der HTW
Berlin. Die Aufgaben basieren auf dem Anwendungsfall einer LKW-Maut.

Importieren sie das Repository in ihren eigenen Github-Account und setzen sie die Sichtbarkeit auf "private", damit
andere Gruppen nicht ihre Lösungen einsehen können. Klonen sie dann ihr Repository (nicht meines) und bearbeiten sie es
mit der IDE ihrer Wahl (VS Code, IntelliJ, Eclipse)

- Anleitung Import
  [(link)](https://ic-htw.github.io/home/lv/dbtech/p/github-import.html)
- Anleitung VS Code
  [(link)](https://ic-htw.github.io/home/lv/dbtech/p/ide-vscode.html)
- Anleitung IntelliJ
  [(link)](https://ic-htw.github.io/home/lv/dbtech/p/ide-intellij.html)
- Anleitung Eclipse
  [(link)](https://ic-htw.github.io/home/lv/dbtech/p/ide-eclipse.html)

## Datenmodell

Zur Bearbeitung der Aufgaben müssen sie das Datenmodell in ihrer eigenen Datenbank anlegen.

- Beschreibung des Datenmodells
  [(pdf)](https://github.com/ic-htw/dbtech-maut/blob/main/doc/maut-beschreibung.pdf)
- SQL-Code zur Erzeugung der Tabellen und zum Einfügen der Daten
  [(link)](https://github.com/ic-htw/dbtech-maut/tree/main/db/maut)

## Logging

Die Verwendung der Logging-Einstellungen wird in der Übung erläutert.

```
-Dorg.slf4j.simpleLogger.showThreadName="false"
-Dorg.slf4j.simpleLogger.logFile="System.out"
-Dorg.slf4j.simpleLogger.log.org.dbunit="error"
-Dorg.slf4j.simpleLogger.log.de.htwberlin="info"
```

Protokollstufen

- error
- warn
- info
- debug

## Übungen

Es gibt eine Übung zur Wiederholung von SQL, zwei Übungen zur Implementierung von datenbank-orientierten Diensten in
Java und eine Übung zur Implementierung eines datenbank-orientierten Dienstes in PL/SQL.

Für die Dienste werden Test bereitgestellt, mit denen sie die Funktionsfähigkeit ihrer Lösungen überprüfen können.
Bedenken Sie aber, dass eine erfolgreiche Ausführung aller Tests nicht automatisch die Korrektheit Ihrer Lösung
sicherstellt. Tests können immer nur die Anwesenheit von Fehlern zeigen, nicht aber deren Abwesenheit. Das liegt daran,
dass Tests im Allgemeinen nicht vollständig alle Fehlersituationen abdecken. In der Bewertung Ihrer Lösung ist daher der
erfolgreiche Durchlauf aller Tests eine notwendige Bedingung zum Erreichen der vollen Punktzahl. Es kann aber trotzdem
Punktabzug geben, falls Ihre Lösung Fehler enthält, die durch die Tests nicht aufgedeckt werden.

### Bonuspukte

Im Laufe des Semesters kann jede Gruppe einmalig 2 Bonuspunkte durch die Präsentation von Lösungsbestandteilen der Übung
2 oder 3 erhalten. Es gelten folgende Bedingungen

- Die Punkte können als Ausgleich für Punktabzüge bei den abgegeben Lösungen dienen
- Sie können trotzdem nur insgesamt 40 Punkte in den Übungen erreichen
- Sollte die Summe regulärer Punkte plus Bonuspunkte 40 übersteigen wird auf 40 gekappt

### Übung 1

Diese Übung dient zur Wiederholung von SQL und zur Einarbeitung in die Struktur der Daten.
Die Bearbeitung dieser Übung ist freiwillig, eine Abgabe der Lösungen ist nicht erforderlich.

- SQL-Aufgaben
  [(link)](https://github.com/ic-htw/dbtech-maut/blob/main/db/aufgaben/ue01/maut-sql.pdf)

### Übung 2 (10 Punkte)

Diese Übung dient zur Implementierung einfacher Dienste in Java. Dabei geht es im Wesentlichen um die Verwaltung der
Daten.

```java
public interface IMautVerwaltung {
  String getStatusForOnBoardUnit(long fzg_id);
  int getUsernumber(int maut_id);
  void registerVehicle(long fz_id, int sskl_id, int nutzer_id, String kennzeichen, String fin, int achsen, int gewicht, String zulassungsland);
  void updateStatusForOnBoardUnit(long fzg_id, String status);
  void deleteVehicle(long fz_id);
  List<Mautabschnitt> getTrackInformations(String abschnittstyp);
  void setConnection(Connection connection);}
```

Details zu den Diensten finden sie hier
[(link)](https://github.com/ic-htw/dbtech-maut/blob/main/javasrc/de/htwberlin/dbtech/aufgaben/ue02/IMautVerwaltung.java)

Der Implementierung soll in der Klasse ```MautVerwaltungImpl``` erfolgen

### Übung 3 (20 Punkte)

Diese Übung dient zur Implementierung eines komplexen Dienstes in Java. Dabei geht es um die Mautabrechnung.

```java
void berechneMaut(int mautAbschnitt, int achszahl, String kennzeichen) throws UnkownVehicleException, InvalidVehicleDataException, AlreadyCruisedException;
```

Details zu dem Dienst finden sie hier
[(link)](https://github.com/ic-htw/dbtech-maut/blob/main/javasrc/de/htwberlin/dbtech/aufgaben/ue03/IMautService.java)

Hinweise zur Implementierung der Abrechnungslogik finden sie hier:
- Ablaufdiagramm [(png)](https://github.com/ic-htw/dbtech-maut/blob/main/doc/maut-service-algo.png)
- Pseudocode [(txt)](https://github.com/ic-htw/dbtech-maut/blob/main/doc/maut-service-algo.txt)

Dieser Dienst soll in zwei Versionen implementiert werden:

1. Implementierung wie bei Übung 2 in einer Klasse ```MautServiceImpl``` 
2. Implementierung auf Grundlage eines Architekturmusters (Table-Data-Gateway, Row-Data-Gateway oder Data-Mapper) in
   einer Klasse ```MautServiceImplDao``` und zusätzlichen Interfaces und Klassen zur Implementierung der DAOs

### Übung 4 (10 Punkte)

Diese Übung dient zur Implementierung eines komplexen Dienstes innerhalb des Datenbanksystems mit PL/SQL. Dabei soll der
Dienst aus Übung 3 in diesem neuen Umfeld reimplementiert werden.

```sql
create or replace package maut_service as
  unkown_vehicle exception;
  pragma exception_init(unkown_vehicle, -20001);
  invalid_vehicle_data exception;
  pragma exception_init(invalid_vehicle_data, -20002);
  already_cruised exception;
  pragma exception_init(already_cruised, -20003);

  procedure berechnemaut(
    p_mautabschnitt mautabschnitt.abschnitts_id%type,
    p_achszahl fahrzeug.achsen%type,
    p_kennzeichen fahrzeug.kennzeichen%type);
end maut_service;
```

PL/SQL-Code zu dieser Übung finden sie hier
[(link)](https://github.com/ic-htw/dbtech-maut/tree/main/db/aufgaben/ue04)