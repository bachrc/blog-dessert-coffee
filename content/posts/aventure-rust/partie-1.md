---
title: "Partie 1 - Création et Bases"
date: 2021-05-21T00:04:03+02:00
draft: false
---

Bonjour, et bienvenue dans cet "article", qui sont plutôt des notes, qui m'ont suivi au long de mes recherches. Rust est un langage qui me semble très prometteur sur le papier, mais peut-on s'en servir afin de façonner un logiciel ? Comment peut-on s'en servir ? Que nous apporte le langage comme outils afin de créer un logiciel lisible, propre, et testé ?

Aujourd'hui, nous voulons voir ce que Rust peut apporter lors du développement logiciel.

Sera-ce un boulet ? Pourquoi est-ce que ce langage fait tant de bruit ?

Présentons très rapidement le langage comme un commercial vendrait son tapis à un client. Rust est un langage compilé et cross-platform. Il est terriblement rapide et économe en mémoire, rivalisant avec du C et C++. Son avantage sur ces langages cependant : sa gestion de la mémoire ! Le cycle de vie de la mémoire est défini à la compilation : aucun garbage collector. Ceci est rendu possible grâce à une architecture du langage adaptée, et intelligente.

Cependant, ces nouveaux concepts rendent l'apprentissage du langage compliqué. La courbe d'apprentissage est très abrupte, et décourage les nouvelles personnes intéressées. Heureusement, le compilateur est dur, mais bienveillant avec des messages d'erreurs très utiles.

Il y a énormément d'articles traitant de Rust en détail, le langage étant extrêmement complexe. Mais aujourd'hui, nous allons simplement découvrir l'outil, et voir s'il peut nous aider afin de réaliser notre projet. Pas un simple Hello World, nous sommes plus originaux que ça. Nous souhaitons un *service de bienveillance*. 

Pourrons-nous développer ce logiciel *proprement* ? Je n'en ai personnellement aucune idée, c'est pour cela que nous allons essayer. Ensemble.

## Installation et bases de Rust

### Compilation basique d'un morceau de code en Rust

Tout d'abord, il va bien évidemment falloir passer par la case installation. Vous pouvez suivre les instructions d'installation disponibles sur le site officiel de Rust, le lien est ci-dessous.

[Lien vers la documentation de Rust](https://doc.rust-lang.org/book/ch01-01-installation.html)

Notre nouveau meilleur ami, c'est désormais `rustc` ! Il s'agit du compilateur de rust.

> **Pour toutes les commandes de cet article, nous assumerons que vous serez sur MacOS, ou une distribution Linux. Veuillez adapter vos commandes pour Windows ! (les commandes comme `cd` ou `mkdir`, par exemple.**

Faisons un premier jet de bienveillance. Nous allons créer un fichier `main.rs` :

```rust
fn main() {
    println!("Coucou ! N'oublie pas de boire de l'eau.");
}
```

De retour dans votre terminal vous pouvez compiler votre grain de bienveillance :

```bash
$ rustc ./main.rs
$ ./main
Coucou ! N'oublie pas de boire de l'eau.
```

Et voilà, merci d'avoir suivi cet article abonnez-vous, mettez la cloche !

Plus sérieusement, rustc est incroyable, mais nous n'allons pas l'utiliser directement. Nous allons passer par un intermédiaire qui va grandement nous faciliter la tâche : cargo.

### Création d'un projet en Rust

Cargo, c'est l'utilitaire de build, ainsi que le gestionnaire de paquets de Rust. Il est très puissant, et va nous permettre de gérer des projets complexes.

Nous allons devoir faire des adieux déchirants à notre grain de bienveillance :

```bash
rm ./main.rs
```

Pour le faire renaître de plus belle. Ta mort ne sera pas vaine, graine de bienveillance. Nous allons te ressusciter.

Nous allons léguer la création de notre projet Rust à Cargo, à l'aide de la commande suivante :

```bash
$ cargo new bienveillance-core --lib
Created library `bienveillance-core` package
$ cd bienveillance-core
```

Vous pouvez voir que nous avons spécifié un —lib, qui ne va pas générer un exécutable, mais une librairie. Notre service de bienveillance n'a pas encore pour but d'être exécuté. Nous n'avons qu'une logique métier. Nous n'avons pas besoin d'exécuter quand nous pouvons tester.

Et nous pouvons voir, ainsi, que des fichiers ont été créés :

```bash
bienveillance-core
├── Cargo.toml
└── src
    └── lib.rs
```

Si nous jetons un oeil au `Cargo.toml` voyons ce qu'il renferme :

```bash
[package]
name = "bienveillance-core"
version = "0.1.0"
authors = ["bachrc <bachrc@dessert.coffee>"]
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```

Notre nouveau service de bienveillance est désormais initialisé, c'est top. Ce fichier `Cargo.toml` va être le fichier renfermant les informations nécessaire à notre build, jusqu'à même pourquoi pas le publier sur les dépôts de librairies de Rust : [https://crates.io](https://crates.io) !

Voyons voir ce que contient notre lib.rs :

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn it_works() {
        assert_eq!(2 + 2, 4);
    }
}
```

C'est un modèle de test par défaut, qui s'assure que.... 2 et 2 font 4.

Nous pouvons tout simplement lancer les tests afin de s'en assurer :

```bash
cargo test
```

Le test passe ! Vous pouvez respirer, l'entropie de notre monde est préservée. Vous pouvez enlever le contenu de ce fichier.

### Quelle application désirons-nous ?

Eh bien moi, ce dont j'ai besoin, c'est qu'on soit gentil avec moi. Recevoir de la chaleur de la part de mon programme. Voyons si Rust peut m'aider à réaliser ça.

Il devra pour l'instant me dire bonjour.

Le fichier `lib.rs` est important ici car il va préciser que notre paquet est une librairie. Mais nous allons enlever les tests dedans, ils n'ont rien à faire ici.

À côté de notre dossier `src`, nous allons créer un dossier `tests`, dans lequel nous allons inscrire notre premier besoin. Quelqu'un pour nous dire bonjour.

## Première fonctionnalité : je veux recevoir un bonjour !

Créons dans le dossier `tests`, un fichier `welcoming_test.rs` :

```rust
#[test]
fn should_welcome_anyone() {
    let welcome_message: String = Welcomer::compute_welcome_message();

    assert_eq!(welcome_message, "Bonjour !")
}
```

C'est le premier code que nous écrivions. Décomposons.

**Première ligne**  
Cet attribut sert d'annotation sur la fonction. Quand nous lancerons les tests à l'aide de Cargo, Cargo reconnaîtra la fonction, et la considèrera comme une fonction de test.

**Deuxième ligne**  
Ici, on déclare la fonction, avec son nom : `should_welcome_anyone`. Qui explicite ce que nous cherchons à implémenter : n'importe doit pouvoir recevoir un bonjour.

**Troisième ligne**  
Celle-ci est plus technique, nous déclarons notre première variable ! Ici nous appellons la fonction `compute_welcome_message` de la classe `Welcomer` qui retourne une variable de type String ! Que nous stockons dans notre variable `welcome_message` à l'aide de l'opérateur `let`.

**Quatrième ligne**  
Nous faisons appel à la macro `assert_eq!`, que nous utilisons afin de nous assurer que notre variable `welcome_message` vaut bien notre bonjour !

Mais là vous vous demandez sûrement : mais qu'est-ce que c'est qu'une macro ? Ce n'est pas une fonction ?

Non ! Une macro est, en quelque sorte, du code qui va générer du code ! Elles sont caractérisées par le point d'exclamation à la fin de leur nom. Elles sont en général utilisées en tant que sucre syntaxique, afin de rendre le code plus lisible. Chacun peut créer ses propres macros, mais ceci est complexe, et peut-être dangereux pour la maintenabilité de votre code ! A utiliser avec parcimonie.

Voici pour les explications de ces quelques lignes. Allons créer notre structure `Welcomer`, car pour le moment, nos tests ne passent pas, mais pas pour la bonne raison : le code ne compile pas !

### Notre premier objet !

Créons le fichier `src/welcomer.rs` :

```rust
pub struct Welcomer;

impl Welcomer {
    pub fn compute_welcome_message() -> String {
        String::from("")
    }
}
```

Que d'émotions, nous avons créé notre premier objet, notre premier **struct**ure de données en Rust !

Décomposons ensemble, ligne par ligne, ce morceau de code.

**Première ligne**  
Ici nous déclarons notre structure de données, super ! C'est ici que nous pouvons déclarer les attributs de notre objet. Pour le moment, notre objet n'en a aucun. Alors nous ne déclarons que son nom. Nous la déclarons en `pub`lique afin de pouvoir y avoir accès en dehors du fichier.

**Deuxième ligne**  
Nous allons ici déclarer les comportements de notre structure de données. Il est totalement possible d'avoir une `struct` sans avoir d' `impl` émentation, l'inverse n'est pas possible, car nous implémentons un comportement à la structure précédemment déclarée.

**Troisième ligne**  
Nous déclarons la fonction `pub`lique `compute_welcome_message` ! J'insiste sur le fait que c'est une *fonction* et non une *méthode* ! La fonction ne dépend pas d'une instance de la structure de données (similaire à ce qu'est une fonction statique par exemple).

Nous notons également que notre type de retour est un **String**. Ceci est important pour la suite des évènements, qui arrive...... maintenant.

**Quatrième ligne**  
J'imagine que vous vous posez plusieurs questions en voyant cette ligne. Nous allons y répondre ensemble.

> **La fonction est censée retourner un String, où est le `return` ?**

En Rust, la dernière instruction d'une fonction/méthode est ce qui est retourné ! Soit ici, l'objet `String` renvoyé par `String::from("")`

> **Pourquoi mange-t-on certains animaux, et en domestiquons d'autres ?**

Alors je ne vais pas répondre à cette question

> **Pourquoi retournons-nous `String::from("")` et non pas "" directement ?**

Et c'est là que le bât blesse. Disons simplement que notre `""` n'est pas un `String`. Mais un `&str`. Je vais tenter de vous résumer une des fonctionnalités les plus importantes et complexes de Rust : l'Ownership.

### L'Ownership : quel enfer

L'Ownership est une des fonctionnalités centrales du langage, elle est notamment responsable de l'établissement du cycle de vie de la mémoire de Rust à la compilation, avec son système de responsabilités de variables.

C'est un principe complexe que nous n'allons qu'effleurer ici. Je vous reconduis vers le livre de Rust afin de l'explorer plus en détails : [https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html)

L'Ownership est régi par trois règles :

- Chaque valeur en Rust possède une variable qui est appelée sa propriétaire.
- Il ne peut y avoir qu'un responsable à la fois
- Quand le responsable sort du scope : la valeur est nettoyée.

Prenons la fonction suivante.

```rust
// Attention : ce code ne COMPILE PAS

fn main() {
    let salutation = String::from("Coucou la petite COMMU");
    
    let longueur_de_salutation = calculate_length(salutation);

    println!("La chaine {} fait {} caractères !", salutation, longueur_de_salutation);
}

fn calculate_length(some_string: String) -> usize {
    some_string.len()
}
```

Cependant, ce code ne compile pas. Pourquoi donc ?

**Ligne 4**  
La variable `salutation` a la propriété de sa valeur.

**Ligne 6**  
Nous déléguons la propriété du contenu de la valeur jusqu'ici possédée par `salutation` à la fonction `calculate_length`. **La fonction calculate_length est désormais propriétaire de la valeur de contenu**

**Ligne 12**  
La fonction retourne la longueur de la chaine de caractère dont elle a la propriété. **Cependant**, elle ne fait rien de la valeur de la chaine de caractère. **La valeur de la chaine de caractères est donc nettoyée.**

**Ligne 8**  
L'erreur se situe ici. La propriété de la variable ayant été déléguée à `calculate_length`, la variable `salutation` ne possède plus rien ! On ne peut plus l'afficher, ni même l'utiliser. Son contenu a été nettoyé à la sortie du scope de `calculate_length`.

Quand nous compilons le code, cette erreur nous est indiquée de manière très explicite, regardez.

```
error[E0382]: borrow of moved value: `salutation`
 --> src/main.rs:8:51
  |
4 |     let salutation = String::from("Coucou la petite COMMU");
  |         ---------- move occurs because `salutation` has type `String`, which does not implement the `Copy` trait
5 |     
6 |     let longueur_de_salutation = calculate_length(salutation);
  |                                                   ---------- value moved here
7 | 
8 |     println!("La chaine {} fait {} caractères !", salutation, longueur_de_salutation);
  |                                                   ^^^^^^^^^^ value borrowed here after move

error: aborting due to previous error

For more information about this error, try `rustc --explain E0382`.
error: could not compile `playground` 
```

Comme nous l'avons spécifié auparavant : le compilateur nous fait mal, mais en nous expliquant ce qu'on a fait de mal. En nous aidant à ne plus répéter l'erreur, en nous faisant progresser. `rustc`, c'est plus qu'un compilateur. *C'est un ami.*

> *Quoi ? Ça veut dire qu'à chaque fois que je veux mettre une variable en paramètre d'une fonction, je ne peux plus l'utiliser derrière ?! Il est tout pété ton langage là !*

Fort heureusement, dans le monde actuel, lorsqu'il n'est pas possible d'acquérir un bien, nous avons la CHANCE de pouvoir faire un emprunt ! Et c'est pareil en Rust, avec la mécanique du Borrowing, de l'emprunt.

### Emprunter des valeurs

En Rust, il est possible d'expliciter que nous ne souhaitons pas avoir la propriété d'une variable, mais seulement une référence à sa valeur. Modifions notre code afin d'utiliser la mécanique de l'emprunt.

```rust
// Attention : ce code COMPILE

fn main() {
    let salutation = String::from("Coucou la petite COMMU");
    
    let longueur_de_salutation = calculate_length(&salutation);

    println!("La chaine {} fait {} caractères !", salutation, longueur_de_salutation);
}

fn calculate_length(some_string: &String) -> usize {
    some_string.len()
}
```

Vous pouvez noter deux différences ici, avec l'introduction du caractère de référence `&` :

Ligne 6 : Nous ne donnons plus la propriété de la valeur de `salutation` à `calculate_length` : nous donnons une **référence** à la valeur de `salutation`. Notre variable `salutation` garde ainsi la propriété de sa valeur !
Ligne 11 : La fonction `calculate_length` indique qu'elle ne requiert plus la propriété de la valeur : elle n'indique n'avoir besoin que d'une référence à la valeur. A la fin du scope de la fonction, nous allons juste mettre fin à l'emprunt ! Et notre programme va ainsi pouvoir compiler. Vérifions !

```
La chaine Coucou la petite COMMU fait 22 caractères !
```

Notre programme avec l'emprunt fonctionne désormais !

> *Ok... mais pourquoi on parle de tout ça au fait ? On ne parlait pas de chaîne de caractères ?*

Oui ! Revenons au point de base. Pourquoi y a-t-il `&str` et `String` ?

Très simplement : `&str` est une référence à une partie d'une `String`. Lorsque vous compilez votre code avec des chaînes de caractères, vos chaînes de caractères se situent dans une mémoire allouée à des valeurs en lecture seule. Lorsque votre programme tourne : *votre variable est donc une référence à ce texte alloué dans cette mémoire en lecture seule.* Tout simplement parce que votre variable n'en a pas la propriété : cette mémoire en lecture seule en a la propriété. Vous ne faites qu'un emprunt.

Revenons à notre code.

```rust
pub struct Welcomer;

impl Welcomer {
    pub fn compute_welcome_message() -> String {
        String::from("")
    }
}
```

Pourquoi donc nous préférons retourner un `String` plutôt qu'un `&str` ? Pour pouvoir donner la propriété de la chaîne de caractères à l'appelant de la fonction, afin de nous permettre une plus grande souplesse lors de l'utilisation.

Voilà, en résumé, l'explication de l'ownership ! Prenez un petit café, un verre d'eau, ce sont des notions très complexes pour des n3wbi3s sur le langage. Ne laissez pas ça vous décourager ! Cela paraît effrayant, mais cela devient juste de la gymnastique mentale, et à force cela devient naturel !

Revenons à nos tests, maintenant.

### Ah oui, les tests ! Ils sont au rouge.

Lorsque nous lançons nos tests, ils sont au rouge. Regardons ça de plus près.

```
Left:  
Right: Bonjour !

thread 'should_welcome_anyone' panicked at 'assertion failed: `(left == right)`
  left: `""`,
 right: `"Bonjour !"`', tests/welcoming_test.rs:7:5
```

Ah bah oui, notre test attend un bonjour, mais nous n'obtenons qu'une chaîne vide ! Implémentons ça fissa !

`src/welcomer.rs`

```rust
pub struct Welcomer;

impl Welcomer {
    pub fn compute_welcome_message() -> String {
        String::from("Bonjour !")
    }
}
```

Relançons nos tests. Est-ce que ça s'est bien passé ?

![Tests au vert](illu-1.png)

Tout ça m'a l'air d'être parfait !

Nous avons implémenté notre première fonctionnalité de `Hello World` ! Et en plus, la fonctionnalité est testée. Superbe !

Dans le prochain article, nous allons continuer nos cycles de développement, afin d'obtenir

