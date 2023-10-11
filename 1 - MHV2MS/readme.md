Erläuterung:

Das Plugin „dekodiert“ ein Mitten- ein Höhen- und ein Vertikalsignal ähnlich, wie man MS in
LR dekodieren kann. Dabei gilt:

Horizontal (wie MS):
L = M + H, R = M − H

Vertikal:
B = M + V, T = M − V

Im Patch ist (zum Testen) auswählbar, ob man auf Kanal 1+2 das L/R- oder das B/T-Signal
haben will. (Auf Kanal 3/4 ist immer das B/T-Signal.) 
Das VST nimmt dB als Werte entgegen, dazu den “enum”-Auswähler für LR/BT-Wahl.

Testfiles finden sich unter ../waves.
