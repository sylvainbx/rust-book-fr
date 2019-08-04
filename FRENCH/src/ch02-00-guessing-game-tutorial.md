<!--
# Programming a Guessing Game
-->

# Programmer un jeu de devinettes

<!--
Let’s jump into Rust by working through a hands-on project together! This
chapter introduces you to a few common Rust concepts by showing you how to use
them in a real program. You’ll learn about `let`, `match`, methods, associated
functions, using external crates, and more! The following chapters will explore
these ideas in more detail. In this chapter, you’ll practice the fundamentals.
-->

Entrons dans le vif du sujet en travaillant ensemble sur un projet concret.
Ce chapitre présente quelques concepts couramment utilisés en Rust en les
mettant en place dans un véritable programme. Nous verrons par exemple les
mots clés `let` et `match`, les méthodes et fonctions associées, ainsi que
l'utilisation de librairies externes (les `crates`) et bien plus encore.
Au cours de ce chapitre, nous verrons les bases de ces concepts à l'aide
d'exemples concrets, et les chapitres qui suivent les présenteront en détails.

<!--
We’ll implement a classic beginner programming problem: a guessing game. Here’s
how it works: the program will generate a random integer between 1 and 100. It
will then prompt the player to enter a guess. After a guess is entered, the
program will indicate whether the guess is too low or too high. If the guess is
correct, the game will print a congratulatory message and exit.
-->

Nous allons créer un programme fréquemment réalisé par les débutants en
programmation, un jeu de devinette. Le principe de ce jeu est le suivant:
le programme va générer un nombre aléatoire entre 1 et 100. Ce sera ensuite au
joueur d'entrer un nombre qu'il aura deviné, et le programme indiquera si le
nombre fourni par le joueur est trop grand ou trop petit. Si le nombre deviné
par le joueur est le bon, le programme affiche un message de félicitations
et se termine.

<!--
## Setting Up a New Project
-->

## Mise en place du projet

<!--
To set up a new project, go to the *projects* directory that you created in
Chapter 1 and make a new project using Cargo, like so:
-->

Pour créer un nouveau projet, rendez-vous dans le dossier *projets* que
vous avez créé au chapitre 1, et utilisez Cargo pour initialiser votre projet.

```text
$ cargo new guessing_game
$ cd guessing_game
```

<!--
The first command, `cargo new`, takes the name of the project (`guessing_game`)
as the first argument. The second command changes to the new project’s
directory.
-->

La première commande, `cargo new`, requiert le nom de notre projet (`deviner_nombre`)
en tant que premier argument. L'option `--bin` informe Cargo que notre projet
formera un programme exécutable, comme celui du chapitre 1, et non une librairie.
La seconde commande nous place dans le dossier de notre projet nouvellement
crée par Cargo.

<!--
Look at the generated *Cargo.toml* file:
-->

Regardons le fichier *Cargo.toml* automatiquement crée:

<!--
<span class="filename">Filename: Cargo.toml</span>
-->

<span class="filename">Fichier: Cargo.toml</span>

```toml
[package]
name = "guessing_game"
version = "0.1.0"
authors = ["Your Name <you@example.com>"]
edition = "2018"

[dependencies]
```

<!--
If the author information that Cargo obtained from your environment is not
correct, fix that in the file and save it again.
-->

Si le nom d'auteur que Cargo a obtenu depuis votre environnement n'est pas
correct, vous pouvez le changer dans ce fichier et le sauvegarder.

<!--
As you saw in Chapter 1, `cargo new` generates a “Hello, world!” program for
you. Check out the *src/main.rs* file:
-->

Tel qu'expérimenté dans le chapitre 1, `cargo new` génère un programme de base,
un “Hello, world!” (que l'on pourrait traduire par “Bonjour tout le monde”).

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

<!--
Now let’s compile this “Hello, world!” program and run it in the same step
using the `cargo run` command:
-->

Maintenant, lançons la compilation de ce programme “Hello, world!”, et
démarrons-le. Il est possible de faire ces deux actions en une seule commande,
`cargo run`:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 1.50 secs
     Running `target/debug/guessing_game`
Hello, world!
```

<!--
The `run` command comes in handy when you need to rapidly iterate on a project,
as we’ll do in this game, quickly testing each iteration before moving on to
the next one.
-->

Cette commande `run` est très pratique lorsque vous souhaitez itérer rapidement
sur un projet, et c'est le cas ici: on veut lancer notre jeu, le tester,
faire une modification, et passer à l'essai suivant.

<!--
Reopen the *src/main.rs* file. You’ll be writing all the code in this file.
-->

Ouvrez à nouveau le fichier *src/main.rs*. C'est ici que nous écrirons la totalité
de notre code Rust.

<!--
## Processing a Guess
-->

## Traitement des entrées utilisateur

<!--
The first part of the guessing game program will ask for user input, process
that input, and check that the input is in the expected form. To start, we’ll
allow the player to input a guess. Enter the code in Listing 2-1 into
*src/main.rs*.
-->

La première partie du programme consiste à demander au joueur de fournir un nombre,
et à vérifier que ce que l'utilisateur entre correspond au format attendu.
Commençons par permettre au joueur d'entrer le nombre qu'il a deviné.
Ajoutez le code de la Figure 2-1 dans le fichier *src/main.rs*.

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier: src/main.rs</span>

<!--
```rust,ignore
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```
-->

```rust,ignore
use std::io;

fn main() {
    println!("Jeu de Plus ou Moins");

    println!("Entrez votre déduction.");

    let mut deduction = String::new();

    io::stdin().read_line(&mut deduction)
        .expect("Echec de la lecture de l'entrée utilisateur");

    println!("Votre déduction: {}", deducation);
}
```

<!--
<span class="caption">Listing 2-1: Code that gets a guess from the user and
prints it</span>
-->

<span class="caption">Figure 2-1: Code permettant de lire une entrée utilisateur et de l'afficher</span>

<!--
This code contains a lot of information, so let’s go over it line by line. To
obtain user input and then print the result as output, we need to bring the
`io` (input/output) library into scope. The `io` library comes from the
standard library (which is known as `std`):
-->

Ce code contient beaucoup d'information, nous allons donc le détailler petit
à petit. Pour obtenir l'entrée utilisateur et l'afficher, nous avons besoin d'importer
la librairie `io` (pour input/output, entrée/sortie) afin de pouvoir l'utiliser.
La librairie `io` provient de `std`, la librairie standard du langage Rust.

```rust,ignore
use std::io;
```

<!--
By default, Rust brings only a few types into the scope of every program in
[the *prelude*][prelude]<!-- ignore -- >. If a type you want to use isn’t in the
prelude, you have to bring that type into scope explicitly with a `use`
statement. Using the `std::io` library provides you with a number of useful
features, including the ability to accept user input.
-->

Par défaut, Rust n'importe que quelques types utilisables dans les programmes,
ceux qui sont listés dans le [le *prelude*][prelude]<!-- ignore -->. Si vous
voulez utiliser un type qui ne s'y trouve pas, vous devrez l'importer au moyen
du mot clé `use`. Lorsque l'on importe `std::io`, on rend disponible de nombreuses
fonctionnalités du domaine des entrées/sorties, comme par exemple la possibilité
de traiter les entrées de l'utilisateur.

[prelude]: ../std/prelude/index.html

<!--
As you saw in Chapter 1, the `main` function is the entry point into the
program:
-->

Tel qu'expliqué au Chapitre 1, la fonction `main` est le point d'entrée
dans le programme, c'est ici que l'exécution démarre.

```rust,ignore
fn main() {
```

<!--
The `fn` syntax declares a new function, the parentheses, `()`, indicate there
are no parameters, and the curly bracket, `{`, starts the body of the function.
-->

Le mot clé `fn` déclare une nouvelle fonction, les `()` indiquent que cette
fonction n'accepte aucun paramètre, et `{` marque le début du corps de la fonction.

<!--
As you also learned in Chapter 1, `println!` is a macro that prints a string to
the screen:
-->

Vous avez également vu lors de votre lecture du Chapitre 1 que `println!` est une
macro qui affiche une chaine de caractères à l'écran.

<!--
```rust,ignore
println!("Guess the number!");

println!("Please input your guess.");
```
-->

```rust,ignore
println!("Jeu de Plus ou Moins");

println!("Entrez votre déduction.");
```

<!--
This code is printing a prompt stating what the game is and requesting input
from the user.
-->

Ce code permet simplement d'afficher le titre de notre jeu, et de demander
au joueur d'entrer un nombre.

<!--
### Storing Values with Variables
-->

### Enregistrer des données grace aux variables

<!--
Next, we’ll create a place to store the user input, like this:
-->

Créons maintenant un emplacement pour enregistrer l'entrée utilisateur:

```rust,ignore
let mut guess = String::new();
```

<!--
Now the program is getting interesting! There’s a lot going on in this little
line. Notice that this is a `let` statement, which is used to create a
*variable*. Here’s another example:
-->

Le programme commence à devenir intéressant ! Il se passe beaucoup de choses
lorsque cette simple ligne est exécutée. Vous remarquerez qu'elle commence par
le mot clé `let`, qui sert à créer des *variables*. En voici un autre exemple:

```rust,ignore
let foo = bar;
```

<!--
This line creates a new variable named `foo` and binds it to the value of the
`bar` variable. In Rust, variables are immutable by default. We’ll be
discussing this concept in detail in the [“Variables and
Mutability”][variables-and-mutability]<!-- ignore -- > section in Chapter 3.
The following example shows how to use `mut` before the variable name to make
a variable mutable:
-->

Cette ligne permet de créer une nouvelle variable nommée `foo` et à lui
assigner la valeur de `bar`. Par défaut en Rust, les variables sont immuables,
c'est-à-dire qu'il est impossible de modifier leur valeur. Le prochain exemple
montre comment utiliser le mot clé `mut` pour autoriser la modification de la
valeur d'une variable.

<!--
```rust,ignore
let foo = 5; // immutable
let mut bar = 5; // mutable
```
-->

```rust
let foo = 5; // immuable
let mut bar = 5; // modifiable
```

<!--
> Note: The `//` syntax starts a comment that continues until the end of the
> line. Rust ignores everything in comments, which are discussed in more detail
> in Chapter 3.
-->

> Note: Les `//` permettent de commencer un commentaire s'étendant jusqu'à la fin
> de la ligne. Rust ignore le texte se trouvant dans un commentaire.

<!--
Let’s return to the guessing game program. You now know that `let mut guess`
will introduce a mutable variable named `guess`. On the other side of the equal
sign (`=`) is the value that `guess` is bound to, which is the result of
calling `String::new`, a function that returns a new instance of a `String`.
[`String`][string]<!-- ignore -- > is a string type provided by the standard
library that is a growable, UTF-8 encoded bit of text.
-->

Vous comprenez donc maintenant que la ligne `let mut deduction` permet de créer une
variable modifiable nommée `deduction`. De l'autre côté du signe égal (`=`) se trouve
la valeur de cette variable. Ici il s'agit du retour de l'appel de la fonction
`String::new`, qui renvoie une nouvelle instance d'un `String`.
[`String`][string]<!-- ignore --> est un type qui représente une chaine de caractères,
et qui nous est fournit par la librairie standard. Les `String` représentent du texte
encodé en UTF-8, et il est possible de les étendre.

[string]: ../std/string/struct.String.html

<!--
The `::` syntax in the `::new` line indicates that `new` is an *associated
function* of the `String` type. An associated function is implemented on a type,
in this case `String`, rather than on a particular instance of a `String`. Some
languages call this a *static method*.
-->

Les `::` de qui précèdent `::new` indiquent que la fonction `new` est une *fonction
associée* du type `String`. Une fonction associée est implémentée sur un type, dans
ce cas `String` plutot que sur une instance particulière de `String`. Ce concept
est parfois appelé une *méthode statique*.

<!--
This `new` function creates a new, empty string. You’ll find a `new` function
on many types, because it’s a common name for a function that makes a new value
of some kind.
-->

Cette fonction `new` crée une nouvelle chaine de caractères vide, un nouveau `String`.
Vous trouverez fréquemment une fonction `new` sur les types, c'est un nom souvent
donné à une fonction qui crée une nouvelle valeur ou instance.

<!--
To summarize, the `let mut guess = String::new();` line has created a mutable
variable that is currently bound to a new, empty instance of a `String`. Whew!
-->

Pour résumer, la ligne `let mut deduction = String::new();` crée une nouvelle
variable modifiable nommée `deduction` qui contient une nouvelle chaine de caractères,
un `String`. Ouf !

<!--
Recall that we included the input/output functionality from the standard
library with `use std::io;` on the first line of the program. Now we’ll call
the `stdin` function from the `io` module:
-->

Rappellez-vous que qu'on a importé les fonctionnalités d'entrée/sortie de la
librairie standard, au moyen de la ligne `use std::io;`, dès la première ligne
de notre programme. Nous allons maintenant appeler la fonction `stdin`, associée
au module `io`:

<!--
```rust,ignore
io::stdin().read_line(&mut guess)
    .expect("Failed to read line");
```
-->

```rust,ignore
io::stdin().read_line(&mut deduction)
    .expect("Echec de la lecture de l'entrée utilisateur");
```

<!--
If we hadn’t listed the `use std::io` line at the beginning of the program, we
could have written this function call as `std::io::stdin`. The `stdin` function
returns an instance of [`std::io::Stdin`][iostdin]<!-- ignore -- >, which is a
type that represents a handle to the standard input for your terminal.
-->

Si la ligne `use std::io` n'était pas présente au début du programme, on aurait
dû écrire l'appel à la fonction de cette manière: `std::io::stdin`. La fonction
`stdin` retourne une instance de [`std::io::Stdin`][iostdin]<!-- ignore -->, qui
est un type qui représente une référence vers l'entrée standard de notre programme,
liée au terminal dans lequel il est lancé.

<!-- TODO: Vérifier traduction de 'handle' en 'référence' -->

[iostdin]: ../std/io/struct.Stdin.html

<!--
The next part of the code, `.read_line(&mut guess)`, calls the
[`read_line`][read_line]<!-- ignore -- > method on the standard input handle to
get input from the user. We’re also passing one argument to `read_line`: `&mut
guess`.
-->

La ligne suivante, `.read_line(&mut deduction)` appelle la méthode
[`read_line`][read_line]<!-- ignore --> de notre référence vers l'entrée standard,
ce qui permet d'obtenir ce que l'utilisateur a entré au clavier. On passe à la
méthode `read_line` un argument, `&mut deduction`.

[read_line]: ../std/io/struct.Stdin.html#method.read_line

<!--
The job of `read_line` is to take whatever the user types into standard input
and place that into a string, so it takes that string as an argument. The
string argument needs to be mutable so the method can change the string’s
content by adding the user input.
-->

L'objectif de `read_line` est de prendre ce que l'utilisateur écrit dans
l'entrée standard, et de le placer dans une chaine de caractères, c'est pourquoi
on passe à la méthode cet argument. Ce dernier se doit d'être modifiable, puisque
`read_line` va le modifier afin d'y placer l'entrée de l'utilisateur.

<!--
The `&` indicates that this argument is a *reference*, which gives you a way to
let multiple parts of your code access one piece of data without needing to
copy that data into memory multiple times. References are a complex feature,
and one of Rust’s major advantages is how safe and easy it is to use
references. You don’t need to know a lot of those details to finish this
program. For now, all you need to know is that like variables, references are
immutable by default. Hence, you need to write `&mut guess` rather than
`&guess` to make it mutable. (Chapter 4 will explain references more
thoroughly.)
-->

Le signe `&` indique que cet argument est une *référence*, ce qui permet de laisser
plusieurs morceaux de code accéder à un même bloc de données, sans avoir besoin
de copier ces données plusieurs fois. Les références sont une fonctionnalité
complexe, et un des avantages majeurs de Rust est qu'il rend simple et sécurisé
l'utilisation des références. Il est inutile de détailler plus que nécessaire
les références pour finir ce programme. Le chapitre 4 les présentera en détails.
Pour l'instant, tout ce que vous devez savoir est que comme les variables,
les références sont immuables par défaut, il est donc nécessaire d'écrire
`&mut deduction` plutot que `&deduction` pour rendre la référence modifiable.

<!--
### Handling Potential Failure with the `Result` Type
-->

### Gérer les potentielles erreurs avec `Result`

<!--
We’re not quite done with this line of code. Although what we’ve discussed so
far is a single line of text, it’s only the first part of the single logical
line of code. The second part is this method:
-->

Nous n'avons pas tout à fait finit de détailler ce bout de code. C'est une ligne
de texte, mais ce n'est que la première partie de la ligne de code complète.
Voici la suite:

<!--
```rust,ignore
.expect("Failed to read line");
```
-->

```rust,ignore
.expect("Echec de la lecture de l'entrée utilisateur");
```

<!--
When you call a method with the `.foo()` syntax, it’s often wise to introduce a
newline and other whitespace to help break up long lines. We could have
written this code as:
-->

Lorsque l'on appelle une méthode avec la syntaxe `.foo()`, il est généralement
préférable de passer une ligne puis d'indenter à l'aide de caractères espace
avant d'écrire la suite du code. Ceci permet d'aider à séparer les longues lignes
de code. On aurait pu écrire le même code de cette manière:

<!--
```rust,ignore
io::stdin().read_line(&mut guess).expect("Failed to read line");
```
-->

```rust,ignore
io::stdin().read_line(&mut deduction).expect("Echec de la lecture de l'entrée utilisateur");
```

<!--
However, one long line is difficult to read, so it’s best to divide it: two
lines for two method calls. Now let’s discuss what this line does.
-->

Cependant, une longue ligne de code n'est pas toujours facile à lire, il est donc
considéré comme une bonne pratique de la diviser, deux lignes de texte pour deux
appels de méthodes. Voyons maintenant l'effet de l'appel à `expect`.

<!--
As mentioned earlier, `read_line` puts what the user types into the string
we’re passing it, but it also returns a value—in this case, an
[`io::Result`][ioresult]<!-- ignore -- >. Rust has a number of types named
`Result` in its standard library: a generic [`Result`][result]<!-- ignore -- >
as well as specific versions for submodules, such as `io::Result`.
-->

Tel qu'expliqué plus haut, `read_line` permet de stocker ce que l'utilisateur a
écrit au clavier dans la variable que l'on spécifie, mais cette fonction retourne
également une valeur, de type [`io::Result`][ioresult]<!-- ignore -->. Il existe
plusieurs types nommés `Result` dans la librairie standard de Rust: un type
générique [`Result`][result]<!-- ignore -->, ainsi que des versions spécifiques
à un sous-module, comme `io::Result`.

[ioresult]: ../std/io/type.Result.html
[result]: ../std/result/enum.Result.html

<!--
The `Result` types are [*enumerations*][enums]<!-- ignore -- >, often referred
to as *enums*. An enumeration is a type that can have a fixed set of values,
and those values are called the enum’s *variants*. Chapter 6 will cover enums
in more detail.
-->

Les types `Result` sont des [*énumerations*][enums]<!-- ignore -->, aussi
appelées *enums*. Une énumération est un type qui peut avoir un certain nombre
de valeurs définies par le programmeur. Les différentes valeurs possibles d'un
*enum* sont appelées *variantes*. Le chapitre 6 explorera les énumération plus
en détails.

[enums]: ch06-00-enums.html

<!--
For `Result`, the variants are `Ok` or `Err`. The `Ok` variant indicates the
operation was successful, and inside `Ok` is the successfully generated value.
The `Err` variant means the operation failed, and `Err` contains information
about how or why the operation failed.
-->

Pour `Result`, il existe deux variantes: `Ok` et `Err`. `Ok` indique qu'une
opération est un succès, et à l'intérieur de la variante `Ok` se trouve le
résultat attendu. A l'inverse, la variante `Err` de `Result` signifie que
l'opération a échouée, et à l'intérieur se trouve les informations décrivant
les raisons de l'échec.

<!--
The purpose of these `Result` types is to encode error-handling information.
Values of the `Result` type, like values of any type, have methods defined on
them. An instance of `io::Result` has an [`expect` method][expect]<!-- ignore
-- > that you can call. If this instance of `io::Result` is an `Err` value,
`expect` will cause the program to crash and display the message that you
passed as an argument to `expect`. If the `read_line` method returns an `Err`,
it would likely be the result of an error coming from the underlying operating
system. If this instance of `io::Result` is an `Ok` value, `expect` will take
the return value that `Ok` is holding and return just that value to you so you
can use it. In this case, that value is the number of bytes in what the user
entered into standard input.
-->

L'objectif du type `Result` est d'encoder les informations nécessaires à une
bonne gestion des erreurs. Les types `Result`, comme tous les types, ont des
méthodes associées. Par exemple, `io::Result` propose
[la méthode `expect`][expect]<!-- ignore -->. Si une instance de `io::Result` est
la variante `Err`, un appel à `expect` sur l'instance de `io::Result` causera
un crash du programme, et affichera le message que vous aurez passé en argument.
Si l'appel à `read_line` retourne une variante `Err`, ce sera probablement
dû à une erreur du système d'exploitation. Si par contre `read_line` retourne
une variante `Ok`, `expect` prendra le contenu du `Ok`, le résultat de l'operation,
et vous le retournera afin que vous puissiez l'utiliser. Dans notre exemple,
ce résultat est le nombre d'octets que l'utilisateur a fournit dans l'entrée
standard.

[expect]: ../std/result/enum.Result.html#method.expect

<!--
If you don’t call `expect`, the program will compile, but you’ll get a warning:
-->

Si l'on appelle pas `expect`, le programme compilera, mais avec une mise en garde:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
warning: unused `std::result::Result` which must be used
  -- > src/main.rs:10:5
   |
10 |     io::stdin().read_line(&mut guess);
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   |
   = note: #[warn(unused_must_use)] on by default
```

<!--
Rust warns that you haven’t used the `Result` value returned from `read_line`,
indicating that the program hasn’t handled a possible error.
-->

Rust nous informe que l'on ne fait rien du `Result` qui nous fournir `read_line`,
et que par conséquent nous n'avons pas géré une erreur potentielle.

<!--
The right way to suppress the warning is to actually write error handling, but
because you just want to crash this program when a problem occurs, you can use
`expect`. You’ll learn about recovering from errors in Chapter 9.
-->

La meilleure façon de régler ce programme est d'écrire le code permettant de
bien gérer l'erreur, mais dans notre cas on souhaite seulement crasher le
programme si un problème survient, on peut donc se contenter d'appeler `expect`.
Nous verrons dans le chapitre 9 comment gérer les erreurs proprement.

<!--
### Printing Values with `println!` Placeholders
-->

### Afficher des valeurs grâce à `println!`

<!--
Aside from the closing curly brackets, there’s only one more line to discuss in
the code added so far, which is the following:
-->

Mis à part l'accolade de fermeture, il ne nous reste qu'une seule ligne à étudier
dans le code que nous avons pour l'instant:

<!--
```rust,ignore
println!("You guessed: {}", guess);
```
-->

```rust,ignore
println!("Votre déduction: {}", guess);
```

<!--
This line prints the string we saved the user’s input in. The set of curly
brackets, `{}`, is a placeholder: think of `{}` as little crab pincers that
hold a value in place. You can print more than one value using curly brackets:
the first set of curly brackets holds the first value listed after the format
string, the second set holds the second value, and so on. Printing multiple
values in one call to `println!` would look like this:
-->

Cette ligne permet d'afficher la variable dans laquelle nous avons stocké ce que
notre utilisateur à tapé au clavier. Les `{}` sont un 'placeholder' qui sera
remplacé par une valeur. Il est possible d'en utiliser plusieurs. Les premières `{}`
seront remplacées par le premier argument qui suit la chaîne de formatage, les
secondes `{}` par le deuxième argument, et ainsi de suite. Afficher plusieurs
valeurs avec un seul appel à `println` ressemble donc à ceci:

<!--
```rust
let x = 5;
let y = 10;

println!("x = {} and y = {}", x, y);
```
-->

```rust
let x = 5;
let y = 10;

println!("x = {} et y = {}", x, y);
```

<!--
This code would print `x = 5 and y = 10`.
-->

Ce code afficherait `x = 5 et y = 10`.

<!--
### Testing the First Part
-->

### Test de la première partie

<!--
Let’s test the first part of the guessing game. Run it using `cargo run`:
-->

Pour tester notre début de programme, lançons-le à l'aide de la commande `cargo run`.

<!--
```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
     Running `target/debug/guessing_game`
Guess the number!
Please input your guess.
6
You guessed: 6
```
-->

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
     Running `target/debug/guessing_game`
Jeu de Plus ou Moins
Entrez votre déduction.
6
Votre déduction: 6
```

<!--
At this point, the first part of the game is done: we’re getting input from the
keyboard and then printing it.
-->

À ce stade, la première partie de notre programme est terminée. Nous sommes
capables de recevoir une entrée de l'utilisateur et de l'afficher à l'écran.

<!--
## Generating a Secret Number
-->

## Générer le nombre secret

<!--
Next, we need to generate a secret number that the user will try to guess. The
secret number should be different every time so the game is fun to play more
than once. Let’s use a random number between 1 and 100 so the game isn’t too
difficult. Rust doesn’t yet include random number functionality in its standard
library. However, the Rust team does provide a [`rand` crate][randcrate].
-->

Maintenant, il nous faut générer un nombre secret que notre joueur va tenter
de deviner. Ce nombre se devra d'être différent à chaque lancement du programme,
afin qu'on puisse y jouer plusieurs fois. Utilisons un nombre aléatoire entre
1 et 100 afin que le jeu de soit pas trop difficile. Rust n'inclue pour l'instant
pas de fonctionnalité de génération de nombre aléatoire dans la librairie standard.
L'équipe de développement propose cependant une [`rand` crate][randcrate] pour cela.

[randcrate]: https://crates.io/crates/rand

<!--
### Using a Crate to Get More Functionality
-->

### Étendre les fonctionnalités de Rust avec une Crate.

<!--
Remember that a crate is a collection of Rust source code files.
The project we’ve been building is a *binary crate*, which is an executable.
The `rand` crate is a *library crate*, which contains code intended to be
used in other programs.
-->

Souvenez-vous, une *crate* est un paquet de code Rust. Le projet sur lequel
nous travaillons est une *binary crate*, c'est-à-dire un programme exécutable.
La crate `rand` est une *library crate*, donc qui contient du code qui peut
être utilisé par d'autres programmes.

<!--
Cargo’s use of external crates is where it really shines. Before we can write
code that uses `rand`, we need to modify the *Cargo.toml* file to include the
`rand` crate as a dependency. Open that file now and add the following line to
the bottom beneath the `[dependencies]` section header that Cargo created for
you:
-->

Le gros point positif de Cargo est la facilité avec laquelle il gère les crates
externes. Afin de pouvoir utiliser la crate `rand`, il nous faut éditer le fichier
*Cargo.toml* pour y spécifier `rand` en tant que dépendance de notre projet.
Ouvrons donc ce fichier, et ajoutons la ligne suivante à la fin, dans la section
`[dependencies]` qui a été automatiquement créée pour vous par Cargo.

<!--
<span class="filename">Filename: Cargo.toml</span>
-->

<span class="filename">Fichier: Cargo.toml</span>

```toml
[dependencies]

rand = "0.3.14"
```

<!--
In the *Cargo.toml* file, everything that follows a header is part of a section
that continues until another section starts. The `[dependencies]` section is
where you tell Cargo which external crates your project depends on and which
versions of those crates you require. In this case, we’ll specify the `rand`
crate with the semantic version specifier `0.3.14`. Cargo understands [Semantic
Versioning][semver]<!-- ignore -- > (sometimes called *SemVer*), which is a
standard for writing version numbers. The number `0.3.14` is actually shorthand
for `^0.3.14`, which means “any version that has a public API compatible with
version 0.3.14.”
-->

Dans le fichier *Cargo.toml*, tout ce qui suit une entête fait partie de cette
section, et ce jusqu'au démarrage d'une autre section. La section `[dependencies]`.
permet d'informer Cargo de quelles crates externes notre projet dépend, et en
quelle version. Dans notre cas, on spécifie que l'on requiert la crate `rand` en
version 0.3.14. Il s'agit d'un numéro de version [Semantic
Versioning][semver]<!-- ignore --> (aussi appelé *SemVer*), qui est un standard
d'écriture de numéros de version. En réalité `0.3.14` est un alias pour `^0.3.14`,
ce qui signifie “n'importe quelle version qui propose une API publique compatible
avec la version 0.3.14”.

[semver]: http://semver.org

<!--
Now, without changing any of the code, let’s build the project, as shown in
Listing 2-2.
-->

Maintenant, sans apporter aucun changement au code, lançons une compilation
du projet, comme dans la figure 2-2:

```text
$ cargo build
    Updating registry `https://github.com/rust-lang/crates.io-index`
 Downloading rand v0.3.14
 Downloading libc v0.2.14
   Compiling libc v0.2.14
   Compiling rand v0.3.14
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
```

<!--
<span class="caption">Listing 2-2: The output from running `cargo build` after
adding the rand crate as a dependency</span>
-->

<span class="caption">Figure 2-2: Lancement de `cargo build` après
avoir ajouté la dépendance à `rand`</span>

<!--
You may see different version numbers (but they will all be compatible with
the code, thanks to SemVer!), and the lines may be in a different order.
-->

Il est possible que vous ne voyiez pas exactement les mêmes numéros de version,
(mais elles seront compatibles avec votre code, merci SemVer !), et les lignes
ne seront pas forcément affichées dans le même ordre.

<!--
Now that we have an external dependency, Cargo fetches the latest versions of
everything from the *registry*, which is a copy of data from
[Crates.io][cratesio]. Crates.io is where people in the Rust ecosystem post
their open source Rust projects for others to use.
-->

Maintenant que nous avons notre dépendance externe, Cargo récupère la dernière
version de tous les objets depuis le *registre*, qui est une copie des données
de [Crates.io][cratesio]. Crates.io est l'endroit où les développeurs de
l'écosystème Rust publient leurs projets open source afin de les rendre disponible
aux autres.

[cratesio]: https://crates.io/

<!--
After updating the registry, Cargo checks the `[dependencies]` section and
downloads any crates you don’t have yet. In this case, although we only listed
`rand` as a dependency, Cargo also grabbed a copy of `libc`, because `rand`
depends on `libc` to work. After downloading the crates, Rust compiles them and
then compiles the project with the dependencies available.
-->

Une fois le registre mis à jour, Cargo lit la section `[dependencies]` et se
charge de télécharger les crates que vous n'avez pas encore chez vous. Dans
notre cas, bien que nous n'ayons spécifié qu'une seule dépendance, `rand`,
Cargo a aussi téléchargé la crate `libc`, parce que `rand` dépend de `libc`
pour fonctionner. Une fois le téléchargement terminé, Cargo compile les
dépendances, puis notre projet qui les utilise.

<!--
If you immediately run `cargo build` again without making any changes, you
won’t get any output aside from the `Finished` line. Cargo knows it has already
downloaded and compiled the dependencies, and you haven’t changed anything
about them in your *Cargo.toml* file. Cargo also knows that you haven’t changed
anything about your code, so it doesn’t recompile that either. With nothing to
do, it simply exits.
-->

Si vous relancez tout de suite `cargo build` sans aucun changement apporté au code,
vous n'obtiendrez aucun message. Cargo sait qu'il a déjà téléchargé et compilé les
dépendances, et puisque le code de notre projet n'a pas changé, il ne le
recompile pas non plus. Étant donné qu'il n'a rien à faire, Cargo termine immédiatement.

<!--
If you open up the *src/main.rs* file, make a trivial change, and then save it
and build again, you’ll only see two lines of output:
-->

En revanche, si vous ouvrez *src/main.rs*, et faites un changement très simple,
enregistrez le fichier, et relancez Cargo, vous verrez une seule ligne en sortie:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
```

<!--
These lines show Cargo only updates the build with your tiny change to the
*src/main.rs* file. Your dependencies haven’t changed, so Cargo knows it can
reuse what it has already downloaded and compiled for those. It just rebuilds
your part of the code.
-->

Cette ligne nous informe que Cargo a compilé le projet, avec notre petit changement
dans le fichier *src/main.rs*. Les dépendances n'ayant pas changé, Cargo sait qu'il
peut simplement utiliser ce qu'il a déjà téléchargé et compilé précédemment. Il se
contente donc de ne recompiler que notre partie du code.

<!--
#### Ensuring Reproducible Builds with the *Cargo.lock* File
-->

#### The *Cargo.lock* File Ensures Reproducible Builds

<!--
Cargo has a mechanism that ensures you can rebuild the same artifact every time
you or anyone else builds your code: Cargo will use only the versions of the
dependencies you specified until you indicate otherwise. For example, what
happens if next week version 0.3.15 of the `rand` crate comes out and
contains an important bug fix but also contains a regression that will break
your code?
-->

Cargo has a mechanism that ensures you can rebuild the same artifact every time
you or anyone else builds your code: Cargo will use only the versions of the
dependencies you specified until you indicate otherwise. For example, what
happens if next week version `v0.3.15` of the `rand` crate comes out and
contains an important bug fix but also contains a regression that will break
your code?

<!--
The answer to this problem is the *Cargo.lock* file, which was created the
first time you ran `cargo build` and is now in your *guessing_game* directory.
When you build a project for the first time, Cargo figures out all the
versions of the dependencies that fit the criteria and then writes them to
the *Cargo.lock* file. When you build your project in the future, Cargo will
see that the *Cargo.lock* file exists and use the versions specified there
rather than doing all the work of figuring out versions again. This lets you
have a reproducible build automatically. In other words, your project will
remain at `0.3.14` until you explicitly upgrade, thanks to the *Cargo.lock*
file.
-->

The answer to this problem is the *Cargo.lock* file, which was created the
first time you ran `cargo build` and is now in your *guessing_game* directory.
When you build a project for the first time, Cargo figures out all the
versions of the dependencies that fit the criteria and then writes them to
the *Cargo.lock* file. When you build your project in the future, Cargo will
see that the *Cargo.lock* file exists and use the versions specified there
rather than doing all the work of figuring out versions again. This lets you
have a reproducible build automatically. In other words, your project will
remain at `0.3.14` until you explicitly upgrade, thanks to the *Cargo.lock*
file.

<!--
#### Updating a Crate to Get a New Version
-->

<!--
When you *do* want to update a crate, Cargo provides another command, `update`,
which will ignore the *Cargo.lock* file and figure out all the latest versions
that fit your specifications in *Cargo.toml*. If that works, Cargo will write
those versions to the *Cargo.lock* file.
-->

<!--
But by default, Cargo will only look for versions greater than `0.3.0` and less
than `0.4.0`. If the `rand` crate has released two new versions, `0.3.15` and
`0.4.0`, you would see the following if you ran `cargo update`:
-->

```text
$ cargo update
    Updating registry `https://github.com/rust-lang/crates.io-index`
    Updating rand v0.3.14 -> v0.3.15
```

<!--
At this point, you would also notice a change in your *Cargo.lock* file noting
that the version of the `rand` crate you are now using is `0.3.15`.
-->

<!--
If you wanted to use `rand` version `0.4.0` or any version in the `0.4.x`
series, you’d have to update the *Cargo.toml* file to look like this instead:
-->

```toml
[dependencies]

rand = "0.4.0"
```

<!--
The next time you run `cargo build`, Cargo will update the registry of crates
available and reevaluate your `rand` requirements according to the new version
you have specified.
-->

<!--
There’s a lot more to say about [Cargo][doccargo]<!-- ignore -- > and [its
ecosystem][doccratesio]<!-- ignore -- > which we’ll discuss in Chapter 14, but
for now, that’s all you need to know. Cargo makes it very easy to reuse
libraries, so Rustaceans are able to write smaller projects that are assembled
from a number of packages.
-->

[doccargo]: http://doc.crates.io
[doccratesio]: http://doc.crates.io/crates-io.html

<!--
### Generating a Random Number
-->

<!--
Now that you’ve added the `rand` crate to *Cargo.toml*, let’s start using
`rand`. The next step is to update *src/main.rs*, as shown in Listing 2-3.
-->

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<!--
```rust,ignore
use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```
-->

<!--
<span class="caption">Listing 2-3: Adding code to generate a random
number</span>
-->

<!--
First, we add a `use` line: `use rand::Rng`. The `Rng` trait defines
methods that random number generators implement, and this trait must be in
scope for us to use those methods. Chapter 10 will cover traits in detail.
-->

<!--
Next, we’re adding two lines in the middle. The `rand::thread_rng` function
will give us the particular random number generator that we’re going to use:
one that is local to the current thread of execution and seeded by the
operating system. Then we call the `gen_range` method on the random number
generator. This method is defined by the `Rng` trait that we brought into
scope with the `use rand::Rng` statement. The `gen_range` method takes two
numbers as arguments and generates a random number between them. It’s inclusive
on the lower bound but exclusive on the upper bound, so we need to specify `1`
and `101` to request a number between 1 and 100.
-->

<!--
> Note: You won’t just know which traits to use and which methods and functions
> to call from a crate. Instructions for using a crate are in each crate’s
> documentation. Another neat feature of Cargo is that you can run the `cargo
> doc --open` command, which will build documentation provided by all of your
> dependencies locally and open it in your browser. If you’re interested in
> other functionality in the `rand` crate, for example, run `cargo doc --open`
> and click `rand` in the sidebar on the left.
-->

<!--
The second line that we added to the middle of the code prints the secret
number. This is useful while we’re developing the program to be able to test
it, but we’ll delete it from the final version. It’s not much of a game if the
program prints the answer as soon as it starts!
-->

<!--
Try running the program a few times:
-->

<!--
```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 7
Please input your guess.
4
You guessed: 4
$ cargo run
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 83
Please input your guess.
5
You guessed: 5
```
-->

<!--
You should get different random numbers, and they should all be numbers between
1 and 100. Great job!
-->

<!--
## Comparing the Guess to the Secret Number
-->

<!--
Now that we have user input and a random number, we can compare them. That step
is shown in Listing 2-4. Note that this code won’t compile quite yet, as we
will explain.
-->

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<!--
```rust,ignore,does_not_compile
use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {

    // ---snip---

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```
-->

<!--
<span class="caption">Listing 2-4: Handling the possible return values of
comparing two numbers</span>
-->

<!--
The first new bit here is another `use` statement, bringing a type called
`std::cmp::Ordering` into scope from the standard library. Like `Result`,
`Ordering` is another enum, but the variants for `Ordering` are `Less`,
`Greater`, and `Equal`. These are the three outcomes that are possible when you
compare two values.
-->

<!--
Then we add five new lines at the bottom that use the `Ordering` type. The
`cmp` method compares two values and can be called on anything that can be
compared. It takes a reference to whatever you want to compare with: here it’s
comparing the `guess` to the `secret_number`. Then it returns a variant of the
`Ordering` enum we brought into scope with the `use` statement. We use a
[`match`][match]<!-- ignore -- > expression to decide what to do next based on
which variant of `Ordering` was returned from the call to `cmp` with the values
in `guess` and `secret_number`.
-->

[match]: ch06-02-match.html

<!--
A `match` expression is made up of *arms*. An arm consists of a *pattern* and
the code that should be run if the value given to the beginning of the `match`
expression fits that arm’s pattern. Rust takes the value given to `match` and
looks through each arm’s pattern in turn. The `match` construct and patterns
are powerful features in Rust that let you express a variety of situations your
code might encounter and make sure that you handle them all. These features
will be covered in detail in Chapter 6 and Chapter 18, respectively.
-->

<!--
Let’s walk through an example of what would happen with the `match` expression
used here. Say that the user has guessed 50 and the randomly generated secret
number this time is 38. When the code compares 50 to 38, the `cmp` method will
return `Ordering::Greater`, because 50 is greater than 38. The `match`
expression gets the `Ordering::Greater` value and starts checking each arm’s
pattern. It looks at the first arm’s pattern, `Ordering::Less`, and sees that
the value `Ordering::Greater` does not match `Ordering::Less`, so it ignores
the code in that arm and moves to the next arm. The next arm’s pattern,
`Ordering::Greater`, *does* match `Ordering::Greater`! The associated code in
that arm will execute and print `Too big!` to the screen. The `match`
expression ends because it has no need to look at the last arm in this scenario.
-->

<!--
However, the code in Listing 2-4 won’t compile yet. Let’s try it:
-->

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
error[E0308]: mismatched types
  -- > src/main.rs:23:21
   |
23 |     match guess.cmp(&secret_number) {
   |                     ^^^^^^^^^^^^^^ expected struct `std::string::String`, found integral variable
   |
   = note: expected type `&std::string::String`
   = note:    found type `&{integer}`

error: aborting due to previous error
Could not compile `guessing_game`.
```

<!--
The core of the error states that there are *mismatched types*. Rust has a
strong, static type system. However, it also has type inference. When we wrote
`let mut guess = String::new()`, Rust was able to infer that `guess` should be
a `String` and didn’t make us write the type. The `secret_number`, on the other
hand, is a number type. A few number types can have a value between 1 and 100:
`i32`, a 32-bit number; `u32`, an unsigned 32-bit number; `i64`, a 64-bit
number; as well as others. Rust defaults to an `i32`, which is the type of
`secret_number` unless you add type information elsewhere that would cause Rust
to infer a different numerical type. The reason for the error is that Rust
cannot compare a string and a number type.
-->

<!--
Ultimately, we want to convert the `String` the program reads as input into a
real number type so we can compare it numerically to the secret number. We can
do that by adding the following two lines to the `main` function body:
-->

<!--
<span class="filename">Filename: src/main.rs</span>
-->

```rust,ignore
// --snip--

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    let guess: u32 = guess.trim().parse()
        .expect("Please type a number!");

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

<!--
The two new lines are:
-->

<!--
```rust,ignore
let guess: u32 = guess.trim().parse()
    .expect("Please type a number!");
```
-->

<!--
We create a variable named `guess`. But wait, doesn’t the program already have
a variable named `guess`? It does, but Rust allows us to *shadow* the previous
value of `guess` with a new one. This feature is often used in situations in
which you want to convert a value from one type to another type. Shadowing lets
us reuse the `guess` variable name rather than forcing us to create two unique
variables, such as `guess_str` and `guess` for example. (Chapter 3 covers
shadowing in more detail.)
-->

<!--
We bind `guess` to the expression `guess.trim().parse()`. The `guess` in the
expression refers to the original `guess` that was a `String` with the input in
it. The `trim` method on a `String` instance will eliminate any whitespace at
the beginning and end. Although `u32` can contain only numerical characters,
the user must press <span class="keystroke">enter</span> to satisfy
`read_line`. When the user presses <span class="keystroke">enter</span>, a
newline character is added to the string. For example, if the user types <span
class="keystroke">5</span> and presses <span class="keystroke">enter</span>,
`guess` looks like this: `5\n`. The `\n` represents “newline,” the result of
pressing <span class="keystroke">enter</span>. The `trim` method eliminates
`\n`, resulting in just `5`.
-->

<!--
The [`parse` method on strings][parse]<!-- ignore -- > parses a string into some
kind of number. Because this method can parse a variety of number types, we
need to tell Rust the exact number type we want by using `let guess: u32`. The
colon (`:`) after `guess` tells Rust we’ll annotate the variable’s type. Rust
has a few built-in number types; the `u32` seen here is an unsigned, 32-bit
integer. It’s a good default choice for a small positive number. You’ll learn
about other number types in Chapter 3. Additionally, the `u32` annotation in
this example program and the comparison with `secret_number` means that Rust
will infer that `secret_number` should be a `u32` as well. So now the
comparison will be between two values of the same type!
-->

[parse]: ../std/primitive.str.html#method.parse

<!--
The call to `parse` could easily cause an error. If, for example, the string
contained `A👍%`, there would be no way to convert that to a number. Because it
might fail, the `parse` method returns a `Result` type, much as the `read_line`
method does (discussed earlier in [“Handling Potential Failure with the
`Result` Type”](#handling-potential-failure-with-the-result-type)<!-- ignore
-- >). We’ll treat this `Result` the same way by using the `expect` method
again. If `parse` returns an `Err` `Result` variant because it couldn’t create
a number from the string, the `expect` call will crash the game and print the
message we give it. If `parse` can successfully convert the string to a number,
it will return the `Ok` variant of `Result`, and `expect` will return the
number that we want from the `Ok` value.
-->

<!--
Let’s run the program now!
-->

<!--
```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43 secs
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 58
Please input your guess.
  76
You guessed: 76
Too big!
```
-->

<!--
Nice! Even though spaces were added before the guess, the program still figured
out that the user guessed 76. Run the program a few times to verify the
different behavior with different kinds of input: guess the number correctly,
guess a number that is too high, and guess a number that is too low.
-->

<!--
We have most of the game working now, but the user can make only one guess.
Let’s change that by adding a loop!
-->

<!--
## Allowing Multiple Guesses with Looping
-->

<!--
The `loop` keyword creates an infinite loop. We’ll add that now to give users
more chances at guessing the number:
-->

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<!--
```rust,ignore
// --snip--

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        // --snip--

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => println!("You win!"),
        }
    }
}
```
-->

<!--
As you can see, we’ve moved everything into a loop from the guess input prompt
onward. Be sure to indent the lines inside the loop another four spaces each
and run the program again. Notice that there is a new problem because the
program is doing exactly what we told it to do: ask for another guess forever!
It doesn’t seem like the user can quit!
-->

<!--
The user could always interrupt the program by using the keyboard shortcut <span
class="keystroke">ctrl-c</span>. But there’s another way to escape this
insatiable monster, as mentioned in the `parse` discussion in [“Comparing the
Guess to the Secret Number”](#comparing-the-guess-to-the-secret-number)<!--
ignore -- >: if the user enters a non-number answer, the program will crash. The
user can take advantage of that in order to quit, as shown here:
-->

<!--
```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 1.50 secs
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 59
Please input your guess.
45
You guessed: 45
Too small!
Please input your guess.
60
You guessed: 60
Too big!
Please input your guess.
59
You guessed: 59
You win!
Please input your guess.
quit
thread 'main' panicked at 'Please type a number!: ParseIntError { kind: InvalidDigit }', src/libcore/result.rs:785
note: Run with `RUST_BACKTRACE=1` for a backtrace.
error: Process didn't exit successfully: `target/debug/guess` (exit code: 101)
```
-->

<!--
Typing `quit` actually quits the game, but so will any other non-number input.
However, this is suboptimal to say the least. We want the game to automatically
stop when the correct number is guessed.
-->

<!--
### Quitting After a Correct Guess
-->

<!--
Let’s program the game to quit when the user wins by adding a `break` statement:
-->

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<!--
```rust,ignore
// --snip--

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```
-->

<!--
Adding the `break` line after `You win!` makes the program exit the loop when
the user guesses the secret number correctly. Exiting the loop also means
exiting the program, because the loop is the last part of `main`.
-->

<!--
### Handling Invalid Input
-->

<!--
To further refine the game’s behavior, rather than crashing the program when
the user inputs a non-number, let’s make the game ignore a non-number so the
user can continue guessing. We can do that by altering the line where `guess`
is converted from a `String` to a `u32`, as shown in Listing 2-5.
-->

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<!--
```rust,ignore
// --snip--

io::stdin().read_line(&mut guess)
    .expect("Failed to read line");

let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};

println!("You guessed: {}", guess);

// --snip--
```
-->

<!--
<span class="caption">Listing 2-5: Ignoring a non-number guess and asking for
another guess instead of crashing the program</span>
-->

<!--
Switching from an `expect` call to a `match` expression is how you generally
move from crashing on an error to handling the error. Remember that `parse`
returns a `Result` type and `Result` is an enum that has the variants `Ok` or
`Err`. We’re using a `match` expression here, as we did with the `Ordering`
result of the `cmp` method.
-->

<!--
If `parse` is able to successfully turn the string into a number, it will
return an `Ok` value that contains the resulting number. That `Ok` value will
match the first arm’s pattern, and the `match` expression will just return the
`num` value that `parse` produced and put inside the `Ok` value. That number
will end up right where we want it in the new `guess` variable we’re creating.
-->

<!--
If `parse` is *not* able to turn the string into a number, it will return an
`Err` value that contains more information about the error. The `Err` value
does not match the `Ok(num)` pattern in the first `match` arm, but it does
match the `Err(_)` pattern in the second arm. The underscore, `_`, is a
catchall value; in this example, we’re saying we want to match all `Err`
values, no matter what information they have inside them. So the program will
execute the second arm’s code, `continue`, which tells the program to go to the
next iteration of the `loop` and ask for another guess. So, effectively, the
program ignores all errors that `parse` might encounter!
-->

<!--
Now everything in the program should work as expected. Let’s try it:
-->

<!--
```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 61
Please input your guess.
10
You guessed: 10
Too small!
Please input your guess.
99
You guessed: 99
Too big!
Please input your guess.
foo
Please input your guess.
61
You guessed: 61
You win!
```
-->

<!--
Awesome! With one tiny final tweak, we will finish the guessing game. Recall
that the program is still printing the secret number. That worked well for
testing, but it ruins the game. Let’s delete the `println!` that outputs the
secret number. Listing 2-6 shows the final code.
-->

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<!--
```rust,ignore
use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```
-->

<!--
<span class="caption">Listing 2-6: Complete guessing game code</span>
-->

<!--
## Summary
-->

<!--
At this point, you’ve successfully built the guessing game. Congratulations!
-->

<!--
This project was a hands-on way to introduce you to many new Rust concepts:
`let`, `match`, methods, associated functions, the use of external crates, and
more. In the next few chapters, you’ll learn about these concepts in more
detail. Chapter 3 covers concepts that most programming languages have, such as
variables, data types, and functions, and shows how to use them in Rust.
Chapter 4 explores ownership, a feature that makes Rust different from other
languages. Chapter 5 discusses structs and method syntax, and Chapter 6
explains how enums work.
-->

[variables-and-mutability]:
ch03-01-variables-and-mutability.html#variables-and-mutability
