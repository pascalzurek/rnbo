# rnbo  

## Grundsätzliche Ressourcen:  
https://rnbo.cycling74.com/  
  
## Installation  
(genaueres: ToDo)  
- Siehe Homepage  
- Sinnvoll: Auch RNBO Guitar Pedals installieren  
  
  
## Was teste ich?  
- Raspberry Pi (welcher geht?)   
- VST, auf allen Plattformen  
- Speed?/Latenz?    
- Web App?
- Gen in dem Objekt?  
- Latenz?  
- (Irgendwas besonderes, wenn man daraus eine Max External baut?)  
  
### Patch 0: "Fingerübung" Stereo Widener in MS  
  
### Patch 1: MHV zu Doppel MS  
  
Horizontal (wie MS):  
L = M + H   
R = M − H   
Vertikal:  
B = M + V   
T = M − V  
- Im Patch ist (zum Testen) auswählbar, ob man auf Kanal 1+2 das L/R- oder das B/T-Signal haben will. (Auf Kanal 3/4 ist immer das B/T-Signal.)
- Das VST nimmt dB als Werte entgegen, dazu den "enum"-Auswähler für LR/BT-Wahl.

  
  
### Common Mistakes/Stolpersteine
- Ohne Internetzugang ist RNBO schwer bis gar nicht zu gebrauchen; bei langsamem Internetzugang hängt Max regelmäßig
- (DNS-Sperre aus Hotels?!)
- Vor Recompile auf jeden Fall das VST in der DAW unloaden. Danach dran denken, dass man einen Neuscan des VST-Ordners machen muss. (Gibt es einen schnelleren Weg? Gibt es einen "einfacheren" VST-Host, damit man nicht immer eine "große" DAW manövrieren muss?)

### Offene Fragen
- Gibt es Text-Ausgabemöglichkeiten in die UI (z. B. in der VST)?  
- Was ist die best practice zum debuggen in der DAW?  
- Was genau kompiliert "Auto Compile"?!  
- Wie viel kann man ohne Internetzugang erreichen?  


### UI/JUCE

https://github.com/Cycling74/rnbo.example.juce

https://github.com/Cycling74/rnbo.example.juce/blob/main/CUSTOM_UI.md

Fazit: Viel Voraussetzungen, um das bauen zu können. ProJucer kann man sich sicherlich dennoch mal anschauen.
