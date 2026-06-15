# Petropia – Das Code-Archiv unseres Minecraft-Netzwerks

## Über das Projekt

Petropia war ein komplett selbst entwickeltes Minecraft-Netzwerk, an dem wir über zwei Jahre lang als Team gearbeitet haben. Unser Ziel war es, weg von Standard-Plugins zu gehen und stattdessen eine eigene, performante und flexible Server-Struktur aufzubauen. 

Auch wenn das Netzwerk am Ende nicht Full-Time online gegangen ist, steckt in dieser GitHub-Organisation jede Menge Arbeit, Herzblut und vor allem cooler Code. Wir haben uns dazu entschieden, das gesamte Projekt als **Open-Source-Archiv** zur Verfügung zu stellen. Wenn du selbst Plugins entwickelst oder dich für die Logik hinter größeren Netzwerken interessierst, kannst du dich hier gerne bedienen und inspirieren lassen!

## Das technische Fundament (Setup)

Damit alles flüssig läuft und die Server nicht unter der Last laggen, haben wir uns für folgendes Setup entschieden:

* **Hardware:** Ubuntu-Server (64GB RAM, 12-Kern CPU)
* **Server-Software:** Paper 1.20.1 kombiniert mit dem Velocity-Proxy, um die Spieler flexibel auf die Unterserver zu verteilen.
* **Netzwerk-Management:** CloudNet v4. Die CloudNet-API und ihre MessageChannels waren das Messagesystem unseres Netzwerks, über das unsere Plugins miteinander kommuniziert haben.
* **Datenbank:** MongoDB, um Spielerdaten, Inventare und Welten performant und asynchron zu sichern.

---

## Unsere wichtigsten Eigenentwicklungen

### 1. TurtleServer (Der Kern)
Der `TurtleServer` ist unsere Kern-API. Statt das jedes Plugin alles selber umsetzt, läuft hier alles zusammen, was global wichtig ist:
* **Daten & Profile:** Speicherung von Statistiken, der Namenshistorie, Skins und dem Freundesystem direkt in der MongoDB.
* **Asynchrones Welten-System:** Um RAM zu sparen, lädt und speichert der Server Welten (z.B. vom Bau-Server) dynamisch im Hintergrund aus der Datenbank, ohne dass das Spiel ins Stocken gerät.
* **Komfort-Features:** Einheitliche Chatformatierung, Tablisten, Prefixes und eine globale Abfrage von Chat-Eingaben für interaktive Menüs.

### 2. Multi-Arena-System & Serverübergreifende Joins
Standardmäßig braucht bei Minecraft oft jede Runde einen eigenen Server, was extrem viel Leistung braucht mit riesigen Overhead. Wir haben ein System gebaut, bei dem mehrere Arenen gleichzeitig auf einem einzigen Server laufen können. Die Logik dahinter:
* **Der Ablauf:** Erstellt ein Server eine neue Arena, lädt er die passende Welt über den `TurtleServer` und gibt die Arena-ID via CloudNet an alle Lobbies weiter.
* **Das Matchmaking:** Will ein Spieler in eine Runde, fragt die Lobby beim Minigame-Server an. Dieser reserviert den Platz, gibt den Platz frei und der Spieler wird über den Proxy direkt in die richtige Arena teleportiert. 
* Eine neue Lobby fragt beim Starten einfach einmal das Netzwerk ab und weiß sofort über alle laufenden Runden Bescheid.

---

## Die Spielmodi

### Bingo (Fertiggestellt)
Klassisches Bingo, aber komplett automatisiert in einer zufälligen Vanilla-Welt. Spielbar alleine (8x1) oder als Team mit PvP (4x2).
* **Der Code:** Ein eigener Algorithmus generiert die Bingokarten. Jedes Item im Spiel hat eine Schwierigkeitsstufe. Der Algorithmus sorgt dafür, dass leichte Items (wie Erde oder Holz) häufiger auf der Karte landen als schwere (wie ein Totem).

### ChickenLeague (Fertiggestellt)
Unsere Minecraft-Version von *Rocket League*. Spieler versuchen, mit verschiedenen Schlägern und Spezial-Items ein Huhn ins gegnerische Tor zu befördern (spielbar als 1vs1, 3vs3 oder 5vs5).
* **Der Code:** Hier mussten wir die Knockback-Mechaniken von Minecraft so anpassen, dass das Huhn flüssig als "Ball" reagiert, während im Hintergrund die Arena-Logik für Tore, Punkte und Resets sorgt.

### SpaceLife (Work in Progress)
Unser Konzept für einen modularen Survival- und Wirtschaftsmodus. Das Herzstück ist das Plugin `SpaceLifeCore`:
* **Wirtschaft & Grundstücke:** Spieler können am Spawn Shops mieten, Jobs ausführen (Blöcke abbauen, Mobs jagen) und eigene Bauwelten kaufen.
* **Der Code:** Die Grundstücke besitzen eigene Rechte-Einstellungen und können mit Freunden geteilt werden. Das Core-Plugin regelt das serverübergreifende Teleportieren, die Währung und die synchrone Speicherung der Spieler-Inventare in der MongoDB.

*Viel Spaß beim Durchschauen des Codes! Wenn du Fragen zu bestimmten Systemen hast, meld dich gerne. Jeglicher Code steht unter der MIT Lizenz frei zur Verfügung*
