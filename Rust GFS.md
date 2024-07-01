# Historie

- Rust ist eine relativ neue/moderne Sprache.
- War zunächst ein kleines Projekt bei Mozilla (den Firefox-Machern)
- Wurde von Mozilla zuerst 2009 angekündigt
- Erste stabile Version wurde am **10. Mai 2015** veröffentlicht
- War sehr stark von den Mozilla-Entlassungen 2020 betroffen
- Gründung der Rust Foundation am 8. Februar 2021

---

# Allgemein

Wenn man auf die offizielle [Rust-Website](rust-lang.org) geht, sieht man 3 Punkte, die besonders für die Sprache sprechen sollen:

- Performance
- Reliability = Zuverlässigkeit
- Productivity

Über alle drei Punkte will ich heute sprechen und Rust mit anderen Sprachen vergleichen

## Performance

Fangen wir an mit Performance: Rust hat den Anspruch eine neue, moderne "**System-Programmiersprache**" zu sein.
Was heißt das?
Rust sieht sich selbst als eine Alternative zu C/C++, das heißt Rust soll dafür geeignet sein, in Anwendungen verwendet zu werden, bei denen Performance höchste Priorität hat.
Zu diesen Anwendungen gehören zum Beispiel:

- Betriebssystem-Kernel
- Embedded-Anwendungen
- Applikationen, die besonders viel Performance brauchen (Physik-Simulationen, Graphik-Rendering)

Dieser große Fokus auf Performance unterscheidet Rust zum Beispiel von Sprachen wie Java, oder besonders Python und Javascript, die im Allgmeinenen langsamer sind.

Benchmark in Rust, Java und Python, der diesen Performance Unterschied verdeutlicht. Es wird jweils die n-te Zahl der Fibonacci-Folge berechnet, für verschiedene n-s von 10 - 1.000.000.
Die Ergebnisse sind die Folgenden:

| n         | Rust   | Java    | Python |
| --------- | ------ | ------- | ------ |
| 10        | 0,000s |  0,000s | 0,000s |
| 100       | 0,000s |  0,000s | 0,000s |
| 1.000     | 0,000s |  0,000s | 0,000s |
| 10.000    | 0,000s |  0,002s | 0,001s |
| 100.000   | 0,042s |  0,154s | 0,104s |
| 1.000.000 | 4,316s | 14,910s | 9,659s |

(alle Ergebnisse auf 3 Nachkommastellen gerundet)

Rust eindeutig die schnellste Ausführungszeit, Python und Java deutlich langsamer.

**Woran liegt das?**

Bei den anderen Beispielen existiert neben eurem eigenen Code auch noch eine sogenannte Laufzeitumgebung. Diese kümmert sich zum Beispiel darum, dass nicht mehr benötigte Variablen aus dem Speicher gelöscht werden. Das macht es für die Entwickler einfacher in diesen Sprachen zu entwickeln, da man sich nicht darum selber kümmern muss - das macht die Sprache automatisch.

Der Vorteil ggü. Java, Python usw. ist also bessere Performance.

**Was aber ist der Vorteil ggü. C/C++?**

## Reliability

Damit kommen wir zum Thema Reliability.

### Speichermanagment/Memory Safety

In C++ und C muss man sich als Entwickler selber um den Speicher kümmern. Das bedeutet in der Praxis: Man erstellt eine Variable oder ein Objekt im Speicher - zum Beispiel eine Klasseninstanz in C++. Es wird also der benötigte Speicher im RAM bereitgestellt an einer bestimmten Speicheradresse bereitgestellt. Diese Speicheradresse wird im sogenannten Pointer gespeichert.

Dabei können mehrere Szenarien entstehen, die ein Problem darstellen können:

#### 1. Dangling Pointer / Use After Free

Es können mehrere Pointer auf die gleiche Speicheradresse und somit das gleiche Objekt verweisen. Wenn von einem dieser Pointer das Objekt gelöscht wird, existiert der andere Pointer immernoch. Er "zeigt" jetzt allerdings nicht mehr auf irgendein sinnvolles Objekt. Das kann zu zahlreichen Sicherheitsrisiken führen. Der Speicher könnte jetzt von einer anderen Variable genutzt werden, was eine große Sicherheitslücke ist

#### 2. Memory Leak

Es kann zu einem Memory-Leak kommen, wenn der Pointer vom Programm nicht mehr erreicht werden kann, der eigentliche Inhalt aber noch nicht gelöscht wurde. Dann wird Speicher unnötig verbraucht. Wenn das bei einem Programm oft passiert kann es zu einem Crash kommen.

#### 3. Out of Bounds

Wenn versucht wird, in einem Array Elemente abzurufen, die nicht exisitieren

#### Wichtigkeit

ca. 70% aller Sicherheitslücken werden durch diese Probleme hervorgerufen
Viele der größten Sicherheitslücken: *WannaCry*, *Trident*, *Heartbleed*, *Stagefright*, etc.

![image](https://msrc.microsoft.com/blog/2019/07/we-need-a-safer-systems-programming-language/wp-content-uploads-2019-07-image-1.png)



#### Der Ansatz von Rust

In Rust werden bestimmte Regeln vom Compiler erzwungen, die solches Verhalten in den allermeisten Fällen verhindern können. Dies führt dazu, dass Rust-Programme generell sicherer sind.

Um sicherzustellen, dass es keine Speicherfehler auftreten, gibt es in Rust einige Regeln

**Ownershipregeln:**

1. Jede Variable hat einen *Besitzer*
2. Es kann zu jedem Zeitpunkt nur einen Besitzer geben
3. Wenn der Besitzer nicht mehr erreichbar ist, wird die Variable gelöscht

**Referenzregeln:**

1. Es darf zu jedem Zeitpunkt entweder eine veränderliche Referenz ODER unbegrenzt viele nicht veränderliche Referenzen geben
2. Eine Referenz muss immer valide sein

##### Beispiele

###### 1:
	
```rust
fn main() {
	let name = String::from("Herbert");
	print_text(name);
	print_text(name);
}
						
fn print_text(name: String) {
	println!("{}", name);
}
```
- wird nicht funktionieren, da `name` verschoben wird

###### 2:

```rust
fn main() {
	let name = String::from("Herbert");
	print_text(&name);
	print_text(&name);
}

fn print_text(name: &String) {
	println!("{}", name);
}
```

- wird funktionieren, da `&` eine nicht veränderliche Referenz anzeigt
- Regel: unbegrenzt viele unveränderliche Referenzen ODER eine veränderliche Referenz erfüllt

###### 3:

```rust
fn main() {
	let mut name = String::from("Herbert");
	let reference = &name;
	print_text(reference);
	add_second_name(&mut name);
	print_text(reference);
}

fn print_text(name: &String) {
	println!("{}", name);
}

fn add_second_name(name: &mut String) {
	name.push_str("Hans");
}
```

- wird nicht funktionieren, da gleichzeitig veränderliche und unveränderliche Referenzen bestehen

###### 4:

```rust
fn main() {
	let mut name = String::from("Herbert");
	print_text(&name);
	add_second_name(&mut name);
	print_text(&name);
}

fn print_text(name: &String) {
	println!("{}", name);
}

fn add_second_name(name: &mut String) {
	name.push_str("Hans");
}
```

- wird funktionieren, da die Referenzen nie gleichzeitig exisitieren

## Productivity

- ist der strittiste Punkt, denn es sprechen einige Punkte dafür, einige dagegen:

### Dafür:

- kompakter, bekannter Syntax
- Fehlernachrichten sind WIRKLICH hilfreich
- Regeln vermeiden Fehler, die gedebuggt werden müssen

### Dagegen:

- Regeln halten einen auf, man muss darüber nachdenken, wie man den Compiler zufriedenstellt
- "Fight the Borrow-Checker"

---

# Schluss

## Bekanntere Rust-Projekte

- Zed-Editor
- Cosmic-Desktop
- Bevy-Engine
- Linux Kernel

-> Noch nicht viele wirklich bekannte Projekte, die Rust benutzen, da es eine noch relativ junge Sprache ist

## Wo Starten?

- Rust-Buch
- Rust by Example
- Rustlings

(sind aber alle auf Englisch)