<!--
## Variables and Mutability
-->

## Variables et Mutabilité

<!--
As mentioned in Chapter 2, by default variables are immutable. This is one of
many nudges Rust gives you to write your code in a way that takes advantage of
the safety and easy concurrency that Rust offers. However, you still have the
option to make your variables mutable. Let’s explore how and why Rust
encourages you to favor immutability and why sometimes you might want to opt
out.
-->

Comme mentionné dans le Chapitre 2, par défaut, les variables sont *immuables*.
C'est un des nombreux coups de pouces de Rust vous encourageant à écrire votre
code d'une façon à tirer avantage de la sûreté et de la concurrence facilitée
que Rust propose. Cependant, vous avez tout de même la possibilité de rendre
vos variables mutables. Explorons comment et pourquoi Rust vous encourage à
favoriser l'immutabilité, et pourquoi vous pourriez choisir d'y renoncer.

<!--
When a variable is immutable, once a value is bound to a name, you can’t change
that value. To illustrate this, let’s generate a new project called *variables*
in your *projects* directory by using `cargo new variables`.
-->

Lorsque qu'une variable est immuable, cela signifie qu'une fois qu'une valeur
est liée à un nom, vous ne pouvez pas changer cette valeur. À titre
d'illustration, générons un nouveau projet appelé *variables* dans votre
dossier *projects* en utilisant `cargo new --bin variables`.

<!--
Then, in your new *variables* directory, open *src/main.rs* and replace its
code with the following code that won’t compile just yet:
-->

Ensuite, dans votre nouveau dossier *variables*, ouvrez *src/main.rs* et remplacez son contenu par ceci :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust,ignore,does_not_compile
fn main() {
    let x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```
-->

```rust,ignore,does_not_compile
fn main() {
    let x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

<!--
Save and run the program using `cargo run`. You should receive an error
message, as shown in this output:
-->

Sauvegardez et lancez le programme en utilisant `cargo run`. Vous devriez obtenir un message d'erreur, tel qu'indiqué par la sortie suivante :

<!--
```text
error[E0384]: cannot assign twice to immutable variable `x`
 -- > src/main.rs:4:5
  |
2 |     let x = 5;
  |         - first assignment to `x`
3 |     println!("The value of x is: {}", x);
4 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable
```
-->

```text
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         - first assignment to `x`
3 |     println!("The value of x is: {}", x);
4 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable
```

<!--
This example shows how the compiler helps you find errors in your programs.
Even though compiler errors can be frustrating, they only mean your program
isn’t safely doing what you want it to do yet; they do *not* mean that you’re
not a good programmer! Experienced Rustaceans still get compiler errors.
-->

Cet exemple montre comment le compilateur vous aide à identifier les erreurs
dans vos programmes. Même si les erreurs de compilation peuvent s'avérer
frustrantes, elles signifient uniquement que votre programme n'est pour le
moment pas en train de faire ce que vous voulez qu'il fasse en toute sécurité ;
elles ne signifient *pas* que vous êtes un mauvais en programmation ! Même les
Rustacéens et Rustacéennes ayant de l'expérience continuent d'avoir des erreurs de compilation.

<!--
The error message indicates that the cause of the error is that you `cannot
assign twice to immutable variable x`, because you tried to assign a second
value to the immutable `x` variable.
-->

Cette erreur indique que la cause du problème est qu'il est `impossible 
d'assigner à deux reprises la variable immuable x`, car nous avons essayé de 
donner à `x`, qui est une variable immuable, une seconde valeur.

<!--
It’s important that we get compile-time errors when we attempt to change a
value that we previously designated as immutable because this very situation
can lead to bugs. If one part of our code operates on the assumption that a
value will never change and another part of our code changes that value, it’s
possible that the first part of the code won’t do what it was designed to do.
The cause of this kind of bug can be difficult to track down after the fact,
especially when the second piece of code changes the value only *sometimes*.
-->

Il est important que nous obtenions des erreurs lors de la compilation quand
nous essayons de changer une valeur qui a précédemment été désignée comme
immuable, car cette situation précise peut donner lieu à des bugs. Si une
partie de notre code opère sur le postulat qu'une valeur ne changera jamais et
qu'une autre partie de notre code modifie cette valeur, il est possible que la
première partie du code ne fera pas ce pour quoi elle a été conçue. Cette
source d'erreur peut être difficile à identifier après coup, particulièrement
lorsque la seconde partie du code ne modifie que *quelquefois* cette valeur.

<!--
In Rust, the compiler guarantees that when you state that a value won’t change,
it really won’t change. That means that when you’re reading and writing code,
you don’t have to keep track of how and where a value might change. Your code
is thus easier to reason through.
-->

En Rust, le compilateur garantie que lorsque nous déclarons qu'une variable ne
changera pas, elle ne changera vraiment pas. Cela signifie que lorsque que vous
lisez et écrivez du code, vous n'avez pas à vous souvenir de comment et où une
valeur pourrait changer, ce qui peut rendre le code plus facile à comprendre.

<!--
But mutability can be very useful. Variables are immutable only by default; as
you did in Chapter 2, you can make them mutable by adding `mut` in front of the
variable name. In addition to allowing this value to change, `mut` conveys
intent to future readers of the code by indicating that other parts of the code
will be changing this variable value.
-->

Mais la mutabilité peut s'avérer très utile. Les variables ne sont seulement
qu'immuables par défaut ; nous pouvons les rendre mutables en ajoutant `mut`
devant notre nom de variable. En plus d'autoriser cette valeur à changer, cela
communique l'intention aux futurs lecteurs de ce code que d'autres parties du
code vont modifier cette valeur variable.

<!--
For example, let’s change *src/main.rs* to the following:
-->

Par exemple, modifions *src/main.rs* par :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```
-->

```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

<!--
When we run the program now, we get this:
-->

Lorsque nous exécutons le programme, nous obtenons :

<!--
```text
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/variables`
The value of x is: 5
The value of x is: 6
```
-->

```text
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/variables`
The value of x is: 5
The value of x is: 6
```

<!--
We’re allowed to change the value that `x` binds to from `5` to `6` when `mut`
is used. In some cases, you’ll want to make a variable mutable because it makes
the code more convenient to write than if it had only immutable variables.
-->

En utilisant `mut`, nous sommes autorisés à changer la valeur à laquelle `x`
est reliée de `5` à `6`. Dans certains cas, vous allez vouloir rendre une
variable mutable car cela rend le code plus pratique à écrire qu'une
implémentation n'utilisant que des variables immuables.

<!--
There are multiple trade-offs to consider in addition to the prevention of
bugs. For example, in cases where you’re using large data structures, mutating
an instance in place may be faster than copying and returning newly allocated
instances. With smaller data structures, creating new instances and writing in
a more functional programming style may be easier to think through, so lower
performance might be a worthwhile penalty for gaining that clarity.
-->

Il y a plusieurs compromis à prendre en considération, outre la prévention des
bugs. Par exemple, dans le cas où vous utiliseriez de larges structures de
données, muter une instance déjà existante peut être plus rapide que copier et
retourner une instance nouvellement allouée. Sur des structures de données plus
petites, créer de nouvelles instances et écrire dans un style de programmation
plus fonctionnel peut rendre votre code plus facile à comprendre, ainsi, peut
être qu'un coût plus élevé en performance est un moindre mal face au gain de
clareté apporté.

<!--
### Differences Between Variables and Constants
-->

### Différences Entre Variable et Constante

<!--
Being unable to change the value of a variable might have reminded you of
another programming concept that most other languages have: *constants*. Like
immutable variables, constants are values that are bound to a name and are not
allowed to change, but there are a few differences between constants and
variables.
-->

Être incapable de changer la valeur d'une variable peut vous avoir rappelé un autre concept de programmation que de nombreux autres langages possèdent : les *constantes*. Comme les variables immuables, les constantes sont également des valeurs qui sont liées à un nom et qui ne peuvent être modifiées, mais il y a quelques différences entre les constantes et les variables.

<!--
First, you aren’t allowed to use `mut` with constants. Constants aren’t just
immutable by default—they’re always immutable.
-->

D'abord, nous ne sommes pas autorisés à utiliser `mut` avec les constantes : les constantes ne sont pas seulement immuables par défaut, elles le sont toujours.

<!--
You declare constants using the `const` keyword instead of the `let` keyword,
and the type of the value *must* be annotated. We’re about to cover types and
type annotations in the next section, [“Data Types,”][data-types]<!-- ignore
-- > so don’t worry about the details right now. Just know that you must always
annotate the type.
-->

Nous déclarons les constantes en utilisant le mot-clé `const` à la place du
mot-clé `let`, et le type de la valeur *doit* être annoté. Nous sommes sur le
point de traiter des types et des annotations de types dans la prochaine
section, “Types de données,” donc ne vous inquiétez pas des détails pour le
moment, rappelez-vous juste que vous devez toujours annoter leur type.

<!--
Constants can be declared in any scope, including the global scope, which makes
them useful for values that many parts of code need to know about.
-->

Les constantes peuvent être déclarées dans n'importe quelle portée, y compris
la portée globale, ce qui les rend très utiles pour des valeurs que de
nombreuses parties de votre code ont besoin de connaître.

<!--
The last difference is that constants may be set only to a constant expression,
not the result of a function call or any other value that could only be
computed at runtime.
-->

La dernière différence est que les constantes ne peuvent être définies que par
une expression constante, et non pas le résultat d'un appel de fonction ou
n'importe quelle autre valeur qui ne pourrait être calculée qu'à l'exécution.

<!--
Here’s an example of a constant declaration where the constant’s name is
`MAX_POINTS` and its value is set to 100,000. (Rust’s naming convention for
constants is to use all uppercase with underscores between words, and
underscores can be inserted in numeric literals to improve readability):
-->

Voici un exemple d'une déclaration de constante où le nom de la constante est 
MAX_POINTS` et où sa valeur est définie à 100 000 (en Rust, la convention de 
nommage des constantes est d'utiliser des majuscules pour chaque lettre et des
tirets bas entre chaque mot) :

```rust
const MAX_POINTS: u32 = 100_000;
```

<!--
Constants are valid for the entire time a program runs, within the scope they
were declared in, making them a useful choice for values in your application
domain that multiple parts of the program might need to know about, such as the
maximum number of points any player of a game is allowed to earn or the speed
of light.
-->

Les constantes sont valables pendant toute la durée d'exécution du programme à
l'intérieur de la portée dans laquelle elles sont déclarées, ce qui en font de
très bon choix lorsque plusieurs parties d'un programme doivent connaître
certaines valeurs, comme par exemple le nombre maximum de points qu'un joueur
est autorisé à gagner ou la vitesse de la lumière.

<!--
Naming hardcoded values used throughout your program as constants is useful in
conveying the meaning of that value to future maintainers of the code. It also
helps to have only one place in your code you would need to change if the
hardcoded value needed to be updated in the future.
-->

Déclarer des valeurs codées en dur et utilisées tout le long de votre programme
comme constantes est utile car cela transmet la signification de ces valeurs
aux futurs mainteneurs de votre code. Cela permet également de n'avoir qu'un
seul endroit de votre code à modifier si une valeur codée en dur doit être mise
à jour dans le futur.

<!--
### Shadowing
-->

### *Shadowing*

<!--
As you saw in the guessing game tutorial in the [“Comparing the Guess to the
Secret Number”][comparing-the-guess-to-the-secret-number]<!-- ignore -- >
section in Chapter 2, you can declare a new variable with the same name as a
previous variable, and the new variable shadows the previous variable.
Rustaceans say that the first variable is *shadowed* by the second, which means
that the second variable’s value is what appears when the variable is used. We
can shadow a variable by using the same variable’s name and repeating the use
of the `let` keyword as follows:
-->

Comme nous l'avons vu dans le jeu du Chapitre 2, nous pouvons déclarer de
nouvelles variables avec le même nom qu'une variable précédente, et que la
nouvelle variable *shadow*, occulte la première. Les Rustacéens disent que la
première variable est *shadowed*, occultée par la seconde, ce qui signifie que
la valeur de la seconde variable sera ce que nous obtiendrons lorsque nous
utiliserons cette variable. Nous pouvons occulter une variable en utilisant le
même nom de variable et en réutilisant le mot-clé `let` comme suit :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
fn main() {
    let x = 5;

    let x = x + 1;

    let x = x * 2;

    println!("The value of x is: {}", x);
}
```
-->

```rust
fn main() {
    let x = 5;

    let x = x + 1;

    let x = x * 2;

    println!("The value of x is: {}", x);
}
```

<!--
This program first binds `x` to a value of `5`. Then it shadows `x` by
repeating `let x =`, taking the original value and adding `1` so the value of
`x` is then `6`. The third `let` statement also shadows `x`, multiplying the
previous value by `2` to give `x` a final value of `12`. When we run this
program, it will output the following:
-->

En premier lieu, ce programme lie `x` à la valeur `5`. Puis il *shadow* `x` en
répétant `let x =`, ce qui récupère la valeur originelle et lui ajoute `1` : la
valeur de `x` est désormais `6`. La troisième instruction `let` *shadow*
également `x`, prenant la précédente valeur et la multipliant par `2` pour
donner à `x` une valeur finale de `12`. Lorsque nous exécutons ce programme,
nous obtenons en sortie ceci :

<!--
```text
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/variables`
The value of x is: 12
```
-->

```text
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/variables`
The value of x is: 12
```

<!--
Shadowing is different from marking a variable as `mut`, because we’ll get a
compile-time error if we accidentally try to reassign to this variable without
using the `let` keyword. By using `let`, we can perform a few transformations
on a value but have the variable be immutable after those transformations have
been completed.
-->

Ceci est différent que marquer une variable comme `mut`, car à moins d'utiliser
le mot-clé `let` une nouvelle fois, nous obtenons une erreur de compilation si
nous essayons accidentellement de réassigner cette variable. Nous pouvons
effectuer quelques transformations sur une valeur puis faire en sorte que la
variable soit immuable après que ces transformations soient terminées.

<!--
The other difference between `mut` and shadowing is that because we’re
effectively creating a new variable when we use the `let` keyword again, we can
change the type of the value but reuse the same name. For example, say our
program asks a user to show how many spaces they want between some text by
inputting space characters, but we really want to store that input as a number:
-->

L'autre différence entre le `mut` et le *shadowing* est la création effective
d'une nouvelle variable lorsque nous utilisons le mot-clé `let` une nouvelle
fois, ce qui nous permet de changer le type de la valeur, mais en réutilisant
le même nom. Par exemple, disons que notre programme demande à un utilisateur
le nombre d'espaces à afficher entre du texte en saisissant des caractères
d'espace, mais que nous voulions quand même enregistrer cette saisie comme un
nombre :

<!--
```rust
let spaces = "   ";
let spaces = spaces.len();
```
-->

```rust
let spaces = "   ";
let spaces = spaces.len();
```

<!--
This construct is allowed because the first `spaces` variable is a string type
and the second `spaces` variable, which is a brand-new variable that happens to
have the same name as the first one, is a number type. Shadowing thus spares us
from having to come up with different names, such as `spaces_str` and
`spaces_num`; instead, we can reuse the simpler `spaces` name. However, if we
try to use `mut` for this, as shown here, we’ll get a compile-time error:
-->

Cette conception est autorisée car la première variable `spaces` est du type
*string*, alors que la seconde variable `spaces`, qui est une toute nouvelle
variable se trouvant avoir le même nom que la première, est du type nombre.
L'utilisation du *shadowing* nous évite ainsi d'avoir à trouver des noms
différents, comme `spaces_str` et `spaces_num` ; nous pouvons plutôt utiliser
le simple nom de `spaces`. En revanche, si nous essayons d'utiliser `mut` pour
cela, comme montré ci-dessous, nous obtenons une erreur de compilation :

<!--
```rust,ignore,does_not_compile
let mut spaces = "   ";
spaces = spaces.len();
```
-->

```rust,ignore
let mut spaces = "   ";
spaces = spaces.len();
```

<!--
The error says we’re not allowed to mutate a variable’s type:
-->

Cette erreur indique que nous ne pouvons pas muer le type d'une variable :

```text
error[E0308]: mismatched types
 -- > src/main.rs:3:14
  |
3 |     spaces = spaces.len();
  |              ^^^^^^^^^^^^ expected &str, found usize
  |
  = note: expected type `&str`
             found type `usize`
```

<!--
Now that we’ve explored how variables work, let’s look at more data types they
can have.
-->

Maintenant que nous avons exploré comment fonctionnent les variables, étudions
désormais de nouveaux types de données que ces variables peuvent contenir.

[comparing-the-guess-to-the-secret-number]:
ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number
[data-types]: ch03-02-data-types.html#data-types
