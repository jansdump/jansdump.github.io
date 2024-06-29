# Allgemein

Wenn man auf die offizielle [Rust-Website](rust-lang.org) geht, sieht man 3 Punkte, die besonders für die Sprache sprechen sollen:

- Performance
- Reliability = Zuverlässigkeit
- Productivity

Über alle drei Punkte will ich heute sprechen und Rust mit anderen Sprachen vergleichen

# Performance

Fangen wir an mit Performance: Rust hat den Anspruch eine neue, moderne "**System-Programmiersprache**" zu sein.
Was heißt das?
Rust sieht sich selbst als eine Alternative zu C/C++, das heißt Rust soll dafür geeignet sein, in Anwendungen verwendet zu werden, bei denen Performance höchste Priorität hat.
Zu diesen Anwendungen gehören zum Beispiel:

- Betriebssystem-Kernel
- Embedded-Anwendungen
- Applikationen, die besonders viel Performance brauchen (Physik-Simulationen, Graphik-Rendering)

Dieser große Fokus auf Performance unterscheidet Rust zum Beispiel von Sprachen wie Java, C# oder besonders Python und Javascript, die im Allgmeinenen langsamer sind.

Ich habe mal einen Benchmark in allen 5 Sprachen sowie C/C++ gemacht um das zu vergleichen:

Auch viele andere Benchmarks bestätigen das:

Man sieht das Rust und C/C++ ziemlich genau gleich schnell sind, Java und C# langsamer, sowie Python und JavaScript sehr viel langsamer sind.
Woran liegt das?
Rust, C, usw. sind kompilierte Sprachen. Das heißt der geschriebene Code wird von einem Compiler (bei Rust i.d.R *rustc*) in Maschinencode übersetzt - dieser Maschinencode kann mit *Assembly* dargestellt werden 

``` Rust
fn main() {
	println!("Hallo Welt");
}
```

**Assembly hier**

Dieser Maschinencode entspricht direkt Befehlen die eure CPU ausführen kann - es wird also wirklich NUR das ausgeführt, was ihr auch explizit geschrieben habt.

Bei den anderen Beispielen existiert neben eurem eigenen Code auch noch eine sogenannte Laufzeitumgebung. Diese kümmert sich zum Beispiel darum, dass nicht mehr benötigte Variablen aus dem Speicher gelöscht werden. Das macht es für die Entwickler einfacher in diesen Sprachen zu entwickeln, da man sich nicht darum selber kümmern muss - das macht die Sprache automatisch.

Der Vorteil ggü. Java, Python usw. ist also bessere Performance.
**Was aber ist der Vorteil ggü. C/C++?**

# Reliability

Damit kommen wir zum Thema Reliability.

## Speichermanagment

In C++ und C muss man sich als Entwickler selber um den Speicher kümmern. Das bedeutet in der Praxis: Man erstellt eine Variable oder ein Objekt im Speicher - zum Beispiel eine Klasseninstanz in C++. Es wird also der benötigte Speicher im RAM bereitgestellt an einer bestimmten Speicheradresse bereitgestellt. Diese Speicheradresse wird im sogenannten Pointer gespeichert.

Dabei können mehrere Szenarien entstehen, die ein Problem darstellen können:

### 1. Dangling Pointer

Es können mehrere Pointer auf die gleiche Speicheradresse und somit das gleiche Objekt verweisen. Wenn von einem dieser Pointer das Objekt gelöscht wird, existiert der andere Pointer immernoch. Er "zeigt" jetzt allerdings nicht mehr auf irgendein sinnvolles Objekt. Das kann zu zahlreichen Sicherheitsrisiken führen

### 2. Memory Leak

Es kann zu einem Memory-Leak kommen, wenn der Pointer vom Programm nicht mehr erreicht werden kann, der eigentliche Inhalt aber noch nicht gelöscht wurde. Dann wird Speicher unnötig verbraucht. Wenn das bei einem Programm oft passiert kann es zu einem Crash kommen.

### Der Ansatz von Rust

In Rust werden bestimmte Regeln vom Compiler erzwungen, die solches Verhalten in den allermeisten Fällen verhindern können. Dies führt dazu, dass Rust-Programme generell sicherer sind.

#### Beispiel:

``` Rust
fn main() {
    let name: String = "Herbert".to_string();
    
    print_string(name); // -> name wird zu print_string() bewegt
    print_string(name); // Error
}

fn print_string(text: String) {
    println!("{}", text);
}
```

Dieser Code gibt einen Fehler aus: "**error[E0382]**: use of moved value: `name`"

Um sicherzustellen, dass es keine Speicherfehler auftreten, gibt es in Rust einige Regeln

**Ownershipregeln:**

1. Jede Variable hat einen *Besitzer*
2. Es kann zu jedem Zeitpunkt nur einen Besitzer geben
3. Wenn der Besitzer nicht mehr erreichbar ist, wird die Variable gelöscht

**Referenzregeln:**

1. Es darf zu jedem Zeitpunkt entweder eine veränderliche Referenz ODER unbegrenzt viele nicht veränderliche Referenzen geben
2. Eine Referenz muss immer valide sein
	
``` Rust
fn main() {
    let name: String = "Herbert".to_string();

    print_string(&name);
    print_string(&name);
}

fn print_string(text: &String) {
    println!("{}", text);
}
```

``` Rust
fn main() {
    let mut name: String = "Herbert".to_string();
    
    let name_reference = &mut name;
    
    add_surname(name_reference);
    
    print_string(&name);  
}

fn print_string(text: &String) {
    println!("{}", text);
}

fn add_surname(text: &mut String) {
    text.push_str(" Schmidt");
}
```
# Productivity

| Hallo | Hallo |
| ----- | ----- |
|       |       |
