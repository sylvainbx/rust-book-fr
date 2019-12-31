<!-- TODO : bonpatron.fr -->

<!--
## Processing a Series of Items with Iterators
-->

## Parcourir une séquence d'objets avec des Itérateurs

<!--
The iterator pattern allows you to perform some task on a sequence of items in
turn. An iterator is responsible for the logic of iterating over each item and
determining when the sequence has finished. When you use iterators, you don’t
have to reimplement that logic yourself.
-->

Le pattern itérateur vous permet d'effectuer une tâche sur une séquence
d'éléments à tour de rôle. Un *itérateur* est responsable de la logique
d'itération sur chaque élément et de déterminer lorsque la séquence est
terminée. Lorsque nous utilisons des itérateurs, nous n'avons pas besoin de ré
implémenter cette logique nous-mêmes.

<!--
In Rust, iterators are *lazy*, meaning they have no effect until you call
methods that consume the iterator to use it up. For example, the code in
Listing 13-13 creates an iterator over the items in the vector `v1` by calling
the `iter` method defined on `Vec<T>`. This code by itself doesn’t do anything
useful.
-->

En Rust, les itérateurs sont *lazy*, ce qui signifie qu'ils n'ont aucun effet
jusqu'à ce que nous appelions les méthodes qui consomment l'itérateur pour
l'utiliser. Par exemple, le code dans le Listing 13-13 crée un itérateur sur les
objets dans le vecteur `v1` en appelant la méthode `iter` définie sur `Vec`. Ce
code en lui-même ne fait rien d'utile:

<!--
```rust
let v1 = vec![1, 2, 3];

let v1_iter = v1.iter();
```
-->

```rust
let v1 = vec![1, 2, 3];

let v1_iter = v1.iter();
```

<!--
<span class="caption">Listing 13-13: Creating an iterator</span>
-->

<span class="caption">Listing 13-13: Création d'un itérateur</span>

<!--
Once we’ve created an iterator, we can use it in a variety of ways. In Listing
3-5 in Chapter 3, we used iterators with `for` loops to execute some code on
each item, although we glossed over what the call to `iter` did until now.
-->

Une fois que nous avons créé un itérateur, nous pouvons l'utiliser de diverses
manières. Dans le Listing 3-4 du chapitre 3, nous avons utilisé des itérateurs
avec des boucles `for` pour exécuter du code sur chaque élément, bien que nous
ayons laissé de côté ce que l'appel à `iter` faisait jusqu'à présent.

<!--
The example in Listing 13-14 separates the creation of the iterator from the
use of the iterator in the `for` loop. The iterator is stored in the `v1_iter`
variable, and no iteration takes place at that time. When the `for` loop is
called using the iterator in `v1_iter`, each element in the iterator is used in
one iteration of the loop, which prints out each value.
-->

L'exemple dans le Listing 13-14 sépare la création de l'itérateur de son
utilisation dans la boucle `for`. L'itérateur est stocké dans la variable
`v1_iter`, et aucune itération n'a lieu à ce moment. Lorsque la boucle `for` est
appelée en utilisant l'itérateur `v1_iter`, chaque élément de l'itérateur est
utilisé dans une itération de la boucle, qui imprime chaque valeur:

<!--
```rust
let v1 = vec![1, 2, 3];

let v1_iter = v1.iter();

for val in v1_iter {
    println!("Got: {}", val);
}
```
-->

```rust
let v1 = vec![1, 2, 3];

let v1_iter = v1.iter();

for val in v1_iter {
    println!("Got: {}", val);
}
```

<!--
<span class="caption">Listing 13-14: Using an iterator in a `for` loop</span>
-->

<span class="caption">Listing 13-14: Utilisation d'un itérateur dans une boucle
`for`</span>

<!--
In languages that don’t have iterators provided by their standard libraries,
you would likely write this same functionality by starting a variable at index
0, using that variable to index into the vector to get a value, and
incrementing the variable value in a loop until it reached the total number of
items in the vector.
-->

Dans les langages qui n'ont pas d'itérateurs fournis par leurs bibliothèques
standard, nous écririons probablement cette même fonctionnalité en démarrant une
variable à l'index 0, en utilisant cette variable comme index sur le vecteur
afin d'obtenir une valeur, en incrémentant la valeur de cette variable dans une
boucle jusqu'à ce qu'elle atteigne le nombre total d'éléments dans le vecteur.

<!--
Iterators handle all that logic for you, cutting down on repetitive code you
could potentially mess up. Iterators give you more flexibility to use the same
logic with many different kinds of sequences, not just data structures you can
index into, like vectors. Let’s examine how iterators do that.
-->

Les itérateurs s'occupent de toute cette logique pour nous, réduisant le code
redondant que nous pourrions potentiellement gâcher. Les itérateurs nous donnent
plus de flexibilité pour utiliser la même logique avec de nombreux types de
séquences différentes et pas uniquement avec des structures de données que nous
pouvons indexer, comme des vecteurs. Voyons comment les itérateurs font cela.

<!--
### The `Iterator` Trait and the `next` Method
-->

### Le Trait `Iterator` et la Méthode `next`

<!--
All iterators implement a trait named `Iterator` that is defined in the
standard library. The definition of the trait looks like this:
-->

Tous les itérateurs implémentent un trait appelé `Iterator` qui est défini dans
la bibliothèque standard. La définition du trait ressemble à ceci :

<!--
```rust
pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // methods with default implementations elided
}
```
-->

```rust
pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // methods with default implementations elided
}
```

<!--
Notice this definition uses some new syntax: `type Item` and `Self::Item`,
which are defining an *associated type* with this trait. We’ll talk about
associated types in depth in Chapter 19. For now, all you need to know is that
this code says implementing the `Iterator` trait requires that you also define
an `Item` type, and this `Item` type is used in the return type of the `next`
method. In other words, the `Item` type will be the type returned from the
iterator.
-->

Notez de nouvelles syntaxes que nous n'avons pas encore couvertes : `type Item`
et `Self::Item`, qui définissent un *type associé* à ce trait. Nous parlerons en
profondeur des types associés au chapitre 19. Pour l'instant, tout ce que vous
devez savoir est que ce code dit que l'implémentation du caractère `Iterator`
nécessite que vous définissiez aussi un type `Item`, et ce type `Item` est
utilisé dans le type de retour de la méthode `next`. En d'autres termes, le type
`Item` sera le type retourné par l'itérateur.

<!--
The `Iterator` trait only requires implementors to define one method: the
`next` method, which returns one item of the iterator at a time wrapped in
`Some` and, when iteration is over, returns `None`.
-->

Le trait `Iterator` n'exige que la définition d'une méthode de la part des
implémenteurs: la méthode `next`, qui retourne un élément de l'itérateur à la
fois enveloppé dans `Some` et, quand l'itération est terminée, il retourne
`None`.

<!--
We can call the `next` method on iterators directly; Listing 13-15 demonstrates
what values are returned from repeated calls to `next` on the iterator created
from the vector.
-->

On peut appeler la méthode `next` directement sur les itérateurs; le Listing
13-15 montre quelles valeurs sont retournées par des appels répétés à `next` sur
l'itérateur créé à partir du vecteur :

<!--
<span class="filename">Fichier : src/lib.rs</span>
-->

<span class="filename">Fichier : src/lib.rs</span>

<!--
```rust
#[test]
fn iterator_demonstration() {
    let v1 = vec![1, 2, 3];

    let mut v1_iter = v1.iter();

    assert_eq!(v1_iter.next(), Some(&1));
    assert_eq!(v1_iter.next(), Some(&2));
    assert_eq!(v1_iter.next(), Some(&3));
    assert_eq!(v1_iter.next(), None);
}
```
-->

```rust
#[test]
fn iterator_demonstration() {
    let v1 = vec![1, 2, 3];

    let mut v1_iter = v1.iter();

    assert_eq!(v1_iter.next(), Some(&1));
    assert_eq!(v1_iter.next(), Some(&2));
    assert_eq!(v1_iter.next(), Some(&3));
    assert_eq!(v1_iter.next(), None);
}
```

<!--
<span class="caption">Listing 13-15: Calling the `next` method on an
iterator</span>
-->

<span class="caption">Listing 13-15: Appel de la méthode `next` sur un
itérateur</span>

<!--
Note that we needed to make `v1_iter` mutable: calling the `next` method on an
iterator changes internal state that the iterator uses to keep track of where
it is in the sequence. In other words, this code *consumes*, or uses up, the
iterator. Each call to `next` eats up an item from the iterator. We didn’t need
to make `v1_iter` mutable when we used a `for` loop because the loop took
ownership of `v1_iter` and made it mutable behind the scenes.
-->

Notez que nous avons besoin de rendre mutable `v1_iter` : appeler la méthode
`next` sur un iterator change l'état qui garde en mémoire où il est dans la
séquence. En d'autres termes, ce code *consomme*, ou utilise, l'itérateur.
Chaque appel à `next` consomme un objet de l'itérateur. Nous n'avons pas eu
besoin de rendre mutable `v1_iter` quand nous avons utilisé une boucle `for`
parce que la boucle a pris possession de `v1_iter` et l'a rendu mutable en
coulisse.

<!--
Also note that the values we get from the calls to `next` are immutable
references to the values in the vector. The `iter` method produces an iterator
over immutable references. If we want to create an iterator that takes
ownership of `v1` and returns owned values, we can call `into_iter` instead of
`iter`. Similarly, if we want to iterate over mutable references, we can call
`iter_mut` instead of `iter`.
-->

Notez également que les valeurs que nous obtenons des appels à `next` sont des
références immuables aux valeurs dans le vecteur. La méthode `iter` produit un
itérateur sur des références immuables. Si nous voulons créer un itérateur qui
prend la propriété de `v1` et retourne les valeurs possédées, nous pouvons
appeler `into_iter` au lieu de `iter`. De même, si nous voulons itérer sur des
références mutables, nous pouvons appeler `iter_mut` au lieu de `iter`.

<!--
### Methods that Consume the Iterator
-->

### Méthodes qui Consomment un Itérateur

<!--
The `Iterator` trait has a number of different methods with default
implementations provided by the standard library; you can find out about these
methods by looking in the standard library API documentation for the `Iterator`
trait. Some of these methods call the `next` method in their definition, which
is why you’re required to implement the `next` method when implementing the
`Iterator` trait.
-->

Le trait `Iterator` a un certain nombre de méthodes différentes avec des
implémentations par défaut fournit pour nous par la bibliothèque standard ; vous
pouvez découvrir ces méthodes en regardant dans la documentation de l'API de la
bibliothèque standard pour le trait `Iterator`. Certaines de ces méthodes
appellent la méthode `next` dans leur définition, c'est pourquoi nous devons
implémenter la méthode `next` lors de l'implémentation du trait `Iterator`.

<!--
Methods that call `next` are called *consuming adaptors*, because calling them
uses up the iterator. One example is the `sum` method, which takes ownership of
the iterator and iterates through the items by repeatedly calling `next`, thus
consuming the iterator. As it iterates through, it adds each item to a running
total and returns the total when iteration is complete. Listing 13-16 has a
test illustrating a use of the `sum` method:
-->

Les méthodes qui appellent `next` sont appelées des *adaptateurs consommateurs*
(ou en anglais *consuming adaptors*), parce que les appeler consomme
l'itérateur. Un exemple est la méthode `sum`, qui prend la propriété de
l'itérateur et itére à travers les éléments en appelant plusieurs fois `next`,
consommant ainsi l'itérateur. A chaque étape de l'itération, il ajoute chaque
élément à un total courant et retourne le total une fois l'itération terminée.
Le Listing 13-16 a un test illustrant une utilisation de la méthode `sum`:

<!--
<span class="filename">Fichier : src/lib.rs</span>
-->

<span class="filename">Fichier : src/lib.rs</span>

<!--
```rust
#[test]
fn iterator_sum() {
    let v1 = vec![1, 2, 3];

    let v1_iter = v1.iter();

    let total: i32 = v1_iter.sum();

    assert_eq!(total, 6);
}
```
-->

```rust
#[test]
fn iterator_sum() {
    let v1 = vec![1, 2, 3];

    let v1_iter = v1.iter();

    let total: i32 = v1_iter.sum();

    assert_eq!(total, 6);
}
```

<!--
<span class="caption">Listing 13-16: Calling the `sum` method to get the total
of all items in the iterator</span>
-->

<span class="caption">Listing 13-16: Appel de la méthode `sum` pour obtenir la
somme de tous les objets dans l'itérateur</span>

<!--
We aren’t allowed to use `v1_iter` after the call to `sum` because `sum` takes
ownership of the iterator we call it on.
-->

Nous ne sommes pas autorisés à utiliser `v1_iter` après l'appel à `sum` parce
que `sum` a pris possession de l'itérateur sur lequel nous l'appelons.

<!--
### Methods that Produce Other Iterators
-->

### Méthodes produisants d'Autres Itérateurs

<!--
Other methods defined on the `Iterator` trait, known as *iterator adaptors*,
allow you to change iterators into different kinds of iterators. You can chain
multiple calls to iterator adaptors to perform complex actions in a readable
way. But because all iterators are lazy, you have to call one of the consuming
adaptor methods to get results from calls to iterator adaptors.
-->

D'autres méthodes définies sur le trait `Iterator`, connues sous le nom
d'*adaptateurs d'itérateurs*, nous permettant de changer un itérateur en un type
d'itérateur différent. Nous pouvons enchaîner plusieurs appels à des adaptateurs
d'itérateurs pour effectuer des actions complexes de manière lisible. Mais parce
que tous les itérateurs sont *lazy*, nous devons faire appel à l'une des
méthodes d'adaptateur de consommation pour obtenir les résultats des appels aux
adaptateurs d'itérateurs.

<!--
Listing 13-17 shows an example of calling the iterator adaptor method `map`,
which takes a closure to call on each item to produce a new iterator. The
closure here creates a new iterator in which each item from the vector has been
incremented by 1. However, this code produces a warning:
-->

Le Listing 13-17 montre un exemple d'appel de la méthode d'adaptateur
d'itérateur: `map`, qui prend une closure pour appeler sur chaque élément pour
produire un nouvel itérateur. La closure crée ici un nouvel itérateur dans
lequel chaque élément du vecteur a été incrémenté de 1. Cependant, ce code
produit un avertissement :

<!--
<span class="filename">Fichier : src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust,not_desired_behavior
let v1: Vec<i32> = vec![1, 2, 3];

v1.iter().map(|x| x + 1);
```
-->

```rust,not_desired_behavior
let v1: Vec<i32> = vec![1, 2, 3];

v1.iter().map(|x| x + 1);
```

<!--
<span class="caption">Listing 13-17: Calling the iterator adaptor `map` to
create a new iterator</span>
-->

<span class="caption">Listing 13-17: Appel de l'adaptateur d'itérateur `map`
pour créer un nouvel itérateur</span>

<!--
The warning we get is this:
-->

L'avertissement que nous obtenons:

<!--
```text
warning: unused `std::iter::Map` which must be used: iterator adaptors are lazy
and do nothing unless consumed
 -- > src/main.rs:4:5
  |
4 |     v1.iter().map(|x| x + 1);
  |     ^^^^^^^^^^^^^^^^^^^^^^^^^
  |
  = note: #[warn(unused_must_use)] on by default
```
-->

```text
warning: unused `std::iter::Map` which must be used: iterator adaptors are lazy
and do nothing unless consumed
 -- > src/main.rs:4:5
  |
4 |     v1.iter().map(|x| x + 1);
  |     ^^^^^^^^^^^^^^^^^^^^^^^^^
  |
  = note: #[warn(unused_must_use)] on by default
```

<!--
The code in Listing 13-17 doesn’t do anything; the closure we’ve specified
never gets called. The warning reminds us why: iterator adaptors are lazy, and
we need to consume the iterator here.
-->

Le code dans le Listing 13-17 ne fait rien; la closureque nous avons spécifiée
n'est jamais appelée. L'avertissement nous rappelle pourquoi: les adaptateurs
d'itérateur sont *lazy*, et nous devons consommer l'itérateur ici.

<!--
To fix this and consume the iterator, we’ll use the `collect` method, which we
used in Chapter 12 with `env::args` in Listing 12-1. This method consumes the
iterator and collects the resulting values into a collection data type.
-->

Pour corriger ceci et consommer l'itérateur, nous utiliserons la méthode
`collect`, que vous avez brièvement vue au chapitre 12. Cette méthode consomme
l'itérateur et collecte les valeurs résultantes dans une collection.

<!--
In Listing 13-18, we collect the results of iterating over the iterator that’s
returned from the call to `map` into a vector. This vector will end up
containing each item from the original vector incremented by 1.
-->

Dans le Listing 13-18, nous recueillons les résultats de l'itération sur
l'itérateur qui est retourné par l'appel à `map` dans un vecteur. Ce vecteur
finira par contenir chaque élément du vecteur original incrémenté d'une unité:

<!--
<span class="filename">Fichier : src/main.rs</span>
-->

<span class="filename">Fichier : src/main.rs</span>

<!--
```rust
let v1: Vec<i32> = vec![1, 2, 3];

let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();

assert_eq!(v2, vec![2, 3, 4]);
```
-->

```rust
let v1: Vec<i32> = vec![1, 2, 3];

let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();

assert_eq!(v2, vec![2, 3, 4]);
```

<!--
<span class="caption">Listing 13-18: Calling the `map` method to create a new
iterator and then calling the `collect` method to consume the new iterator and
create a vector</span>
-->

<span class="caption">Listing 13-18: Appel de la méthode `map` pour créer un
nouvel itérateur, puis appel de la méthode `collect` pour consommer le nouvel
itérateur et créer un vecteur</span>

<!--
Because `map` takes a closure, we can specify any operation we want to perform
on each item. This is a great example of how closures let you customize some
behavior while reusing the iteration behavior that the `Iterator` trait
provides.
-->

Puisque `map` prend une closure, nous pouvons spécifier n'importe quelle
opération que nous voulons exécuter sur chaque élément. C'est un bon exemple de
la façon dont les fermetures permettent de personnaliser certains comportements
tout en réutilisant le comportement d'itération fourni par le trait `Iterator`.

<!--
### Using Closures that Capture Their Environment
-->

### Utilisation de Closures Capturant Leur Environnement

<!--
Now that we’ve introduced iterators, we can demonstrate a common use of
closures that capture their environment by using the `filter` iterator adaptor.
The `filter` method on an iterator takes a closure that takes each item from
the iterator and returns a Boolean. If the closure returns `true`, the value
will be included in the iterator produced by `filter`. If the closure returns
`false`, the value won’t be included in the resulting iterator.
-->

Maintenant que nous avons introduit les itérateurs, nous pouvons démontrer une
utilisation commune des closures qui capturent leur environnement en utilisant
l'adaptateur d'itérateur `filter`. La méthode `filter` appelée sur un itérateur
prend une closure qui prend chaque élément de l'itérateur et retourne un
bouléen. Si la closure retourne `true`, la valeur sera incluse dans l'itérateur
produit par `filter`. Si la closure retourne `false`, la valeur ne sera pas
incluse dans l'itérateur résultant.

<!--
In Listing 13-19, we use `filter` with a closure that captures the `shoe_size`
variable from its environment to iterate over a collection of `Shoe` struct
instances. It will return only shoes that are the specified size.
-->

Dans le Listing 13-19 nous utilisons `filter` avec une closure qui capture la
variable `shoe_size` de son environnement pour itérer sur une collection
d'instances de structure `Shoe`. Il ne retournera que les chaussures avec la
pointure spécifiée :

<!--
<span class="filename">Fichier : src/lib.rs</span>
-->

<span class="filename">Fichier : src/lib.rs</span>

<!--
```rust
#[derive(PartialEq, Debug)]
struct Shoe {
    size: u32,
    style: String,
}

fn shoes_in_my_size(shoes: Vec<Shoe>, shoe_size: u32) -> Vec<Shoe> {
    shoes.into_iter()
        .filter(|s| s.size == shoe_size)
        .collect()
}

#[test]
fn filters_by_size() {
    let shoes = vec![
        Shoe { size: 10, style: String::from("sneaker") },
        Shoe { size: 13, style: String::from("sandal") },
        Shoe { size: 10, style: String::from("boot") },
    ];

    let in_my_size = shoes_in_my_size(shoes, 10);

    assert_eq!(
        in_my_size,
        vec![
            Shoe { size: 10, style: String::from("sneaker") },
            Shoe { size: 10, style: String::from("boot") },
        ]
    );
}
```
-->

```rust
#[derive(PartialEq, Debug)]
struct Shoe {
    size: u32,
    style: String,
}

fn shoes_in_my_size(shoes: Vec<Shoe>, shoe_size: u32) -> Vec<Shoe> {
    shoes.into_iter()
        .filter(|s| s.size == shoe_size)
        .collect()
}

#[test]
fn filters_by_size() {
    let shoes = vec![
        Shoe { size: 10, style: String::from("sneaker") },
        Shoe { size: 13, style: String::from("sandal") },
        Shoe { size: 10, style: String::from("boot") },
    ];

    let in_my_size = shoes_in_my_size(shoes, 10);

    assert_eq!(
        in_my_size,
        vec![
            Shoe { size: 10, style: String::from("sneaker") },
            Shoe { size: 10, style: String::from("boot") },
        ]
    );
}
```

<!--
<span class="caption">Listing 13-19: Using the `filter` method with a closure
that captures `shoe_size`</span>
-->

<span class="caption">Listing 13-19: Utilisation de la méthode `filter` avec une
closure capturant `shoe_size`</span>

<!--
The `shoes_in_my_size` function takes ownership of a vector of shoes and a shoe
size as parameters. It returns a vector containing only shoes of the specified
size.
-->

La fonction `shoes_in_my_size` prend la propriété d'un vecteur de Shoe et d'une
pointure comme paramètres. Il renvoie un vecteur contenant uniquement des Shoe
de la pointure spécifiée.

<!--
In the body of `shoes_in_my_size`, we call `into_iter` to create an iterator
that takes ownership of the vector. Then we call `filter` to adapt that
iterator into a new iterator that only contains elements for which the closure
returns `true`.
-->


Dans le corps de `shoes_in_my_size`, nous appelons `into_iter` pour créer un
itérateur qui prend possession du vecteur. Ensuite, nous appelons `filter` pour
adapter cet itérateur dans un nouvel itérateur qui ne contient que les éléments
pour lesquels la closure retourne `true`.

<!--
The closure captures the `shoe_size` parameter from the environment and
compares the value with each shoe’s size, keeping only shoes of the size
specified. Finally, calling `collect` gathers the values returned by the
adapted iterator into a vector that’s returned by the function.
-->

La closure capture le paramètre `shoe_size` de l'environnement et compare la
valeur avec la pointure de chaque chaussure, en ne gardant que les chaussures de
la pointure spécifiée. Enfin, l'appel à `collect` regroupe les valeurs renvoyées
par l'itérateur adapté en un vecteur retourné par la fonction.

<!--
The test shows that when we call `shoes_in_my_size`, we get back only shoes
that have the same size as the value we specified.
-->

Le test montre que lorsque nous appelons `shoes_in_my_size`, nous n'obtenons que
des chaussures qui ont la même pointure que la valeur que nous avons spécifiée.

<!--
### Creating Our Own Iterators with the `Iterator` Trait
-->

### Création de nos propres Itérateurs avec `Iterator`

<!--
We’ve shown that you can create an iterator by calling `iter`, `into_iter`, or
`iter_mut` on a vector. You can create iterators from the other collection
types in the standard library, such as hash map. You can also create iterators
that do anything you want by implementing the `Iterator` trait on your own
types. As previously mentioned, the only method you’re required to provide a
definition for is the `next` method. Once you’ve done that, you can use all
other methods that have default implementations provided by the `Iterator`
trait!
-->

Nous avons montré que nous pouvons créer un itérateur en appelant `iter`,
`into_iter`, ou `iter_mut` sur un vecteur. Nous pouvons créer des itérateurs à
partir d'autres types de collection dans la bibliothèque standard, tels que des
hashmap. Nous pouvons aussi créer des itérateurs qui font tout ce que nous
voulons en implémentant le trait `Iterator` sur nos propres types. Comme nous
l'avons mentionné précédemment, la seule méthode pour laquelle nous devons
fournir une définition est la méthode `next`. Une fois que nous avons fait cela,
nous pouvons utiliser toutes les autres méthodes qui ont des implémentations par
défaut fournies par le trait `Iterator` !

<!--
To demonstrate, let’s create an iterator that will only ever count from 1 to 5.
First, we’ll create a struct to hold some values. Then we’ll make this struct
into an iterator by implementing the `Iterator` trait and using the values in
that implementation.
-->

Pour preuve, créons un itérateur qui ne comptera que de 1 à 5. D'abord, nous
allons créer une structure contenant quelques valeurs et ensuite nous ferons de
cette structure un itérateur en implémentant le trait `Iterator` et nous
utiliserons les valeurs de cette implémentation.

<!--
Listing 13-20 has the definition of the `Counter` struct and an associated
`new` function to create instances of `Counter`:
-->

Le Listing 13-20 défini la structure `Counter` et une nouvelle fonction associée
pour créer des instances de `Counter`:

<!--
<span class="filename">Fichier : src/lib.rs</span>
-->

<span class="filename">Fichier : src/lib.rs</span>

<!--
```rust
struct Counter {
    count: u32,
}

impl Counter {
    fn new() -> Counter {
        Counter { count: 0 }
    }
}
```
-->

```rust
struct Counter {
    count: u32,
}

impl Counter {
    fn new() -> Counter {
        Counter { count: 0 }
    }
}
```

<!--
<span class="caption">Listing 13-20: Defining the `Counter` struct and a `new`
function that creates instances of `Counter` with an initial value of 0 for
`count`</span>
-->

<span class="caption">Listing 13-20: Définition de la structure `Counter` et
d'une nouvelle fonction qui crée des instances de `Counter` avec une valeur
initiale de 0 pour `count`.</span>

<!--
The `Counter` struct has one field named `count`. This field holds a `u32`
value that will keep track of where we are in the process of iterating from 1
to 5. The `count` field is private because we want the implementation of
`Counter` to manage its value. The `new` function enforces the behavior of
always starting new instances with a value of 0 in the `count` field.
-->

La structure `Counter` a un champ nommé `count`. Ce champ contient une valeur
`u32` qui gardera trace de l'endroit où nous sommes dans le processus
d'itération de 1 à 5. Le champ `count` est privé car nous voulons que
l'implémentation de `Counter` gère sa valeur. La fonction `new` impose le
comportement de toujours démarrer de nouvelles instances avec une valeur de 0
dans le champ `count`.

<!--
Next, we’ll implement the `Iterator` trait for our `Counter` type by defining
the body of the `next` method to specify what we want to happen when this
iterator is used, as shown in Listing 13-21:
-->

Ensuite, nous allons implémenter le trait `Iterator` pour notre type `Counter`
en définissant le corps de la méthode `next` pour spécifier ce que nous voulons
qu'il se passe quand cet itérateur est utilisé, comme montré dans Listing 13-21:

<!--
<span class="filename">Fichier : src/lib.rs</span>
-->

<span class="filename">Fichier : src/lib.rs</span>

<!--
```rust
# struct Counter {
#     count: u32,
# }
#
impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        self.count += 1;

        if self.count < 6 {
            Some(self.count)
        } else {
            None
        }
    }
}
```
-->

```rust
# struct Counter {
#     count: u32,
# }
#
impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        self.count += 1;

        if self.count < 6 {
            Some(self.count)
        } else {
            None
        }
    }
}
```

<!--
<span class="caption">Listing 13-21: Implementing the `Iterator` trait on our
`Counter` struct</span>
-->

<span class="caption">Listing 13-21: Implémentation du trait `Iterator` sur
notre structure `Counter`</span>

<!--
We set the associated `Item` type for our iterator to `u32`, meaning the
iterator will return `u32` values. Again, don’t worry about associated types
yet, we’ll cover them in Chapter 19.
-->

Nous avons défini le type `Item` associé pour notre itérateur à `u32`, ce qui
signifie que l'itérateur renverra les valeurs `u32`. Encore une fois, ne vous
inquiétez pas des types associés, nous les aborderons au chapitre 19.

<!--
We want our iterator to add 1 to the current state, so we initialized `count`
to 0 so it would return 1 first. If the value of `count` is less than 6, `next`
will return the current value wrapped in `Some`, but if `count` is 6 or higher,
our iterator will return `None`.
-->

Nous voulons que notre itérateur ajoute un à l'état courant, donc nous avons
initialisé `count` à 0 pour qu'il retourne 1 lors du premier appel à `next`. Si
la valeur de `count` est inférieure à 6, `next` renverra la valeur courante
enveloppée dans `Some`, mais si `count` vaut 6 ou plus, notre itérateur renverra
`None`.

<!--
#### Using Our `Counter` Iterator’s `next` Method
-->


### Utilisation de la méthode `next` de notre Itérateur `Counter`

<!--
Once we’ve implemented the `Iterator` trait, we have an iterator! Listing 13-22
shows a test demonstrating that we can use the iterator functionality of our
`Counter` struct by calling the `next` method on it directly, just as we did
with the iterator created from a vector in Listing 13-15.
-->

Une fois que nous avons implémenté le trait `Iterator`, nous avons un itérateur!
Le Listing 13-22 montre un test démontrant que nous pouvons utiliser la
fonctionnalité d'itération de notre structure `Counter` en appelant directement
la méthode `next`, comme nous l'avons fait avec l'itérateur créé à partir d'un
vecteur dans le Listing 13-15:

<!--
<span class="filename">Fichier : src/lib.rs</span>
-->

<span class="filename">Fichier : src/lib.rs</span>

<!--
```rust
# struct Counter {
#     count: u32,
# }
#
# impl Iterator for Counter {
#     type Item = u32;
#
#     fn next(&mut self) -> Option<Self::Item> {
#         self.count += 1;
#
#         if self.count < 6 {
#             Some(self.count)
#         } else {
#             None
#         }
#     }
# }
#
#[test]
fn calling_next_directly() {
    let mut counter = Counter::new();

    assert_eq!(counter.next(), Some(1));
    assert_eq!(counter.next(), Some(2));
    assert_eq!(counter.next(), Some(3));
    assert_eq!(counter.next(), Some(4));
    assert_eq!(counter.next(), Some(5));
    assert_eq!(counter.next(), None);
}
```
-->

```rust
# struct Counter {
#     count: u32,
# }
#
# impl Iterator for Counter {
#     type Item = u32;
#
#     fn next(&mut self) -> Option<Self::Item> {
#         self.count += 1;
#
#         if self.count < 6 {
#             Some(self.count)
#         } else {
#             None
#         }
#     }
# }
#
#[test]
fn calling_next_directly() {
    let mut counter = Counter::new();

    assert_eq!(counter.next(), Some(1));
    assert_eq!(counter.next(), Some(2));
    assert_eq!(counter.next(), Some(3));
    assert_eq!(counter.next(), Some(4));
    assert_eq!(counter.next(), Some(5));
    assert_eq!(counter.next(), None);
}
```

<!--
<span class="caption">Listing 13-22: Testing the functionality of the `next`
method implementation</span>
-->

<span class="caption">Listing 13-22: Test fonctionnelle de la méthode `next` de
notre implémentation</span>

<!--
This test creates a new `Counter` instance in the `counter` variable and then
calls `next` repeatedly, verifying that we have implemented the behavior we
want this iterator to have: returning the values from 1 to 5.
-->

Ce test créé une nouvelle instance `Counter` dans la variable `counter` et
appelle ensuite `next` à plusieurs reprises, en vérifiant que nous avons
implémenté le comportement que nous voulions que cet itérateur ait: renvoyer les
valeurs de 1 à 5.

<!--
#### Using Other `Iterator` Trait Methods
-->

#### Utilisation d'autres méthodes du trait `Iterator`

<!--
We implemented the `Iterator` trait by defining the `next` method, so we
can now use any `Iterator` trait method’s default implementations as defined in
the standard library, because they all use the `next` method’s functionality.
-->

Puisque nous avons implémenté le trait `Iterator` en définissant la méthode
`next`, nous pouvons maintenant utiliser les implémentations par défaut de
n'importe quelle méthode du trait `Iterator` telles que définies dans la
bibliothèque standard, car elles utilisent toutes la fonctionnalité de la
méthode `next`.

<!--
For example, if for some reason we wanted to take the values produced by an
instance of `Counter`, pair them with values produced by another `Counter`
instance after skipping the first value, multiply each pair together, keep only
those results that are divisible by 3, and add all the resulting values
together, we could do so, as shown in the test in Listing 13-23:
-->

Par exemple, si pour une raison quelconque nous voulions prendre les valeurs
produites par une instance de `Counter`, les coupler avec des valeurs produites
par une autre instance de `Counter` après avoir sauté la première valeur,
multiplier chaque paire ensemble, ne garder que les résultats qui sont
divisibles par trois et additionner toutes les valeurs résultantes ensemble,
nous pourrions le faire, comme le montre le test dans le Listing 13-23:

<!--
<span class="filename">Fichier : src/lib.rs</span>
-->

<span class="filename">Fichier : src/lib.rs</span>

<!--
```rust
# struct Counter {
#     count: u32,
# }
#
# impl Counter {
#     fn new() -> Counter {
#         Counter { count: 0 }
#     }
# }
#
# impl Iterator for Counter {
#     // Our iterator will produce u32s
#     type Item = u32;
#
#     fn next(&mut self) -> Option<Self::Item> {
#         // increment our count. This is why we started at zero.
#         self.count += 1;
#
#         // check to see if we've finished counting or not.
#         if self.count < 6 {
#             Some(self.count)
#         } else {
#             None
#         }
#     }
# }
#
#[test]
fn using_other_iterator_trait_methods() {
    let sum: u32 = Counter::new().zip(Counter::new().skip(1))
                                 .map(|(a, b)| a * b)
                                 .filter(|x| x % 3 == 0)
                                 .sum();
    assert_eq!(18, sum);
}
```
-->

```rust
# struct Counter {
#     count: u32,
# }
#
# impl Counter {
#     fn new() -> Counter {
#         Counter { count: 0 }
#     }
# }
#
# impl Iterator for Counter {
#     // Our iterator will produce u32s
#     type Item = u32;
#
#     fn next(&mut self) -> Option<Self::Item> {
#         // increment our count. This is why we started at zero.
#         self.count += 1;
#
#         // check to see if we've finished counting or not.
#         if self.count < 6 {
#             Some(self.count)
#         } else {
#             None
#         }
#     }
# }
#
#[test]
fn using_other_iterator_trait_methods() {
    let sum: u32 = Counter::new().zip(Counter::new().skip(1))
                                 .map(|(a, b)| a * b)
                                 .filter(|x| x % 3 == 0)
                                 .sum();
    assert_eq!(18, sum);
}
```

<!--
<span class="caption">Listing 13-23: Using a variety of `Iterator` trait
methods on our `Counter` iterator</span>
-->


<span class="caption">Listing 13-23: Utilisation d'une variété de méthodes de
traits `Iterator` sur notre itérateur `Counter` </span>

<!--
Note that `zip` produces only four pairs; the theoretical fifth pair `(5,
None)` is never produced because `zip` returns `None` when either of its input
iterators return `None`.
-->

Notez que `zip` ne produit que quatre paires; la cinquième paire théorique ` (5,
None)` n'est jamais produite parce que `zip` retourne `None` lorsque l'un de ses
itérateurs d'entrée retourne `None`.

<!--
All of these method calls are possible because we specified how the `next`
method works, and the standard library provides default implementations for
other methods that call `next`.
-->

Tous ces appels de méthode sont possibles parce que nous avons spécifié comment
la méthode `next` fonctionne et la bibliothèque standard fournit des
implémentations par défaut pour les autres méthodes qui appellent `next`.
