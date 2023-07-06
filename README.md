# rnbo  

## Grundsätzliche Ressourcen:  
https://rnbo.cycling74.com/  
https://docs.cycling74.com/max8/vignettes/rnbo_resources (Synth Building Blocks und RNBO Pedals)
  
## Installation  
- RNBO und evtl. auch RNBO Guitar Pedals im Package Manager installieren. Das war's eigentlich.

## FFT in RNBO:
- https://rnbo.cycling74.com/learn/using-the-fft
- Das Windowing ist vereinfacht; es gibt preset Window-Funktionen, man kann aber auch die alten Max-Patches rüberziehen - aber der default ist, dass kein Windowing angestellt ist! Man muss also prinzipiell weniger das Windowing von Hand machen, muss sich aber dennoch überlegen, was man da gerade tut.

## Wann sollte man RNBO benutzen und wann nicht?
- Ja:
  - Sachen als VST oder auf den RasPi oder auf eine Webpage exportieren (kann nur RNBO)
  - Samplegenaues Arbeiten (kann allerdings auch Gen)
  - Gen auf anderen Geräten benutzen (*alle* Gen-Befehle klappen in RNBO!)
  - Unumständliche Polyphony (t.b.c.)
  - Granularsynthese soll aus irgendwelchen Gründen besser sein (t.b.c.)
- Nein:
  - Rapid Prototyping (denn die Kompiliererei braucht Zeit)
  - bpatcher gibt es nicht
  - Wenn ich Presets brauche; die gehen zwar, aber nur über Snapshots
  - während des Entwickelns eines großen Patches (Kompilierzeiten skalieren schlecht)
  - zum Ausprobieren von neuer, unbekannter Hardware (keine Debug-Ausgaben, d. h. schon die CC-Ausgabe eine MIDI-Controllers kann ich nicht sichtbar machen)


### Patch 0: "Fingerübung" Stereo Widener in MS  
- siehe Verzeichnis
  
### Patch 1: MHV zu Doppel MS  
  
Horizontal (wie MS):  
L = M + H, R = M − H   
Vertikal:  
B = M + V, T = M − V  
- Im Patch ist (zum Testen) auswählbar, ob man auf Kanal 1+2 das L/R- oder das B/T-Signal haben will. (Auf Kanal 3/4 ist immer das B/T-Signal.)
- Das VST nimmt dB als Werte entgegen, dazu den "enum"-Auswähler für LR/BT-Wahl.

 
  
### Common Mistakes/Stolpersteine/Dinge, die anders sind als erwartet
Achtung: Das hier ist bewusst eine lose Sammlung an Stolpersteinen ohne Ordnung.
- Ohne Internetzugang ist RNBO schwer bis gar nicht zu gebrauchen; bei langsamem Internetzugang hängt Max regelmäßig
- (DNS-Sperre aus Hotels?!)
- Beim VST bauen: Vor Recompile auf jeden Fall das VST in der DAW unloaden. Danach dran denken, dass man einen Neuscan des VST-Ordners machen muss. (Gibt es einen "einfacheren" VST-Host, damit man nicht immer eine "große" DAW manövrieren muss? Sequoia muss man z. B. manchmal komplett neu starten.)
- RNBO rechnet nur mit floats (intern)
- RNBO ist im Input blockbasiert. Die Befehle sind dennoch samplebasiert. Das hat Folgen: Ähnlich wie history~ in Gen ("gib mir das Sample n-1") kann man feedback~ in RNBO benutzen und kriegt ein um einen Block verschobenes Signal. Das muss man u. a. dann machen, wenn man eine Feedbackschleife bauen will. RNBO lässt das nicht zu; man muss den Output eben mit feedback~ um ein Sample verzögern.
- RNBO nicht Gen: Man kann Scope etc. benutzen, was in Gen nicht geht (weil Gen nur samplebasiert arbeitet)
- z.\*-Objekte heißen hier list.\*
- Als Alternative zu buffer gibt es data-Objekte (64-bit Buffer). Das zieht einiges nach sich:
  - "groove~ @buffername buff1 buff2 buff3 @loop 1 @end 1500"
  - "waveform~" kann "buffer" darstellen, aber keine "data". Es gibt also keine "waveform"-Preview bei "data"-Objekten
  - Scope von Objekten kann modifiziert werden: "buffer local:foo 2048"
  - Buffer können auch urls als Source kriegen! Das ist wunderschön, weil man so Internet-Ressourcen laden kann (z. B. auf dem Pi headless Audiodateien abspielen)
  - Auch noch eine Menge anderer Änderungen in dem BUffer-Objekt. Weiteres: https://rnbo.cycling74.com/learn/using-buffers
- Sample-Accurate Patching
  - RNBO benutzt nicht den normalen Scheduler von Max
  - Man sollte also niemals ein RNBO in einem größeren Max-Patcher verwenden und dann z. B. ein uzi darauf anwenden.
  - Wann RNBO eine Message (also nicht: ein Signal!) verarbeitet, ist ungewiss. Wenn man will, dass etwas pünktlich passiert, sollte man Signale draus machen:
  - sig~ wandelt Zahlen in Signale um, damit kann ich wieder sample-akkurate Verarbeitung auch von Zahlenwerten nutzen. Achtung, das frisst natürlich Ressourcen.
  - Weiteres: https://rnbo.cycling74.com/learn/intro-to-sample-accurate-patching
- Immer wieder: Man muss sich WIRKLICH daran gewöhnen, dass alles länger braucht.

### Messages and Ports
- https://rnbo.cycling74.com/learn/messages-and-ports
- Man muss sich erstmal dran gewöhnen, wie RNBO Nachrichten empfängt und sendet: Die in~ und inport~-Eingänge muss man sich genau angucken und schauen, welches Scope sie jeweils haben.
- Wichtiger Denkfehler hier: Für das Prototyping auf einem normalen Computer strickt man einen normalen Max-Patch mit ezdac~, ezadc~ usw. um ein RNBO-Objekt. All das fliegt aber raus, wenn man das RNBO-Objekt dann kompiliert und an die entsprechende Stelle (z. B. auf den RasPi) bringt. Man muss also immer genau überlegen, welche Infrastruktur in den RNBO-Patcher soll und welche Infrastruktur außenrum gebraucht wird.
- Die Nachrichten, die ich über die Ports kriege, kann ich bearbeiten:
  - @fromnormalized expression => linear scaling
  - Für das RasPi-Webinterface ist noch @displayorder interessant
  - @max, @min, @enum sind auch offensichtlich wichtige Parameter 


### MIDI und OSC
- midiin und ctlin sind die Blöcke, die wir brauchen.
  - ACHTUNG. Wenn man im Max-Patcher draußen herum kein midiin hat, funktioniert beim Prototyping das midiin im RNBO-Subpatcher auch nicht.
  - Dem midiin im Hauptpatcher gibt man von midiinfo (message: Controller) das richtige control device.
  - Magisch erscheint dann im rnbo~ objekt ein zweites inlet für MIDI. Sonst nicht!

- Um z. B. einen RasPi zu steueren, ohne das Web-Interface zu benutzen, ist die einfachste Lösung, dass man einen Max-Patch zur Kontrolle baut mit OSC-Buttons: 
  - Alle Parameter findet man über http://adresse_des_raspi:5678/rnbo/inst/0/ heraus
  - Dann baut man entsprechende Messages in Max
  - ...und sendet sie via "udpsend ipaddr 1234" an die OSC-Adresse. Das war's schon.


### FAQ
- Gibt es Text-Ausgabemöglichkeiten in die UI (z. B. in der VST)?
  - Nein. Es gibt praktisch keine Ausgabemöglichkeiten außer dem "outport", der eigentlich Ausgaben ins Webinterface schreiben soll, wenn man z. B. auf den RasPi exportiert. (Funktioniert aber auch nicht.) Debuggen ist damit sehr, sehr schwer.
- Was ist die best practice zum debuggen in der DAW?
  - Erst das Plugin 100% fertig machen, dann in die DAW exportieren  
- Wie viel kann man ohne Internetzugang erreichen?
  - Fast gar nichts. Schon beim Starten verbindet sich Max mit dem RNBO-Server, und wenn das nicht klappt, wird die Arbeit nichts.  


### UI/JUCE
- Die UI für ein VST-Plugin muss man mit JUCE bauen. Das ist nicht ganz trivial. Infos hier:
  - https://github.com/Cycling74/rnbo.example.juce
  - https://github.com/Cycling74/rnbo.example.juce/blob/main/CUSTOM_UI.md
  - Fazit: Viel Voraussetzungen, um das bauen zu können. ProJucer kann man sich sicherlich dennoch mal anschauen.


### RasPi
- Geht laut Cycling nur auf RasPi 3 und 4  
- Audio-Interface benötigt, z. B. das übliche Billo-Behringer oder ein USB-Mikro, das class compatible ist. (Das interne Audio-Interface ist als Standard disabled.)
- Guter Überblick und Startpunkt: https://rnbo.cycling74.com/learn/raspberry-pi-target-overview
- Bester Anfang: Raspberry Pi Imager downloaden (ToDo: Link) und RNBO-Image drauf ziehen
  - Mustergültiges Lernvideo: https://www.youtube.com/watch?v=oYBGYqhbRR4&
- Image: https://rnbo.cycling74.com/resources#raspberry-pi-images
- Absolut schönstes Feature, direkt out of the box: 
  - Web-Interface zum Einstellen der Parameter
  - Auch am Handy bedienbar (QR-Code mit URL ist direkt dabei)
  - Achtung, Fallstrick: Das Webinterface kriegt nur initial Daten vom Pi, sendet aber immer, wenn man etwas verstellt. Das hat die kuriose Auswirkung, dass man z. B. per MIDI oder OSC etwas auf dem Pi verstellen kann, im Webinterface sieht man es aber nicht. 
- Grundlegendes Setup, falls noch nichts klappt: http://ip_address:5678/rnbo/inst/0
- https://rnbo.cycling74.com/learn/configuring-audio-on-the-raspberry-pi - Veraltete Doku: Da sollte eigentlich ein entsprecheder Audio-Config-Button sein, der kommt aber nicht mehr (Stand Max 8.5.x). Abhilfe schafft:
  - Das meiste ist jetzt direkt rechts beim Export einstellbar (Samplerate, Audiointerface, Vektorgröße)
  - Das nicht verlinkte Script rpi-configure-audio hilft auch sehr. Liegt bei Windows in "C:\Program Files\Cycling '74\Max 8\resources\packages\RNBO\examples\Utilities\rpi-configure-audio\patchers\rpi-configure-audio.maxpat"

  - Abhilfe schafft außerdem: https://rnbo.cycling74.com/learn/configuring-audio-on-the-raspberry-pi#using-the-osc-interface 
  - Man kann z. B. via OSC alles einstellen; https://github.com/yoggy/sendosc machts möglich (sendosc c74rpi.local 5678 /rnbo/jack/config/card s hw:0)
- "The RNBO Runner exposes an OSC-based interface to JACK, which you can use to select your active soundcard, choose a sample rate, and restart JACK. You can use this OSC interface directly, but it's often easier to use the special audio configuration Max patch included with the RNBO package."

- Fallstricke:
  - Der Username muss "pi" bleiben! https://rnbo.cycling74.com/learn/working-with-the-raspberry-pi-target
  - Debuggen: /usr/bin/rnbooscquery muss laufen
- Wo liegen die Dateien eigentlich? In /home/pi/documents/rnbo. Den Ordner sollte man backuppen.
  - Nice to know: Das Kompilieren findet dann nativ auf dem RasPi statt, d. h. man sollte dafür sorgen, dass auf dem RasPi keine Last ist, wenn man schnell exporten will.

#### Potis etc. einbauen
- Das geht am einfachsten, indem man in Python ein Script schreibt, das die GPIOs ausliest und OSC-Nachrichten baut. 
- Beispiel mit gpiozero und liblo (OSC-Sende-Package)
- https://www.youtube.com/watch?v=LVrVWhJBOL4
  - Nötig dafür: "sudo apt install liblo-dev" und "pip install pyliblo3"

### LCD-Displays und Encoder
- Anleitung: https://www.youtube.com/watch?v=TZ-4zqEvpgo


### Plugins auf Webpages: (ungetestet) 
- HowTo: https://www.youtube.com/watch?v=Q3S3BD1LnCY
- Quelle für das "drumrum": https://github.com/Cycling74/rnbo.example.webpage
  - Webserver nötig, z. B. der in VisualStudioCode eingebaute oder ein lighttpd oder ein nginx...
