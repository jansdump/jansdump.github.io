# Der Test

Die Tests wurden in den Sprachen Rust, C, Java und Python durchgeführt.
Es wurde immer die Release-Build Option ausgewählt. Die CPU war immer ein Intel i5-10400.

Der Test bestand darin, die n-te Zahl der Fibonacci-Folge zu berechnen.
Hierfür wurde der folgende Algorithmus verwendet (Pseudocode, abgewandelt für jede Sprache):

'''
fib(n) {
    f0 = 0
    f1 = 1
    
    repeat n {
        f2 = f0 + f1
        f0 = f1
        f1 = f2
    }

    return f0
}
'''

Für jeden Aufruf dieser Funktion wurde die Zeit berechnet, die zum Berechnen benötigt wurde.

Die gewählten n-s waren:

    1. n = 10
    2. n = 100
    3. n = 1.000
    4. n = 10.000
    5. n = 100.000
    6. n = 1.000.000

Der Algorithmus wurde in jeder Sprache für jedes n 100 mal wiederholt. Dabei wurde der Durchschnitt und Median sowiel langsamster bzw. schnellster Wert berechnet.

# Ergebnisse

In der folgenden Tabelle werden jeweils die Durchschnittswerte für n und Sprache angegeben.

| n         | Rust   | Java    | Python |
| --------- | ------ | ------- | ------ |
| 10        | 0,000s |  0,000s | 0,000s |
| 100       | 0,000s |  0,000s | 0,000s |
| 1.000     | 0,000s |  0,000s | 0,000s |
| 10.000    | 0,000s |  0,002s | 0,001s |
| 100.000   | 0,042s |  0,154s | 0,104s |
| 1.000.000 | 4,316s | 14,910s | 9,659s |

(alle Ergebnisse auf 3 Nachkommastellen gerundet)