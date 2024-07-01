**Quelle:** https://www.memorysafety.org/docs/memory-safety/

# Zusammenfassung

## Arten von Bugs

### Out-of-Bounds Reads & Writes

- z.B. in einem Array mit 10 Elementen das 11. Element lesen oder zum 11. Element schreiben
- Memory Safe Sprache sollte das nicht erlauben
- Möglichkeiten, das Problem zu lösen: Error beim Kompilieren, Crashen beim Ausführen

### Use After Free/Dangling Pointer

- Wenn versucht wird, ein Objekt/Ähnliches zu nutzen, nachdem es bereits gelöscht wurde
- Adresse könnte bereits von etwas anderem genutzt werden
- kann durch Dangling Pointer hervorgerufen werden

## Wie wichtig ist Memory Safety?

- 70% aller Schwächen der letzten 10 Jahre in Microsoft Projekte waren Probleme der Speichersicherheit
    **Quelle:** https://msrc.microsoft.com/blog/2019/07/we-need-a-safer-systems-programming-language/
    Bild: https://msrc.microsoft.com/blog/2019/07/we-need-a-safer-systems-programming-language/wp-content-uploads-2019-07-image-1.png
- Viele der größten Sicherheitslücken: *WannaCry*, *Trident*, *Heartbleed*, *Stagefright*, etc.
- Macht Programme stabiler, da weniger Bugs auftreten können
- Für Multi-Threading noch viel wichtiger

