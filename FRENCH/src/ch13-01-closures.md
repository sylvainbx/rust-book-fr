<!-- TODO : bonpatron.fr -->

<!--
## Closures: Anonymous Functions that Can Capture Their Environment
-->

## Les fermetures : des fonctions anonymes qui peuvent utiliser leur environnement

<!--
Rust’s closures are anonymous functions you can save in a variable or pass as
arguments to other functions. You can create the closure in one place and then
call the closure to evaluate it in a different context. Unlike functions,
closures can capture values from the scope in which they’re defined. We’ll
demonstrate how these closure features allow for code reuse and behavior
customization.
-->

Les *closures* en Rust sont des fonctions anonymes qui peuvent être sauvegardés
dans une variable ou qui peuvent être passées en argument à d'autres fonctions.
Il est possible de créer une *closure* à un endroit du code et ensuite de
l'appeler dans un contexte différent pour l'évaluer. Contrairement aux
fonctions, les *closures* ont la possibilité de capturer les valeurs présentes
dans le contexte où elles sont appelées. Nous allons montrer comment les
caractéristiques des *closures* permet de faire de la réutilisation de code et
des comportements personnalisés.

<!--
### Creating an Abstraction of Behavior with Closures
-->

### Création d'une Abstraction de Comportement à l'aide d'une *Closure*

<!--
Let’s work on an example of a situation in which it’s useful to store a closure
to be executed later. Along the way, we’ll talk about the syntax of closures,
type inference, and traits.
-->

Travaillons sur un exemple d'une situation où il est utile de stocker une
*closure* qui s'exécutera ultérieurement. Nous allons parler de la syntaxe des
*closures*, de l'inférence de type, et des traits au cours de ce chapitre.

<!--
Consider this hypothetical situation: we work at a startup that’s making an app
to generate custom exercise workout plans. The backend is written in Rust, and
the algorithm that generates the workout plan takes into account many factors,
such as the app user’s age, body mass index, exercise preferences, recent
workouts, and an intensity number they specify. The actual algorithm used isn’t
important in this example; what’s important is that this calculation takes a
few seconds. We want to call this algorithm only when we need to and only call
it once so we don’t make the user wait more than necessary.
-->

Considérez cette situation hypothétique: nous travaillons au démarrage d'une
application pour générer des plans d'entraînement personnalisés. Le backend est
écrit en Rust et l'algorithme qui génère les exercices prend en compte beaucoup
de facteurs comme l'age de l'utilisateur, son indice de masse corporelle, ses
préférences et une intensité paramétrable par l'utilisateur. L'algorithme
réellement utilisé n'est pas important pour cet exemple: ce qui est important
est que le calcul prenne plusieurs secondes. Nous voulons appeler l'algorithme
uniquement quand nous avons besoin, et seulement une fois, afin que
l'utilisateur n'est pas à attendre plus que nécessaire.

<!--
We’ll simulate calling this hypothetical algorithm with the function
`simulated_expensive_calculation` shown in Listing 13-1, which will print
`calculating slowly...`, wait for two seconds, and then return whatever number
we passed in.
-->

Nous allons, pour simuler l'appel à cet algorithme hypothétique, utiliser la
fonction `simulated_expensive_calculation` montré dans le Listing 13-1, qui
affichera `calculating slowly...`, attend 2 secondes, et ensuite retourne le
nombre qui lui a été passé :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
use std::thread;
use std::time::Duration;

fn simulated_expensive_calculation(intensity: u32) -> u32 {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    intensity
}
```
-->

```rust
use std::thread;
use std::time::Duration;

fn simulated_expensive_calculation(intensity: u32) -> u32 {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    intensity
}
```

<!--
<span class="caption">Listing 13-1: A function to stand in for a hypothetical
calculation that takes about 2 seconds to run</span>
-->

<span class="caption">Listing 13-1: Une fonction pour remplacer un calcul
hypothétique qui prend environ deux secondes à exécuter</span>

<!--
Next is the `main` function, which contains the parts of the workout app
important for this example. This function represents the code that the app will
call when a user asks for a workout plan. Because the interaction with the
app’s frontend isn’t relevant to the use of closures, we’ll hardcode values
representing inputs to our program and print the outputs.
-->

Ensuite, vient la fonction `main` qui contient les parties, de l'application
d'entraînement, importantes pour cet exemple. Cette fonction représente le code
que l'application appellera quand un utilisateur demande un plan d'entraînement.
Parce que l'interaction avec le frontend de l'application n'est pas pertinente à
l'utilisation des *closures*, nous allons coder en dur les valeurs représentant
les entrées de notre programme et afficher les résultats.

<!--
The required inputs are these:
-->

Les paramètres d'entrées requis sont:

<!--
* An intensity number from the user, which is specified when they request
  a workout to indicate whether they want a low-intensity workout or a
  high-intensity workout
* A random number that will generate some variety in the workout plans
-->

* Un nombre `intensité` de l'utilisateur, spécifié quand ils demandent un
  entraînement, afin qu'ils puissent indiquer s'ils veulent un entraînement de
  faible ou de haute intensité.
* Un nombre aléatoire qui produira une certaine variété dans les plans
  d'entraînement

<!--
The output will be the recommended workout plan. Listing 13-2 shows the `main`
function we’ll use.
-->

Le résultat sera le plan d'entraînement recommandé. Le Listing 13-2 montre la
fonction `main` que nous allons utiliser :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
fn main() {
    let simulated_user_specified_value = 10;
    let simulated_random_number = 7;

    generate_workout(
        simulated_user_specified_value,
        simulated_random_number
    );
}
# fn generate_workout(intensity: u32, random_number: u32) {}
```
-->

```rust
fn main() {
    let simulated_user_specified_value = 10;
    let simulated_random_number = 7;

    generate_workout(
        simulated_user_specified_value,
        simulated_random_number
    );
}
# fn generate_workout(intensity: u32, random_number: u32) {}
```

<!--
<span class="caption">Listing 13-2: A `main` function with hardcoded values to
simulate user input and random number generation</span>
-->

<span class="caption">Listing 13-2: Une fonction `main` avec des valeurs codées
en dur pour simuler l'entrée d'un utilisateur et la génération de nombres
aléatoires</span>

<!--
We’ve hardcoded the variable `simulated_user_specified_value` as 10 and the
variable `simulated_random_number` as 7 for simplicity’s sake; in an actual
program, we’d get the intensity number from the app frontend, and we’d use the
`rand` crate to generate a random number, as we did in the Guessing Game
example in Chapter 2. The `main` function calls a `generate_workout` function
with the simulated input values.
-->

Nous avons codé en dur la variable `simulated_user_specified_value` à 10 et la
variable `simulated_random_number` à 7 pour des raisons de simplicité ; dans un
programme réel, nous obtiendrions le nombre d'intensité à partir du frontend de
l'application et nous utiliserions la crate `rand` pour générer un nombre
aléatoire, comme nous l'avons fait dans l'exemple du Guessing Game dans le
chapitre 2. La fonction `main` appelle une fonction `generate_workout` avec les
valeurs d'entrée simulées.

<!--
Now that we have the context, let’s get to the algorithm. The function
`generate_workout` in Listing 13-3 contains the business logic of the
app that we’re most concerned with in this example. The rest of the code
changes in this example will be made to this function.
-->

Maintenant que nous avons le contexte, passons à l'algorithme. La fonction
`generate_workout` dans le Listing 13-3 contient la logique métier de
l'application qui nous préoccupe le plus dans cet exemple. Le reste des
changements de code dans cet exemple sera apporté à cette fonction :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
# use std::thread;
# use std::time::Duration;
#
# fn simulated_expensive_calculation(num: u32) -> u32 {
#     println!("calculating slowly...");
#     thread::sleep(Duration::from_secs(2));
#     num
# }
#
fn generate_workout(intensity: u32, random_number: u32) {
    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            simulated_expensive_calculation(intensity)
        );
        println!(
            "Next, do {} situps!",
            simulated_expensive_calculation(intensity)
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                simulated_expensive_calculation(intensity)
            );
        }
    }
}
```
-->

```rust
# use std::thread;
# use std::time::Duration;
#
# fn simulated_expensive_calculation(num: u32) -> u32 {
#     println!("calculating slowly...");
#     thread::sleep(Duration::from_secs(2));
#     num
# }
#
fn generate_workout(intensity: u32, random_number: u32) {
    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            simulated_expensive_calculation(intensity)
        );
        println!(
            "Next, do {} situps!",
            simulated_expensive_calculation(intensity)
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                simulated_expensive_calculation(intensity)
            );
        }
    }
}
```

<!--
<span class="caption">Listing 13-3: The business logic that prints the workout
plans based on the inputs and calls to the `simulated_expensive_calculation`
function</span>
-->

<span class="caption">Listing 13-3: La logique de gestion du programme qui
imprime les plans d'entraînement basés sur les entrées et les appels à la
fonction `simulated_expensive_calculation`.</span>

<!--
The code in Listing 13-3 has multiple calls to the slow calculation function.
The first `if` block calls `simulated_expensive_calculation` twice, the `if`
inside the outer `else` doesn’t call it at all, and the code inside the
second `else` case calls it once.
-->

Le code dans le Listing 13-3 a plusieurs appels à la fonction de calcul lent: le
premier bloc `if` appelle `simulated_expensive_calculation` deux fois, le `if`à
l'intérieur de l'`else` extérieur ne l'appelle pas du tout, et le code à
l'intérieur du second `else` cas l'appelle une fois.

<!--
The desired behavior of the `generate_workout` function is to first check
whether the user wants a low-intensity workout (indicated by a number less than
25) or a high-intensity workout (a number of 25 or greater).
-->

Le comportement souhaité de la fonction `generate_workout` est de vérifier
d'abord si l'utilisateur veut un entraînement de faible intensité (indiqué par
un nombre inférieur à 25) ou un entraînement de haute intensité (un nombre de 25
ou plus).

<!--
Low-intensity workout plans will recommend a number of push-ups and sit-ups
based on the complex algorithm we’re simulating.
-->

Les plans d'entraînement à faible intensité recommanderont un certain nombre de
pompes et d'abdominaux basés sur l'algorithme complexe que nous simulons.

<!--
If the user wants a high-intensity workout, there’s some additional logic: if
the value of the random number generated by the app happens to be 3, the app
will recommend a break and hydration. If not, the user will get a number of
minutes of running based on the complex algorithm.
-->

Si l'utilisateur souhaite un entraînement de haute intensité, il y a une logique
supplémentaire: si la valeur du nombre aléatoire généré par l'application est 3,
l'application recommandera une pause et une hydratation à la place. Sinon,
l'utilisateur recevra un nombre de minutes de course qui provient de
l'algorithme complexe.

<!--
This code works the way the business wants it to now, but let’s say the data
science team decides that we need to make some changes to the way we call the
`simulated_expensive_calculation` function in the future. To simplify the
update when those changes happen, we want to refactor this code so it calls the
`simulated_expensive_calculation` function only once. We also want to cut the
place where we’re currently unnecessarily calling the function twice without
adding any other calls to that function in the process. That is, we don’t want
to call it if the result isn’t needed, and we still want to call it only once.
-->

L'équipe de data science nous a fait savoir qu'il va y avoir des changements
dans la façon dont nous devrons appeler l'algorithme à l'avenir. Pour simplifier
la mise à jour lorsque ces changements se produisent, nous voulons refactorer ce
code pour qu'il n'appelle la fonction `simulated_expensive_calculation` qu'une
seule fois. Nous voulons également nous débarrasser de l'endroit où nous
appelons actuellement la fonction deux fois inutilement, sans ajouter d'autres
appels à cette fonction au cours de ce processus. C'est-à-dire, nous ne voulons
pas l'appeler si le résultat n'est pas nécessaire, et nous voulons quand même
l'appeler une seule fois.

<!--
#### Refactoring Using Functions
-->

#### Refactoring Using Functions

<!--
We could restructure the workout program in many ways. First, we’ll try
extracting the duplicated call to the `simulated_expensive_calculation`
function into a variable, as shown in Listing 13-4.
-->

Nous pourrions restructurer le programme d'entraînement de plusieurs façons.
Tout d'abord, nous allons essayer d'extraire l'appel dupliqué à la fonction
`expensive_calculation` dans une variable, comme le montre le Listing 13-4 :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
# use std::thread;
# use std::time::Duration;
#
# fn simulated_expensive_calculation(num: u32) -> u32 {
#     println!("calculating slowly...");
#     thread::sleep(Duration::from_secs(2));
#     num
# }
#
fn generate_workout(intensity: u32, random_number: u32) {
    let expensive_result =
        simulated_expensive_calculation(intensity);

    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            expensive_result
        );
        println!(
            "Next, do {} situps!",
            expensive_result
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_result
            );
        }
    }
}
```
-->

```rust
# use std::thread;
# use std::time::Duration;
#
# fn simulated_expensive_calculation(num: u32) -> u32 {
#     println!("calculating slowly...");
#     thread::sleep(Duration::from_secs(2));
#     num
# }
#
fn generate_workout(intensity: u32, random_number: u32) {
    let expensive_result =
        simulated_expensive_calculation(intensity);

    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            expensive_result
        );
        println!(
            "Next, do {} situps!",
            expensive_result
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_result
            );
        }
    }
}
```

<!--
<span class="caption">Listing 13-4: Extracting the calls to
`simulated_expensive_calculation` to one place and storing the result in the
`expensive_result` variable</span>
-->

<span class="caption">Listing 13-4: Extraction des appels à
`simulated_expensive_calculation` à un seul endroit avant les blocs `if` et
stockage du résultat dans la variable `expensive_result`.</span>

<!--
This change unifies all the calls to `simulated_expensive_calculation` and
solves the problem of the first `if` block unnecessarily calling the function
twice. Unfortunately, we’re now calling this function and waiting for the
result in all cases, which includes the inner `if` block that doesn’t use the
result value at all.
-->

Ce changement unifie tous les appels à `simulated_expensive_calculation` et
résout le problème du premier bloc `if` qui appelle la fonction deux fois
inutilement. Malheureusement, nous appelons maintenant cette fonction et
attendons le résultat dans tous les cas, ce qui inclut le bloc `if` interne qui
n'utilise pas du tout la valeur du résultat.

<!--
We want to define code in one place in our program, but only *execute* that
code where we actually need the result. This is a use case for closures!
-->

Nous voulons définir le code à un seul endroit dans notre programme, mais
seulement *exécuter* ce code-là où nous avons réellement besoin du résultat.
C'est un cas d'utilisation pour les *closures*!

<!--
#### Refactoring with Closures to Store Code
-->

#### Les Closures stockent du code pour une exécution ultérieure

<!--
Instead of always calling the `simulated_expensive_calculation` function before
the `if` blocks, we can define a closure and store the *closure* in a variable
rather than storing the result of the function call, as shown in Listing 13-5.
We can actually move the whole body of `simulated_expensive_calculation` within
the closure we’re introducing here.
-->

Au lieu d'appeler toujours la fonction `simulated_expensive_calculation` avant
les blocs `if`, nous pouvons définir une closure et enregistrer la closure dans
une variable au lieu du résultat comme le montre le Listing 13-5. Nous pouvons
en fait choisir de déplacer l'ensemble du corps de
`simulated_expensive_calculation` dans la closure que nous introduisons ici :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
# use std::thread;
# use std::time::Duration;
#
let expensive_closure = |num| {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    num
};
# expensive_closure(5);
```
-->

```rust
# use std::thread;
# use std::time::Duration;
#
let expensive_closure = |num| {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    num
};
# expensive_closure(5);
```

<!--
<span class="caption">Listing 13-5: Defining a closure and storing it in the
`expensive_closure` variable</span>
-->

<span class="caption">Listing 13-5: Définition d'une *closure* et son
enregistrement dans la variable `expensive_closure`.</span>

<!--
The closure definition comes after the `=` to assign it to the variable
`expensive_closure`. To define a closure, we start with a pair of vertical
pipes (`|`), inside which we specify the parameters to the closure; this syntax
was chosen because of its similarity to closure definitions in Smalltalk and
Ruby. This closure has one parameter named `num`: if we had more than one
parameter, we would separate them with commas, like `|param1, param2|`.
-->

La définition de la *closure* vient après le `=` pour l'assigner à la variable
`expensive_closure`. Pour définir une fermeture, on commence par une paire de
tubes verticaux (`|`), à l'intérieur desquels on spécifie les paramètres à la
fermeture; cette syntaxe a été choisie en raison de sa similitude avec les
définitions de *closure* en Smalltalk et en Ruby. Cette closure a un paramètre
appelé `num` : si nous avions plus d'un paramètre, nous les séparerions par des
virgules, comme `|param1, param2|`.

<!--
After the parameters, we place curly brackets that hold the body of the
closure—these are optional if the closure body is a single expression. The end
of the closure, after the curly brackets, needs a semicolon to complete the
`let` statement. The value returned from the last line in the closure body
(`num`) will be the value returned from the closure when it’s called, because
that line doesn’t end in a semicolon; just as in function bodies.
-->

Après les paramètres, on met des accolades qui contiennent le corps de la
closure, celles-ci sont facultatives si le corps de la *closure* est une
expression unique. Après les accolades, nous avons besoin d'un point-virgule
pour terminer la déclaration commencée avec `let`. La valeur à la dernière ligne
dans le corps de la closure (`num`), comme cette ligne ne se termine pas par un
point-virgule, sera la valeur renvoyée par la closure lorsqu'elle est appelée,
exactement  comme dans les corps de fonction.

<!--
Note that this `let` statement means `expensive_closure` contains the
*definition* of an anonymous function, not the *resulting value* of calling the
anonymous function. Recall that we’re using a closure because we want to define
the code to call at one point, store that code, and call it at a later point;
the code we want to call is now stored in `expensive_closure`.
-->

Notez que cette instruction `let` signifie que la variable `expensive_closure`
contient la *définition* d'une fonction anonyme, pas la valeur *résultant* de
l'appel à cette fonction anonyme. Rappelons la raison pour laquelle nous
utilisons une *closure* est parce que nous voulons définir le code à appeler à
un point, stocker ce code, et l'appeler à un point ultérieur; le code que nous
voulons appeler est maintenant stocké dans `expensive_closure`.

<!--
With the closure defined, we can change the code in the `if` blocks to call the
closure to execute the code and get the resulting value. We call a closure like
we do a function: we specify the variable name that holds the closure
definition and follow it with parentheses containing the argument values we
want to use, as shown in Listing 13-6.
-->

Maintenant que nous avons défini la *closure*, nous pouvons changer le code dans
les blocs `if` pour appeler la closure afin d'exécuter le code et obtenir la
valeur résultante. L'appel d'une closure est comme l'appel d'une fonction ; nous
spécifions le nom de la variable qui détient la définition de la closure et la
complétons avec des parenthèses contenant les valeurs du ou des arguments que
nous voulons utiliser pour cet appel comme indiqué dans le Listing 13-6 :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
# use std::thread;
# use std::time::Duration;
#
fn generate_workout(intensity: u32, random_number: u32) {
    let expensive_closure = |num| {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    };

    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            expensive_closure(intensity)
        );
        println!(
            "Next, do {} situps!",
            expensive_closure(intensity)
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_closure(intensity)
            );
        }
    }
}
```
-->

```rust
# use std::thread;
# use std::time::Duration;
#
fn generate_workout(intensity: u32, random_number: u32) {
    let expensive_closure = |num| {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    };

    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            expensive_closure(intensity)
        );
        println!(
            "Next, do {} situps!",
            expensive_closure(intensity)
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_closure(intensity)
            );
        }
    }
}
```

<!--
<span class="caption">Listing 13-6: Calling the `expensive_closure` we’ve
defined</span>
-->

<span class="caption">Listing 13-6: Appel de la *closure* `expensive_closure`
que nous avons défini</span>

<!--
Now the expensive calculation is called in only one place, and we’re only
executing that code where we need the results.
-->

Maintenant, le calcul coûteux n'est appelé qu'à un seul endroit, et nous
n'exécutons ce code que là où nous avons besoin des résultats.

<!--
However, we’ve reintroduced one of the problems from Listing 13-3: we’re still
calling the closure twice in the first `if` block, which will call the
expensive code twice and make the user wait twice as long as they need to. We
could fix this problem by creating a variable local to that `if` block to hold
the result of calling the closure, but closures provide us with another
solution. We’ll talk about that solution in a bit. But first let’s talk about
why there aren’t type annotations in the closure definition and the traits
involved with closures.
-->

Cependant, nous avons réintroduit l'un des problèmes du Listing 13-3: nous
continuons d'appeler la *closure* deux fois dans le premier bloc `if`, qui
appellera le code coûteux deux fois et fera attendre l'utilisateur deux fois
plus longtemps que nécessaire. Nous pourrions résoudre ce problème en créant une
variable locale à ce bloc  `if` pour conserver le résultat de l'appel à la
*closure*, mais les *closures* nous fournissent une autre solution. Mais
commençons d'abord par expliquer pourquoi il n'y a pas d'annotation de type dans
la définition des *closures* et les traits impliqués dans les *closures*.

<!--
### Closure Type Inference and Annotation
-->

### Closure : Inférence de Type et Annotations

<!--
Closures don’t require you to annotate the types of the parameters or the
return value like `fn` functions do. Type annotations are required on functions
because they’re part of an explicit interface exposed to your users. Defining
this interface rigidly is important for ensuring that everyone agrees on what
types of values a function uses and returns. But closures aren’t used in an
exposed interface like this: they’re stored in variables and used without
naming them and exposing them to users of our library.
-->

Les *closures* ne nécessitent pas d'annoter le type des paramètres ou la valeur
de retour comme le font les fonctions `fn`. Les annotations de type sont
nécessaires pour les fonctions, car elles font partie d'une interface explicite
exposée à vos utilisateurs. Définir cette interface de manière rigide est
important pour s'assurer que tout le monde s'accorde sur les types de valeurs
qu'une fonction utilise et retourne. Mais les *closures* ne sont pas utilisées
dans une interface exposée comme cela: elles sont stockées dans des variables et
utilisées sans les nommer ni les exposer aux utilisateurs de notre bibliothèque.

<!--
Closures are usually short and relevant only within a narrow context rather
than in any arbitrary scenario. Within these limited contexts, the compiler is
reliably able to infer the types of the parameters and the return type, similar
to how it’s able to infer the types of most variables.
-->

En outre, les *closures* sont généralement brèves et ne sont pertinentes que
dans un contexte plutôt restreint que dans un scénario arbitraire. Dans ce
contexte limité, le compilateur est capable d'inférer le type des paramètres et
le type de retour, comme il est capable d'inférer le type de la plupart des
variables.

<!--
Making programmers annotate the types in these small, anonymous functions would
be annoying and largely redundant with the information the compiler already has
available.
-->

Faire annoter par les programmeurs le type de ces petites fonctions anonymes
serait agaçant et largement redondant avec l'information dont dispose déjà le
compilateur.

<!--
As with variables, we can add type annotations if we want to increase
explicitness and clarity at the cost of being more verbose than is strictly
necessary. Annotating the types for the closure we defined in Listing 13-5
would look like the definition shown in Listing 13-7.
-->

Comme les variables, nous pouvons ajouter des annotations de type si nous
voulons expliciter et augmenter la clarté  du code au risque d'être plus verbeux
que ce qui est strictement nécessaire; annoter les types pour la closure que
nous avons définie dans le Listing 13-4 ressemblerait à la définition présentée
dans le Listing 13-7 :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
# use std::thread;
# use std::time::Duration;
#
let expensive_closure = |num: u32| -> u32 {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    num
};
```
-->

```rust
# use std::thread;
# use std::time::Duration;
#
let expensive_closure = |num: u32| -> u32 {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    num
};
```

<!--
<span class="caption">Listing 13-7: Adding optional type annotations of the
parameter and return value types in the closure</span>
-->

<span class="caption">Listing 13-7: Ajout d'annotations de type optionnel sur
les paramètres et les valeurs de retour dans la closure</span>

<!--
With type annotations added, the syntax of closures looks more similar to the
syntax of functions. The following is a vertical comparison of the syntax for
the definition of a function that adds 1 to its parameter and a closure that
has the same behavior. We’ve added some spaces to line up the relevant parts.
This illustrates how closure syntax is similar to function syntax except for
the use of pipes and the amount of syntax that is optional:
-->

La syntaxe des *closures* et des fonctions semble plus similaire avec les
annotations de type. Ce qui suit est une comparaison verticale de la syntaxe
pour la définition d'une fonction qui ajoute une fonction à son paramètre, et
une *closures* qui a le même comportement. Nous avons ajouté des espaces pour
aligner les parties pertinentes. Ceci illustre comment la syntaxe des *closures*
est similaire à la syntaxe des fonctions, sauf pour l'utilisation des tubes
verticaux et la quantité de syntaxe facultative :

<!--
```rust,ignore
fn  add_one_v1   (x: u32) -> u32 { x + 1 }
let add_one_v2 = |x: u32| -> u32 { x + 1 };
let add_one_v3 = |x|             { x + 1 };
let add_one_v4 = |x|               x + 1  ;
```
-->

```rust,ignore
fn  add_one_v1   (x: u32) -> u32 { x + 1 }
let add_one_v2 = |x: u32| -> u32 { x + 1 };
let add_one_v3 = |x|             { x + 1 };
let add_one_v4 = |x|               x + 1  ;
```

<!--
The first line shows a function definition, and the second line shows a fully
annotated closure definition. The third line removes the type annotations from
the closure definition, and the fourth line removes the brackets, which are
optional because the closure body has only one expression. These are all valid
definitions that will produce the same behavior when they’re called.
-->

La première ligne affiche une définition de fonction et la deuxième ligne une
définition de closure entièrement annotée. La troisième ligne supprime les
annotations de type de la définition de la closure, et la quatrième ligne
supprime les crochets qui sont facultatifs, parce que le corps d'une closure n'
a qu'une seule expression. Ce sont toutes des définitions valides qui produiront
le même comportement quand on les appelle.

<!--
Closure definitions will have one concrete type inferred for each of their
parameters and for their return value. For instance, Listing 13-8 shows the
definition of a short closure that just returns the value it receives as a
parameter. This closure isn’t very useful except for the purposes of this
example. Note that we haven’t added any type annotations to the definition: if
we then try to call the closure twice, using a `String` as an argument the
first time and a `u32` the second time, we’ll get an error.
-->

Les définitions de *closures* auront un type concret déduit pour chacun de leurs
paramètres et pour leur valeur de retour. Par exemple, le Listing 13-8 montre la
définition d'une petite closure qui renvoie simplement la valeur qu'elle reçoit
comme paramètre. Cette closure n'est pas très utile sauf pour les besoins de cet
exemple. Notez que nous n'avons pas ajouté d'annotations de type à la
définition: si nous essayons alors d'appeler la closure deux fois, en utilisant
une `String` comme argument la première fois et un `u32` la deuxième fois, nous
obtiendrons une erreur :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust,ignore,does_not_compile
let example_closure = |x| x;

let s = example_closure(String::from("hello"));
let n = example_closure(5);
```
-->

```rust,ignore,does_not_compile
let example_closure = |x| x;

let s = example_closure(String::from("hello"));
let n = example_closure(5);
```

<!--
<span class="caption">Listing 13-8: Attempting to call a closure whose types
are inferred with two different types</span>
-->

<span class="caption">Listing 13-8: Tentative d'appeler une closure dont les
types sont inférés avec deux types différents</span>

<!--
The compiler gives us this error:
-->

Le compilateur nous renvoie l'erreur suivante :

<!--
```text
error[E0308]: mismatched types
 -- > src/main.rs
  |
  | let n = example_closure(5);
  |                         ^ expected struct `std::string::String`, found
  integer
  |
  = note: expected type `std::string::String`
             found type `{integer}`
```
-->

```text
error[E0308]: mismatched types
 -- > src/main.rs
  |
  | let n = example_closure(5);
  |                         ^ expected struct `std::string::String`, found
  integer
  |
  = note: expected type `std::string::String`
             found type `{integer}`
```

<!--
The first time we call `example_closure` with the `String` value, the compiler
infers the type of `x` and the return type of the closure to be `String`. Those
types are then locked in to the closure in `example_closure`, and we get a type
error if we try to use a different type with the same closure.
-->

La première fois que nous appelons `example_closure` avec une `String`, le
compilateur infère le type de `x` et le type de retour de la closure comme étant
de type `String`. Ces types sont ensuite verrouillés dans `example_closure`, et
nous obtenons une erreur de type si nous essayons d'utiliser un type différent
avec la même closure.

<!--
### Storing Closures Using Generic Parameters and the `Fn` Traits
-->

### Stockage de Closures avec des paramètres génériques et le Trait `Fn`

<!--
Let’s return to our workout generation app. In Listing 13-6, our code was still
calling the expensive calculation closure more times than it needed to. One
option to solve this issue is to save the result of the expensive closure in a
variable for reuse and use the variable in each place we need the result,
instead of calling the closure again. However, this method could result in a
lot of repeated code.
-->

Revenons à notre application de génération d'entraînement. Dans le Listing 13-6,
notre code appelait toujours la closure de calcul coûteux plus de fois qu'il
n'en avait besoin. Une option pour résoudre ce problème est de sauvegarder le
résultat de la closure coûteuse dans une variable pour une future utilisation et
d'utiliser la variable à  chaque endroit où nous en avons besoin au lieu de
rappeler la closure plusieurs fois. Cependant, cette méthode pourrait donner
lieu à un code très redondant.

<!--
Fortunately, another solution is available to us. We can create a struct that
will hold the closure and the resulting value of calling the closure. The
struct will execute the closure only if we need the resulting value, and it
will cache the resulting value so the rest of our code doesn’t have to be
responsible for saving and reusing the result. You may know this pattern as
*memoization* or *lazy evaluation*.
-->

Heureusement, une autre solution s'offre à nous. Nous pouvons créer une struct
qui sauvegardera la closure et la valeur qui en résulte. La structure
n'exécutera la closure que si nous avons besoin de la valeur résultante, et elle
cachera la valeur résultante pour que le reste de notre code n'ait pas à être
responsable de la sauvegarde et de la réutilisation du résultat. Vous connaissez
peut-être ce pattern sous le nom de *mémoization* ou *évaluation larmoyante*.

<!--
To make a struct that holds a closure, we need to specify the type of the
closure, because a struct definition needs to know the types of each of its
fields. Each closure instance has its own unique anonymous type: that is, even
if two closures have the same signature, their types are still considered
different. To define structs, enums, or function parameters that use closures,
we use generics and trait bounds, as we discussed in Chapter 10.
-->

Pour faire qu'une struct détiennne une closure, il faut spécifier le type de
closure, car une définition de structure a besoin de connaître les types de
chacun de ses champs. Chaque instance de closure a son propre type anonyme
unique: c'est-à-dire que même si deux *closures* ont la même signature, leurs
types sont toujours considérés comme différents. Pour définir des structs, des
enums ou des paramètres de fonction qui utilisent les *closures*, nous utilisons
des génériques et des limites de "traits", comme nous l'avons vu au chapitre 10.

<!--
The `Fn` traits are provided by the standard library. All closures implement at
least one of the traits: `Fn`, `FnMut`, or `FnOnce`. We’ll discuss the
difference between these traits in the [“Capturing the Environment with
Closures”](#capturing-the-environment-with-closures)<!-- ignore -- > section; in
this example, we can use the `Fn` trait.
-->

Les traits `Fn` sont fournis par la bibliothèque standard. Toutes les *closures*
implémentent un des traits suivants : `Fn`, `FnMut`, ou `FnOnce`. Nous
discuterons de la différence entre ces traits dans la prochaine section sur la
capture de l'environnement ; dans cet exemple, nous pouvons utiliser le
caractère `Fn`.

<!--
We add types to the `Fn` trait bound to represent the types of the parameters
and return values the closures must have to match this trait bound. In this
case, our closure has a parameter of type `u32` and returns a `u32`, so the
trait bound we specify is `Fn(u32) -> u32`.
-->

Nous ajoutons des types au trait `Fn` pour représenter les types de paramètres
et les valeurs de retour que les *closures* doivent avoir pour correspondre à ce
trait lié. Dans ce cas, notre closure a un paramètre de type `u32` et renvoie un
`u32`, donc le trait lié que nous spécifions est `Fn (u32) -> u32`.

<!--
Listing 13-9 shows the definition of the `Cacher` struct that holds a closure
and an optional result value.
-->

Le Listing 13-9 montre la définition de la struct`Cacher` qui possède une
closure et une valeur de résultat optionnelle :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
struct Cacher<T>
    where T: Fn(u32) -> u32
{
    calculation: T,
    value: Option<u32>,
}
```
-->

```rust
struct Cacher<T>
    where T: Fn(u32) -> u32
{
    calculation: T,
    value: Option<u32>,
}
```

<!--
<span class="caption">Listing 13-9: Defining a `Cacher` struct that holds a
closure in `calculation` and an optional result in `value`</span>
-->

<span class="caption">Listing 13-9: Définition d'une struct `Cacher` qui possède
une closure dans `calculation` et un résultat facultatif dans `value`.</span>

<!--
The `Cacher` struct has a `calculation` field of the generic type `T`. The
trait bounds on `T` specify that it’s a closure by using the `Fn` trait. Any
closure we want to store in the `calculation` field must have one `u32`
parameter (specified within the parentheses after `Fn`) and must return a
`u32` (specified after the `->`).
-->

La struct `Cacher` a un champ `calculation` du type générique `T`. Les limites
du trait sur `T` spécifient que c'est une closure en utilisant le trait `Fn`.
Toute closure que l'on veut stocker dans le champ `calculation` doit avoir un
paramètre `u32` (spécifié entre parenthèse après `->`) et doit retourner un
`u32` (spécifié après `->`).

<!--
> Note: Functions can implement all three of the `Fn` traits too. If what we
> want to do doesn’t require capturing a value from the environment, we can use
> a function rather than a closure where we need something that implements an
> `Fn` trait.
-->

> Remarque : Les fonctions implémentent aussi les trois traits `Fn`. Si ce que
> nous voulons faire n' a pas besoin de capturer une > valeur de
> l'environnement nous pouvons utiliser une fonction plutôt qu'une closure où
> nous avons besoin de quelque chose qui > implémente un trait `Fn`.

<!--
The `value` field is of type `Option<u32>`. Before we execute the closure,
`value` will be `None`. When code using a `Cacher` asks for the *result* of the
closure, the `Cacher` will execute the closure at that time and store the
result within a `Some` variant in the `value` field. Then if the code asks for
the result of the closure again, instead of executing the closure again, the
`Cacher` will return the result held in the `Some` variant.
-->

The `value` field is of type `Option<u32>`.
Avant d'exécuter la fermeture, `value` sera initialisée à `None`. Lorsque ducode
utilisant un `Cacher` demande le *result* de la closure, le `Cacher` exécutera
la closureà ce moment-là et stockera le résultat dans une variante `Some` dans
le champ `value`. Ensuite, si le code demande à nouveau le résultat de la
closure, au lieu d'exécuter à nouveau la closure, le `Cacher` renverra le
résultat contenu dans la variante `Some`.

<!--
The logic around the `value` field we’ve just described is defined in Listing
13-10.
-->

La logique autour du champ `value` que nous venons de décrire est définie dans
le Listing 13-10 :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
# struct Cacher<T>
#     where T: Fn(u32) -> u32
# {
#     calculation: T,
#     value: Option<u32>,
# }
#
impl<T> Cacher<T>
    where T: Fn(u32) -> u32
{
    fn new(calculation: T) -> Cacher<T> {
        Cacher {
            calculation,
            value: None,
        }
    }

    fn value(&mut self, arg: u32) -> u32 {
        match self.value {
            Some(v) => v,
            None => {
                let v = (self.calculation)(arg);
                self.value = Some(v);
                v
            },
        }
    }
}
```
-->

```rust
# struct Cacher<T>
#     where T: Fn(u32) -> u32
# {
#     calculation: T,
#     value: Option<u32>,
# }
#
impl<T> Cacher<T>
    where T: Fn(u32) -> u32
{
    fn new(calculation: T) -> Cacher<T> {
        Cacher {
            calculation,
            value: None,
        }
    }

    fn value(&mut self, arg: u32) -> u32 {
        match self.value {
            Some(v) => v,
            None => {
                let v = (self.calculation)(arg);
                self.value = Some(v);
                v
            },
        }
    }
}
```

<!--
<span class="caption">Listing 13-10: The caching logic of `Cacher`</span>
-->

<span class="caption">Listing 13-10: La logique de caching de `Cacher`</span>

<!--
We want `Cacher` to manage the struct fields’ values rather than letting the
calling code potentially change the values in these fields directly, so these
fields are private.
-->

Nous voulons que `Cacher` gère les valeurs des champs de structure plutôt que de
laisser le code appelant, la possibilité de  modifier les valeurs dans ces
champs directement, donc nous laissons ces champs privés.

<!--
The `Cacher::new` function takes a generic parameter `T`, which we’ve defined
as having the same trait bound as the `Cacher` struct. Then `Cacher::new`
returns a `Cacher` instance that holds the closure specified in the
`calculation` field and a `None` value in the `value` field, because we haven’t
executed the closure yet.
-->

La fonction `Cacher::new` prend un paramètre générique `T`, que nous avons
défini comme ayant le même caractère lié que la struct `Cacher`. Puis
`Cacher::new` renvoie une instance `Cacher` qui contient la closure spécifiée
dans le champ `calculation` et une valeur `None` dans le champ `value`, parce
que nous n'avons pas encore exécuté la closure.

<!--
When the calling code needs the result of evaluating the closure, instead of
calling the closure directly, it will call the `value` method. This method
checks whether we already have a resulting value in `self.value` in a `Some`;
if we do, it returns the value within the `Some` without executing the closure
again.
-->

Lorsque le code appelant veut le résultat de l'évaluation de la closure, au lieu
d'appeler directement la clôture, il appellera la méthode `value`. Cette méthode
vérifie si nous avons déjà une valeur  dans `self.value` dans un `Some`; si tel
est le cas, elle renvoie la valeur contenue dans le `Some` sans exécuter de
nouveau la closure.

<!--
If `self.value` is `None`, the code calls the closure stored in
`self.calculation`, saves the result in `self.value` for future use, and
returns the value as well.
-->

Si `self.value` est à `None`, nous appelons la closure stockée dans
`self.calculation`, sauvegardons le résultat dans `self. value` pour une
utilisation future, et retournons la valeur après calcul.

<!--
Listing 13-11 shows how we can use this `Cacher` struct in the function
`generate_workout` from Listing 13-6.
-->

Le Listing 13-11 montre comment utiliser cette struct `Cacher` dans la fonction
`generate_workout` du Listing 13-6 :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
# use std::thread;
# use std::time::Duration;
#
# struct Cacher<T>
#     where T: Fn(u32) -> u32
# {
#     calculation: T,
#     value: Option<u32>,
# }
#
# impl<T> Cacher<T>
#     where T: Fn(u32) -> u32
# {
#     fn new(calculation: T) -> Cacher<T> {
#         Cacher {
#             calculation,
#             value: None,
#         }
#     }
#
#     fn value(&mut self, arg: u32) -> u32 {
#         match self.value {
#             Some(v) => v,
#             None => {
#                 let v = (self.calculation)(arg);
#                 self.value = Some(v);
#                 v
#             },
#         }
#     }
# }
#
fn generate_workout(intensity: u32, random_number: u32) {
    let mut expensive_result = Cacher::new(|num| {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    });

    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            expensive_result.value(intensity)
        );
        println!(
            "Next, do {} situps!",
            expensive_result.value(intensity)
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_result.value(intensity)
            );
        }
    }
}
```
-->

```rust
# use std::thread;
# use std::time::Duration;
#
# struct Cacher<T>
#     where T: Fn(u32) -> u32
# {
#     calculation: T,
#     value: Option<u32>,
# }
#
# impl<T> Cacher<T>
#     where T: Fn(u32) -> u32
# {
#     fn new(calculation: T) -> Cacher<T> {
#         Cacher {
#             calculation,
#             value: None,
#         }
#     }
#
#     fn value(&mut self, arg: u32) -> u32 {
#         match self.value {
#             Some(v) => v,
#             None => {
#                 let v = (self.calculation)(arg);
#                 self.value = Some(v);
#                 v
#             },
#         }
#     }
# }
#
fn generate_workout(intensity: u32, random_number: u32) {
    let mut expensive_result = Cacher::new(|num| {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    });

    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            expensive_result.value(intensity)
        );
        println!(
            "Next, do {} situps!",
            expensive_result.value(intensity)
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_result.value(intensity)
            );
        }
    }
}
```

<!--
<span class="caption">Listing 13-11: Using `Cacher` in the `generate_workout`
function to abstract away the caching logic</span>
-->

<span class="caption">Listing 13-11: Utilisation de `Cacher` dans la fonction
`generate_workout` pour rendre abstrait la logique de caching.</span>

<!--
Instead of saving the closure in a variable directly, we save a new instance of
`Cacher` that holds the closure. Then, in each place we want the result, we
call the `value` method on the `Cacher` instance. We can call the `value`
method as many times as we want, or not call it at all, and the expensive
calculation will be run a maximum of once.
-->

Au lieu de sauvegarder la closure dans une variable directement, nous
sauvegardons une nouvelle instance de `Cacher` qui contient la closure. Ensuite,
à chaque fois que nous voulons le résultat, nous appelons la méthode `value` sur
l'instance de `Cacher`. Nous pouvons appeler la méthode `value` autant de fois
que nous voulons, ou ne pas l'appeler du tout, et le calcul coûteux sera exécuté
un maximum, une fois.

<!--
Try running this program with the `main` function from Listing 13-2. Change the
values in the `simulated_user_specified_value` and `simulated_random_number`
variables to verify that in all the cases in the various `if` and `else`
blocks, `calculating slowly...` appears only once and only when needed. The
`Cacher` takes care of the logic necessary to ensure we aren’t calling the
expensive calculation more than we need to so `generate_workout` can focus on
the business logic.
-->

Essayez d'exécuter ce programme avec la fonction `main` du Listing 13-2.
Modifiez les valeurs des variables `simulated_user_specified_value` et
`simulated_random_number` pour vérifier que dans tous les cas, dans les
différents blocs `if` et `else`, `calculating slowly...` n'apparaît qu'une seule
fois et seulement si nécessaire. Le `Cacher` prend soin de la logique nécessaire
pour s'assurer que nous n'appelons pas le calcul coûteux plus que nous n'en
avons besoin, ainsi `generate_workout` peut se concentrer sur la logique
business.

<!--
### Limitations of the `Cacher` Implementation
-->

### Limitations de l'implémentation de `Cacher`

<!--
Caching values is a generally useful behavior that we might want to use in
other parts of our code with different closures. However, there are two
problems with the current implementation of `Cacher` that would make reusing it
in different contexts difficult.
-->

Cacher des valeurs est un comportement généralement utile que nous pourrions
vouloir utiliser dans d'autres parties de notre code avec différentes
*closures*. Cependant, il y a deux problèmes avec la mise en oeuvre actuelle de
`Cacher` qui rendraient difficile sa réutilisation dans des contextes
différents.

<!--
The first problem is that a `Cacher` instance assumes it will always get the
same value for the parameter `arg` to the `value` method. That is, this test of
`Cacher` will fail:
-->

Le premier problème est qu'une instance de `Cacher` suppose qu'elle obtiennne
toujours la même valeur pour le paramètre `arg` à la méthode `value`. Autrement
dit, ce test sur `Cacher` échouera:

<!--
```rust,ignore,panics
#[test]
fn call_with_different_values() {
    let mut c = Cacher::new(|a| a);

    let v1 = c.value(1);
    let v2 = c.value(2);

    assert_eq!(v2, 2);
}
```
-->

```rust,ignore
#[test]
fn call_with_different_values() {
    let mut c = Cacher::new(|a| a);

    let v1 = c.value(1);
    let v2 = c.value(2);

    assert_eq!(v2, 2);
}
```

<!--
This test creates a new `Cacher` instance with a closure that returns the value
passed into it. We call the `value` method on this `Cacher` instance with an
`arg` value of 1 and then an `arg` value of 2, and we expect the call to
`value` with the `arg` value of 2 to return 2.
-->

Ce test créé une nouvelle instance de `Cacher` avec une closure qui retourne la
valeur qui lui est passée. Nous appelons la méthode `value` sur cette instance
de `Cacher` avec une valeur `arg` de 1 et ensuite une valeur `arg` de 2, et nous
nous attendons à ce que l'appel à `value` avec la valeur `arg` de 2 devrait
retourner 2.

<!--
Run this test with the `Cacher` implementation in Listing 13-9 and Listing
13-10, and the test will fail on the `assert_eq!` with this message:
-->

Exécutez ce test avec l'implémentation de `Cacher` du Listing 13-9 et du Listing
13-10, et le test échouera sur le `assert_eq!` avec ce message:

<!-- markdownlint-disable -->
<!--
```text
thread 'call_with_different_values' panicked at 'assertion failed: `(left == right)`
  left: `1`,
 right: `2`', src/main.rs
```
-->
<!-- markdownlint-restore -->

```text
thread 'call_with_different_values' panicked at 'assertion failed: `(left == right)`
  left: `1`,
 right: `2`', src/main.rs
```

<!--
The problem is that the first time we called `c.value` with 1, the `Cacher`
instance saved `Some(1)` in `self.value`. Thereafter, no matter what we pass in
to the `value` method, it will always return 1.
-->

Le problème est que la première fois que nous avons appelé `c. value` avec 1,
l'instance `Cacher` a sauvé `Some(1)` dans `self.value`. Par la suite, peu
importe ce que nous passons à la méthode `value`, elle retournera toujours 1.

<!--
Try modifying `Cacher` to hold a hash map rather than a single value. The keys
of the hash map will be the `arg` values that are passed in, and the values of
the hash map will be the result of calling the closure on that key. Instead of
looking at whether `self.value` directly has a `Some` or a `None` value, the
`value` function will look up the `arg` in the hash map and return the value if
it’s present. If it’s not present, the `Cacher` will call the closure and save
the resulting value in the hash map associated with its `arg` value.
-->

Essayez de modifier `Cacher` pour tenir une hashmap plutôt qu'une seule valeur.
Les clés de la hashmap seront les valeurs `arg` qui lui sont passées, et les
valeurs de la hashmap  seront le résultat de l'appel de la fermeture sur cette
clé. Plutôt que de regarder si `self.value` a directement une valeur `Some` ou
une valeur `None`, la fonction `value` recherchera `arg` dans la hashmap et
retournera la valeur si elle est présente. S'il n'est pas présent, le `Cacher`
appellera la closure et sauvegardera la valeur résultante dans la hashmap
associée à sa valeur `arg`.

<!--
The second problem with the current `Cacher` implementation is that it only
accepts closures that take one parameter of type `u32` and return a `u32`. We
might want to cache the results of closures that take a string slice and return
`usize` values, for example. To fix this issue, try introducing more generic
parameters to increase the flexibility of the `Cacher` functionality.
-->

Le second problème avec l'implémentation actuelle de `Cacher` est qu'il
n'accepte que les *closures* qui prennent un paramètre de type `u32` et
renvoient un `u32`. Nous pourrions vouloir mettre en cache les résultats des
fermetures qui prennent une slice d'une string et renvoient des valeurs `usize`,
par exemple. Pour corriger ce problème, essayez d'introduire des paramètres plus
génériques pour augmenter la flexibilité de la fonctionnalité que possède
`Cacher`.

<!--
### Capturing the Environment with Closures
-->

### Capturer l'environnement avec des Closures

<!--
In the workout generator example, we only used closures as inline anonymous
functions. However, closures have an additional capability that functions don’t
have: they can capture their environment and access variables from the scope in
which they’re defined.
-->

Dans l'exemple du générateur d'entraînement, nous n'avons utilisé les *closures*
que comme des fonctions anonymes "inline". Cependant, les *closures* ont une
capacité supplémentaire que les fonctions n'ont pas : elles peuvent capturer
leur environnement et accéder aux variables à partir du scope dans lequel elles
sont définies.

<!--
Listing 13-12 has an example of a closure stored in the `equal_to_x` variable
that uses the `x` variable from the closure’s surrounding environment.
-->

Le Listing 13-12 a un exemple de closure stockée dans la variable `equal_to_x`
qui utilise la variable `x` de l'environnement environnant de la closure :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
fn main() {
    let x = 4;

    let equal_to_x = |z| z == x;

    let y = 4;

    assert!(equal_to_x(y));
}
```
-->

```rust
fn main() {
    let x = 4;

    let equal_to_x = |z| z == x;

    let y = 4;

    assert!(equal_to_x(y));
}
```

<!--
<span class="caption">Listing 13-12: Example of a closure that refers to a
variable in its enclosing scope</span>
-->

<span class="caption">Listing 13-12: Exemple d'une closure qui réfère à une
variable du scope la contenant.</span>

<!--
Here, even though `x` is not one of the parameters of `equal_to_x`, the
`equal_to_x` closure is allowed to use the `x` variable that’s defined in the
same scope that `equal_to_x` is defined in.
-->

Ici, même si `x` n'est pas un des paramètres de `equal_to_x`, la closure
`equal_to_x` est autorisée à utiliser la variable `x` définie dans la même
portée (ou environnement) que `equal_to_x`.

<!--
We can’t do the same with functions; if we try with the following example, our
code won’t compile:
-->

Nous ne pouvons pas faire la même chose avec les fonctions; si nous essayons
avec l'exemple suivant, notre code ne compilera pas :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust,ignore,does_not_compile
fn main() {
    let x = 4;

    fn equal_to_x(z: i32) -> bool { z == x }

    let y = 4;

    assert!(equal_to_x(y));
}
```
-->

```rust,ignore,does_not_compile
fn main() {
    let x = 4;

    fn equal_to_x(z: i32) -> bool { z == x }

    let y = 4;

    assert!(equal_to_x(y));
}
```

<!--
We get an error:
-->

Nous obtenons l'erreur suivante:

<!--
```text
error[E0434]: can't capture dynamic environment in a fn item; use the || { ...
} closure form instead
 -- > src/main.rs
  |
4 |     fn equal_to_x(z: i32) -> bool { z == x }
  |                                          ^
```
-->

```text
error[E0434]: can't capture dynamic environment in a fn item; use the || { ...
} closure form instead
 -- > src/main.rs
  |
4 |     fn equal_to_x(z: i32) -> bool { z == x }
  |                                          ^
```

<!--
The compiler even reminds us that this only works with closures!
-->

Le compilateur nous rappelle même que cela ne fonctionne qu'avec les *closures*!

<!--
When a closure captures a value from its environment, it uses memory to store
the values for use in the closure body. This use of memory is overhead that we
don’t want to pay in more common cases where we want to execute code that
doesn’t capture its environment. Because functions are never allowed to capture
their environment, defining and using functions will never incur this overhead.
-->

Lorsqu'une closure capture une valeur de son environnement, elle utilise la
mémoire pour stocker les valeurs à utiliser dans son corps. Cette utilisation de
la mémoire a un coût supplémentaire que nous ne voulons pas payer dans les cas
les plus courants où nous voulons exécuter du code qui ne capture pas leur
environnement. Comme les fonctions ne sont jamais autorisées à capturer leur
environnement, la définition et l'utilisation des fonctions n'occasionneront
jamais cette surcharge.

<!--
Closures can capture values from their environment in three ways, which
directly map to the three ways a function can take a parameter: taking
ownership, borrowing mutably, and borrowing immutably. These are encoded in the
three `Fn` traits as follows:
-->

Les *closures* peuvent captuer les valeurs de leur environnement de trois
façons, ce qui correspond directement aux trois façons dont une fonction peut
prendre un paramètre: la prise de propriété (ownership), l'emprunt immutable et
l'emprunt mutable. Ceux-ci sont codés dans les trois traits `Fn` comme suit:

<!--
* `FnOnce` consumes the variables it captures from its enclosing scope, known
  as the closure’s *environment*. To consume the captured variables, the
  closure must take ownership of these variables and move them into the closure
  when it is defined. The `Once` part of the name represents the fact that the
  closure can’t take ownership of the same variables more than once, so it can
  be called only once.
* `FnMut` can change the environment because it mutably borrows values.
* `Fn` borrows values from the environment immutably.
-->

* `FnOnce` consomme les variables qu'il capture à partir de son scope, connu
  sous le nom de l'*environnement* de la closure. Pour consommer les variables
  capturées, la closure doit s'approprier ces variables et les déplacer dans la
  closure lorsqu'elle est définie. La partie `Once` du nom représente le fait
  que la closure ne puisse pas prendre la propriété (ou l'ownership) des mêmes
  variables plus d'une fois, donc elle ne peut être appelée qu'une seule fois.
* `Fn` emprunte des valeurs à l'environnement de façon immutable.
* `FnMut` peut changer l'environnement parce qu'il emprunte des valeurs de
  manière mutable.

<!--
When you create a closure, Rust infers which trait to use based on how the
closure uses the values from the environment. All closures implement `FnOnce`
because they can all be called at least once. Closures that don’t move the
captured variables also implement `FnMut`, and closures that don’t need mutable
access to the captured variables also implement `Fn`. In Listing 13-12, the
`equal_to_x` closure borrows `x` immutably (so `equal_to_x` has the `Fn` trait)
because the body of the closure only needs to read the value in `x`.
-->

Lorsque nous créons une closure, Rust déduit quel caractère utiliser en se
basant sur la façon dont la closure utilise les valeurs de l'environnement. Dans
le Listing 13-12, la closure `equal_to_x` emprunte `x` immutablement (donc
`equal_to_x` a le trait `Fn`) parce que le corps de la closure ne fait que lire
la valeur de `x`.

<!--
If you want to force the closure to take ownership of the values it uses in the
environment, you can use the `move` keyword before the parameter list. This
technique is mostly useful when passing a closure to a new thread to move the
data so it’s owned by the new thread.
-->

Si nous voulons forcer la closure à s'approprier les valeurs qu'elle utilise
dans l'environnement, nous pouvons utiliser le mot-clé `move` avant la liste des
paramètres. Cette technique est surtout utile lorsque vous passez une fermeture
à un nouveau thread pour déplacer les données afin qu'elles appartiennent au
nouveau thread.

<!--
We’ll have more examples of `move` closures in Chapter 16 when we talk about
concurrency. For now, here’s the code from Listing 13-12 with the `move`
keyword added to the closure definition and using vectors instead of integers,
because integers can be copied rather than moved; note that this code will not
yet compile.
-->

Nous aurons d'autres exemples de *closures* utilisant `move` au chapitre 16
lorsque nous parlerons du multithreading. Pour l'instant, voici le code du
Listing 13-12 avec le mot-clé `move` ajouté à la définition de la closure et
utilisant des vecteurs au lieu d'entiers, car les entiers peuvent être copiés
plutôt que déplacés ; notez que ce code ne compilera pas encore :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Fichier: src/main.rs</span>

<!--
```rust,ignore,does_not_compile
fn main() {
    let x = vec![1, 2, 3];

    let equal_to_x = move |z| z == x;

    println!("can't use x here: {:?}", x);

    let y = vec![1, 2, 3];

    assert!(equal_to_x(y));
}
```
-->

```rust,ignore
fn main() {
    let x = vec![1, 2, 3];

    let equal_to_x = move |z| z == x;

    println!("can't use x here: {:?}", x);

    let y = vec![1, 2, 3];

    assert!(equal_to_x(y));
}
```

<!--
We receive the following error:
-->

Nous obtenons l'erreur suivante:

<!--
```text
error[E0382]: use of moved value: `x`
 -- > src/main.rs:6:40
  |
4 |     let equal_to_x = move |z| z == x;
  |                      -------- value moved (into closure) here
5 |
6 |     println!("can't use x here: {:?}", x);
  |                                        ^ value used here after move
  |
  = note: move occurs because `x` has type `std::vec::Vec<i32>`, which does not
  implement the `Copy` trait
```
-->

```text
error[E0382]: use of moved value: `x`
 --> src/main.rs:6:40
  |
4 |     let equal_to_x = move |z| z == x;
  |                      -------- value moved (into closure) here
5 |
6 |     println!("can't use x here: {:?}", x);
  |                                        ^ value used here after move
  |
  = note: move occurs because `x` has type `std::vec::Vec<i32>`, which does not
  implement the `Copy` trait
```

<!--
The `x` value is moved into the closure when the closure is defined, because we
added the `move` keyword. The closure then has ownership of `x`, and `main`
isn’t allowed to use `x` anymore in the `println!` statement. Removing
`println!` will fix this example.
-->

La valeur `x` est déplacée dans la closure lorsque la fermeture est définie,
parce que nous avons ajouté le mot-clé `move`. La closure a alors la propriété
de `x`, et `main` n'est plus autorisé à utiliser `x` dans l'instruction
`println!`. Supprimer `println!` corrigera cet exemple.

<!--
Most of the time when specifying one of the `Fn` trait bounds, you can start
with `Fn` and the compiler will tell you if you need `FnMut` or `FnOnce` based
on what happens in the closure body.
-->

La plupart du temps, lorsque vous spécifiez l'un des traits `Fn`, vous pouvez
commencer par `Fn` et le compilateur vous dira si vous avez besoin de `FnMut` ou
`FnOnce` basé sur ce qui se passe dans le corps de la closure.

<!--
To illustrate situations where closures that can capture their environment are
useful as function parameters, let’s move on to our next topic: iterators.
-->

Pour illustrer les situations où les *closures*, capturant leur environnement,
sont utiles comme paramètres de fonction, passons à notre prochain sujet: les
itérateurs.
