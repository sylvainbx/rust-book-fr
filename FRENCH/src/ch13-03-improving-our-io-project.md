<!--
## Improving Our I/O Project
-->

## Amélioration de notre Projet d'I/O

<!--
With this new knowledge about iterators, we can improve the I/O project in
Chapter 12 by using iterators to make places in the code clearer and more
concise. Let’s look at how iterators can improve our implementation of the
`Config::new` function and the `search` function.
-->

Grâce à ces nouvelles connaissances sur les itérateurs, nous pouvons améliorer
le projet d'I/O du chapitre 12 en utilisant des itérateurs pour rendre certains
endroits du code plus clairs et plus concis. Voyons comment les itérateurs
peuvent améliorer notre implémentation de la fonction `Config::new` et de la
fonction `search`.

<!--
### Removing a `clone` Using an Iterator
-->

### Suppression de l'appel à `clone` à l'aide d'un Itérateur

<!--
In Listing 12-6, we added code that took a slice of `String` values and created
an instance of the `Config` struct by indexing into the slice and cloning the
values, allowing the `Config` struct to own those values. In Listing 13-24,
we’ve reproduced the implementation of the `Config::new` function as it was in
Listing 12-23:
-->

Dans le Listing 12-6, nous avons ajouté du code qui a pris une *slice* de
valeurs `String` et créé une instance de la structure `Config` en utilisant les
index de la *slice* et en clonant les valeurs, permettant ainsi à la structure
`Config` de posséder ces valeurs. Dans le Listing 13-24, nous avons reproduit
l'implémentation de la fonction `Config:: new` comme dans le Listing 12-23 à la
fin du chapitre 12:

<!--
<span class="filename">Filename: src/lib.rs</span>
-->

<span class="filename">Filename: src/lib.rs</span>

<!--
```rust,ignore
impl Config {
    pub fn new(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("not enough arguments");
        }

        let query = args[1].clone();
        let filename = args[2].clone();

        let case_sensitive = env::var("CASE_INSENSITIVE").is_err();

        Ok(Config { query, filename, case_sensitive })
    }
}
```
-->

```rust,ignore
impl Config {
    pub fn new(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("not enough arguments");
        }

        let query = args[1].clone();
        let filename = args[2].clone();

        let case_sensitive = env::var("CASE_INSENSITIVE").is_err();

        Ok(Config { query, filename, case_sensitive })
    }
}
```

<!--
<span class="caption">Listing 13-24: Reproduction of the `Config::new` function
from Listing 12-23</span>
-->

<span class="caption">Listing 13-24: Reproduction de la fonction `Config::new`
à la fin du Chapitre 12</span>

<!--
At the time, we said not to worry about the inefficient `clone` calls because
we would remove them in the future. Well, that time is now!
-->

À ce moment-là, nous avions dit de ne pas s'inquiéter des appels inefficaces à
`clone` parce que nous les supprimerions à l'avenir. Eh bien, le moment est
venu!

<!--
We needed `clone` here because we have a slice with `String` elements in the
parameter `args`, but the `new` function doesn’t own `args`. To return
ownership of a `Config` instance, we had to clone the values from the `query`
and `filename` fields of `Config` so the `Config` instance can own its values.
-->

Nous avions besoin de `clone` ici parce que nous avons une *slice* avec des
éléments `String` dans le paramètre `args`, mais la fonction `new` ne possède
pas `args`. Pour rendre la propriété d'une instance `Config`, nous avons dû
cloner les valeurs des champs `query` et `filename` de `Config` pour que
l'instance `Config` puisse posséder ses propres valeurs.

<!--
With our new knowledge about iterators, we can change the `new` function to
take ownership of an iterator as its argument instead of borrowing a slice.
We’ll use the iterator functionality instead of the code that checks the length
of the slice and indexes into specific locations. This will clarify what the
`Config::new` function is doing because the iterator will access the values.
-->

Avec nos nouvelles connaissances sur les itérateurs, nous pouvons changer la
fonction `new` pour prendre en charge un itérateur comme argument au lieu
d'emprunter une *slice*. Nous utiliserons la fonctionnalité `next` des
itérateurs au lieu du code qui vérifie la longueur de la *slice* et les index
des emplacements spécifiques. Ceci clarifiera ce que la fonction `Config:: new`
fait parce que l'itérateur accédera aux valeurs.

<!--
Once `Config::new` takes ownership of the iterator and stops using indexing
operations that borrow, we can move the `String` values from the iterator into
`Config` rather than calling `clone` and making a new allocation.
-->

Une fois que `Config::new` prend possession de l'itérateur et cesse d'utiliser
les opérations d'indexation qui empruntent, nous pouvons déplacer les valeurs
`String` de l'iterator dans `Config` plutôt que d'appeler `clone` et de faire
une nouvelle allocation.

<!--
#### Using the Returned Iterator Directly
-->

### Utilisation directe de l'Itérateur renvoyé

<!--
Open your I/O project’s *src/main.rs* file, which should look like this:
-->

Ouvrez le fichier *src/main.rs* de votre projet d'I/O, qui devrait ressembler à
ceci:

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Filename: src/main.rs</span>

<!--
```rust,ignore
fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::new(&args).unwrap_or_else(|err| {
        eprintln!("Problem parsing arguments: {}", err);
        process::exit(1);
    });

    // --snip--
}
```
-->

```rust,ignore
fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::new(&args).unwrap_or_else(|err| {
        eprintln!("Problem parsing arguments: {}", err);
        process::exit(1);
    });

    // --snip--
}
```

<!--
We’ll change the start of the `main` function that we had in Listing 12-24 to
the code in Listing 13-25. This won’t compile until we update `Config::new` as
well.
-->

Nous allons changer le début de la fonction `main` que nous avions dans le
Listing 12-24 à la fin du Chapitre 12 pour le code dans le Listing 13-25. Ceci
ne compilera pas encore jusqu'à ce que nous mettions également à jour
`Config::new`:

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Filename: src/main.rs</span>

<!--
```rust,ignore
fn main() {
    let config = Config::new(env::args()).unwrap_or_else(|err| {
        eprintln!("Problem parsing arguments: {}", err);
        process::exit(1);
    });

    // --snip--
}
```
-->

```rust,ignore
fn main() {
    let config = Config::new(env::args()).unwrap_or_else(|err| {
        eprintln!("Problem parsing arguments: {}", err);
        process::exit(1);
    });

    // --snip--
}
```

<!--
<span class="caption">Listing 13-25: Passing the return value of `env::args` to
`Config::new`</span>
-->

<span class="caption">Listing 13-25: Transmission de la valeur de retour de
`env::args` à `Config::new`.</span>

<!--
The `env::args` function returns an iterator! Rather than collecting the
iterator values into a vector and then passing a slice to `Config::new`, now
we’re passing ownership of the iterator returned from `env::args` to
`Config::new` directly.
-->

La fonction `env::args` retourne un itérateur! Plutôt que de collecter les
valeurs de l'itérateur dans un vecteur et de passer ensuite une *slice* à
`Config::new`, maintenant nous passons la propriété de l'itérateur directement
de `env::args` à `Config::new`.

<!--
Next, we need to update the definition of `Config::new`. In your I/O project’s
*src/lib.rs* file, let’s change the signature of `Config::new` to look like
Listing 13-26. This still won’t compile because we need to update the function
body.
-->

Ensuite, nous devons mettre à jour la définition de `Config::new`. Dans le
fichier *src/lib.rs* de votre projet d'I/O, modifions la signature de
`Config::new` pour ressembler au Listing 13-26. Ceci ne compilera pas encore
parce que nous devons mettre à jour le corps de la fonction:

<!--
<span class="filename">Filename: src/lib.rs</span>
-->

<span class="filename">Filename: src/lib.rs</span>

<!--
```rust,ignore
impl Config {
    pub fn new(mut args: std::env::Args) -> Result<Config, &'static str> {
        // --snip--
```
-->

```rust,ignore
impl Config {
    pub fn new(mut args: std::env::Args) -> Result<Config, &'static str> {
        // --snip--
```

<!--
<span class="caption">Listing 13-26: Updating the signature of `Config::new` to
expect an iterator</span>
-->

<span class="caption">Listing 13-26:Mise à jour de la signature de
`Config::new` pour recevoir un itérateur</span>

<!--
The standard library documentation for the `env::args` function shows that the
type of the iterator it returns is `std::env::Args`. We’ve updated the
signature of the `Config::new` function so the parameter `args` has the type
`std::env::Args` instead of `&[String]`. Because we’re taking ownership of
`args` and we’ll be mutating `args` by iterating over it, we can add the `mut`
keyword into the specification of the `args` parameter to make it mutable.
-->

La documentation standard de la bibliothèque pour la fonction `env::args`
montre que le type de l'itérateur qu'elle renvoie est `std::env::Args`. Nous
avons mis à jour la signature de la fonction `Config::new` pour que le
paramètre `args` ait le type `std::env::Args` au lieu de `&[String]`. Etant
donné que nous prenons la propriété de `args` et que nous allons muter `args`
en itérant dessus, nous pouvons ajouter le mot-clé `mut` dans la spécification
du paramètre `args` pour le rendre mutable.

<!--
#### Using `Iterator` Trait Methods Instead of Indexing
-->

#### Utilisation des Méthodes du Trait `Iterator` au lieu de l'Indexation

<!--
Next, we’ll fix the body of `Config::new`. The standard library documentation
also mentions that `std::env::Args` implements the `Iterator` trait, so we know
we can call the `next` method on it! Listing 13-27 updates the code from
Listing 12-23 to use the `next` method:
-->

Ensuite, nous allons réparer le corps de `Config::new`. La documentation
standard de la bibliothèque mentionne aussi que `std::env::Args` implémente le
trait `Iterator`, donc nous savons que nous pouvons appeler la méthode `next`
dessus! Le listing 13-27 met à jour le code du Listing 12-23 afin d'utiliser la
méthode `next`:

<!--
<span class="filename">Filename: src/lib.rs</span>
-->

<span class="filename">Fichier: src/lib.rs</span>

<!--
```rust
# fn main() {}
# use std::env;
#
# struct Config {
#     query: String,
#     filename: String,
#     case_sensitive: bool,
# }
#
impl Config {
    pub fn new(mut args: std::env::Args) -> Result<Config, &'static str> {
        args.next();

        let query = match args.next() {
            Some(arg) => arg,
            None => return Err("Didn't get a query string"),
        };

        let filename = match args.next() {
            Some(arg) => arg,
            None => return Err("Didn't get a file name"),
        };

        let case_sensitive = env::var("CASE_INSENSITIVE").is_err();

        Ok(Config { query, filename, case_sensitive })
    }
}
```
-->

```rust
# fn main() {}
# use std::env;
#
# struct Config {
#     query: String,
#     filename: String,
#     case_sensitive: bool,
# }
#
impl Config {
    pub fn new(mut args: std::env::Args) -> Result<Config, &'static str> {
        args.next();

        let query = match args.next() {
            Some(arg) => arg,
            None => return Err("Didn't get a query string"),
        };

        let filename = match args.next() {
            Some(arg) => arg,
            None => return Err("Didn't get a file name"),
        };

        let case_sensitive = env::var("CASE_INSENSITIVE").is_err();

        Ok(Config { query, filename, case_sensitive })
    }
}
```

<!--
<span class="caption">Listing 13-27: Changing the body of `Config::new` to use
iterator methods</span>
-->

<span class="caption">Listing 13-27: Changement du corps de `Config::new` afin
d'utiliser les méthodes d'itération</span>

<!--
Remember that the first value in the return value of `env::args` is the name of
the program. We want to ignore that and get to the next value, so first we call
`next` and do nothing with the return value. Second, we call `next` to get the
value we want to put in the `query` field of `Config`. If `next` returns a
`Some`, we use a `match` to extract the value. If it returns `None`, it means
not enough arguments were given and we return early with an `Err` value. We do
the same thing for the `filename` value.
-->

Rappelez-vous que la première valeur dans ce qui est retourné par `env::args`
est le nom du programme. Nous voulons ignorer cette valeur et passer à la
valeur suivante, donc d'abord nous appelons `next` et nous ne faisons rien avec
la valeur de retour. Deuxièmement, nous appelons `next` sur la valeur que nous
voulons mettre dans le champ `query` de `Config`. Si `next` renvoie un `Some`,
nous utilisons un `match` pour extraire la valeur. S'il retourne `None`, cela
signifie qu'il n'y a pas assez d'arguments donnés et nous revenons plus tôt
avec une valeur `Err`. De même pour la valeur `filename`.

<!--
### Making Code Clearer with Iterator Adaptors
-->

### Rendre le Code plus Clair avec des Adaptateurs d'Itérateurs

<!--
We can also take advantage of iterators in the `search` function in our I/O
project, which is reproduced here in Listing 13-28 as it was in Listing 12-19:
-->

Nous pouvons également tirer parti des itérateurs dans la fonction `search` de
notre projet d'I/O, qui est reproduite ici dans le Listing 13-28, comme elle
l'était dans Listing 12-19 à la fin du chapitre 12:

<!--
<span class="filename">Filename: src/lib.rs</span>
-->

<span class="filename">Filename: src/lib.rs</span>

<!--
```rust,ignore
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    let mut results = Vec::new();

    for line in contents.lines() {
        if line.contains(query) {
            results.push(line);
        }
    }

    results
}
```
-->

```rust,ignore
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    let mut results = Vec::new();

    for line in contents.lines() {
        if line.contains(query) {
            results.push(line);
        }
    }

    results
}
```

<!--
<span class="caption">Listing 13-28: The implementation of the `search`
function from Listing 12-19</span>
-->

<span class="caption">Listing 13-28: La mise en oeuvre de la fonction `search`
telle qu'au chapitre 12</span>

<!--
We can write this code in a more concise way using iterator adaptor methods.
Doing so also lets us avoid having a mutable intermediate `results` vector. The
functional programming style prefers to minimize the amount of mutable state to
make code clearer. Removing the mutable state might enable a future enhancement
to make searching happen in parallel, because we wouldn’t have to manage
concurrent access to the `results` vector. Listing 13-29 shows this change:
-->

Nous pouvons écrire ce code de façon plus concise en utilisant des méthodes
d'adaptateur d'itérateur. Ce faisant, nous évitons aussi d'avoir un vecteur
intermédiaire mutable: `results`. Le style de programmation fonctionnelle
préfère minimiser la quantité d'état modifiable pour rendre le code plus clair.
Supprimer l'état mutable pourrait nous aider à faire une amélioration future
pour que la recherche se fasse en parallèle, car nous n'aurions pas à gérer
l'accès simultané au vecteur `results`. Le Listing 13-29 montre ce changement:

<!--
<span class="filename">Filename: src/lib.rs</span>
-->

<span class="filename">Filename: src/lib.rs</span>

<!--
```rust,ignore
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    contents.lines()
        .filter(|line| line.contains(query))
        .collect()
}
```
-->

```rust,ignore
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    contents.lines()
        .filter(|line| line.contains(query))
        .collect()
}
```

<!--
<span class="caption">Listing 13-29: Using iterator adaptor methods in the
implementation of the `search` function</span>
-->

<span class="caption">Listing 13-29: Utilisation de méthodes d'adaptateurs
d'itérateurs dans l'implémentation de la fonction `search`</span>

<!--
Recall that the purpose of the `search` function is to return all lines in
`contents` that contain the `query`. Similar to the `filter` example in Listing
13-19, this code uses the `filter` adaptor to keep only the lines that
`line.contains(query)` returns `true` for. We then collect the matching lines
into another vector with `collect`. Much simpler! Feel free to make the same
change to use iterator methods in the `search_case_insensitive` function as
well.
-->

Souvenez-vous que le but de la fonction `search` est de renvoyer toutes les
lignes dans `contents` qui contiennent `query`. Comme dans l'exemple de
`filter` dans le Listing 13-19, nous pouvons utiliser l'adaptateur `filter`
pour ne garder que les lignes pour lesquelles `line.contains(query)` renvoie
true. Nous collectons ensuite les lignes correspondantes dans un autre vecteur
avec `collect`. Bien plus simple ! N'hésitez pas à faire le même changement
pour utiliser les méthodes d'itération dans la fonction
`search_case_insensitive`.

<!--
The next logical question is which style you should choose in your own code and
why: the original implementation in Listing 13-28 or the version using
iterators in Listing 13-29. Most Rust programmers prefer to use the iterator
style. It’s a bit tougher to get the hang of at first, but once you get a feel
for the various iterator adaptors and what they do, iterators can be easier to
understand. Instead of fiddling with the various bits of looping and building
new vectors, the code focuses on the high-level objective of the loop. This
abstracts away some of the commonplace code so it’s easier to see the concepts
that are unique to this code, such as the filtering condition each element in
the iterator must pass.
-->

Logiquement la question suivante est de savoir quel style utiliser dans votre
propre code et pourquoi: l'implémentation originale dans le Listing 13-28 ou la
version utilisant les itérateurs dans le Listing 13-29. La plupart des
développeurs Rust préfèrent utiliser le style itérateur. C'est un peu plus
difficile au début, mais une fois que vous avez une idée des différents
adaptateurs d'itérateur et de ce qu'ils font, les itérateurs peuvent être plus
faciles à comprendre. Au lieu de jouer avec les différentes boucles et de
construire de nouveaux vecteurs, le code se concentre sur l'objectif de haut
niveau de la boucle. Cette abstraction élimine une partie du code trivial, de
sorte qu'il soit plus facile de voir les concepts qui sont uniques à ce code,
comme la condition de filtration que chaque élément de l'itérateur doit passer.

<!--
But are the two implementations truly equivalent? The intuitive assumption
might be that the more low-level loop will be faster. Let’s talk about
performance.
-->

Mais les deux implémentations sont-elles réellement équivalentes? L'hypothèse
intuitive pourrait être que la boucle de plus bas niveau sera plus rapide.
Intéressons nous aux performances.
