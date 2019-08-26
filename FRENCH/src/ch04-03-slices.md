<!--
## The Slice Type
-->

## Le type Slice

<!--
Another data type that does not have ownership is the *slice*. Slices let you
reference a contiguous sequence of elements in a collection rather than the
whole collection.
-->

Un autre type de données qui n'applique pas le principe d'appartenance est le
*slice*. Un slice vous permet d'avoir une référence vers une suite continue
d'éléments dans une collection plutôt que toute la collection.

<!--
Here’s a small programming problem: write a function that takes a string and
returns the first word it finds in that string. If the function doesn’t find a
space in the string, the whole string must be one word, so the entire string
should be returned.
-->

Voici un petit problème de programmation : écrire une fonction qui prend une
chaîne de caractères et retourne le premier mot qu'elle trouve dans cette
chaîne. Si la fonction ne trouve pas d'espace dans la chaîne, cela veut dire
que toute la chaîne est un seul mot, donc la chaîne en entier doit être
retournée.

<!--
Let’s think about the signature of this function:
-->

Imaginons la signature de cette fonction :

<!--
```rust,ignore
fn first_word(s: &String) -> ?
```
-->

```rust,ignore
fn first_word(s: &String) -> ?
```

<!--
This function, `first_word`, has a `&String` as a parameter. We don’t want
ownership, so this is fine. But what should we return? We don’t really have a
way to talk about *part* of a string. However, we could return the index of the
end of the word. Let’s try that, as shown in Listing 4-7.
-->

Cette fonction, `first_word`, prends un `&String` comme paramètre. Nous ne
voulons pas se l'approprier, donc tout va bien. Mais que devons-nous
retourner ? Nous n'avons pas de moyen de désigner une *partie* de chaîne de
caractères. Cependant, nous pouvons retourner l'index de la fin du mot.
Essayons cela dans l'entrée 4-5 :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Nom du fichier : src/main.rs</span>

<!--
```rust
fn first_word(s: &String) -> usize {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i;
        }
    }

    s.len()
}
```
-->

```rust
fn first_word(s: &String) -> usize {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i;
        }
    }

    s.len()
}
```

<!--
<span class="caption">Listing 4-7: The `first_word` function that returns a
byte index value into the `String` parameter</span>
-->

<span class="caption">Entrée 4-5 : la fonction `first_word` qui retourne la
valeur d'index d'octet dans le paramètre `String`</span>

<!--
Because we need to go through the `String` element by element and check whether
a value is a space, we’ll convert our `String` to an array of bytes using the
`as_bytes` method:
-->

Regardons un peu plus en détail ce code. Puisque nous avons besoin de parcourir
le `String` éléments par éléments et vérifier si leur valeur est un espace,
nous allons convertir notre `String` en tableau d'octets en utilisant la
méthode `as_bytes` :

<!--
```rust,ignore
let bytes = s.as_bytes();
```
-->

```rust,ignore
let bytes = s.as_bytes();
```

<!--
Next, we create an iterator over the array of bytes using the `iter` method:
-->

Ensuite, nous créons un itérateur sur le tableau d'octets en utilisant la
méthode `iter` :

<!--
```rust,ignore
for (i, &item) in bytes.iter().enumerate() {
```
-->

```rust,ignore
for (i, &item) in bytes.iter().enumerate() {
```

<!--
We’ll discuss iterators in more detail in Chapter 13. For now, know that `iter`
is a method that returns each element in a collection and that `enumerate`
wraps the result of `iter` and returns each element as part of a tuple instead.
The first element of the tuple returned from `enumerate` is the index, and the
second element is a reference to the element. This is a bit more convenient
than calculating the index ourselves.
-->

Nous discuterons plus en détail des itérateur dans le chapitre 13. Pour le
moment, sachez que ce `iter` est une méthode qui retourne chaque élément dans
une collection, et que `enumerate` enveloppe le résultat de `iter` et retourne
plutôt chaque élément comme une partie d'un tuple. Le premier élément du tuple
retourné est l'index, et le second  élément est une référence vers l'élément.
C'est un peu plus pratique que de calculer les index par nous-mêmes.

<!--
Because the `enumerate` method returns a tuple, we can use patterns to
destructure that tuple, just like everywhere else in Rust. So in the `for`
loop, we specify a pattern that has `i` for the index in the tuple and `&item`
for the single byte in the tuple. Because we get a reference to the element
from `.iter().enumerate()`, we use `&` in the pattern.
-->

Comme la méthode `enumerate` retourne un tuple, ne pouvons utiliser une
technique pour décomposer ce tuple, comme nous pourrions le faire n'importe où
avec Rust. Donc dans la boucle `for`, nous précisions un schéma qui indique que
nous définissons `i` pour l'index à partir du tuple et `&item` for chaque octet
dans le tuple. Comme nous obtenons une référence vers l'élément avec
`.iter().enumerate()`, nous utilisons `&` dans le schéma.

<!--
Inside the `for` loop, we search for the byte that represents the space by
using the byte literal syntax. If we find a space, we return the position.
Otherwise, we return the length of the string by using `s.len()`:
-->

Nous recherchons l'octet qui représente l'espace en utilisant la syntaxe des
mots binaires. Si nous trouvons un espace, nous retournons sa position. Sinon,
nous retournons la taille du string en utilisant `s.len()` :

<!--
```rust,ignore
    if item == b' ' {
        return i;
    }
}

s.len()
```
-->

```rust,ignore
    if item == b' ' {
        return i;
    }
}
s.len()
```

<!--
We now have a way to find out the index of the end of the first word in the
string, but there’s a problem. We’re returning a `usize` on its own, but it’s
only a meaningful number in the context of the `&String`. In other words,
because it’s a separate value from the `String`, there’s no guarantee that it
will still be valid in the future. Consider the program in Listing 4-8 that
uses the `first_word` function from Listing 4-7.
-->

Nous avons maintenant une façon de trouver l'index de la fin du premier mot
dans la chaîne de caractères, mais il y a un problème. Nous retournons un
`usize` seul, mais il n'est important que lorsqu'il est mis en rapport avec
le `&String`. Autrement dit, parce qu'il a une valeur séparée du `String`, il
n'y a pas de garantie qu'il sera toujours valide dans le futur. Imaginons
le programme dans l'entrée 4-6 qui utilise la fonction de l'entrée 4-5 :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Nom du fichier : src/main.rs</span>

<!--
```rust
# fn first_word(s: &String) -> usize {
#     let bytes = s.as_bytes();
#
#     for (i, &item) in bytes.iter().enumerate() {
#         if item == b' ' {
#             return i;
#         }
#     }
#
#     s.len()
# }
#
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s); // word will get the value 5

    s.clear(); // this empties the String, making it equal to ""

    // word still has the value 5 here, but there's no more string that
    // we could meaningfully use the value 5 with. word is now totally invalid!
}
```
-->

```rust
# fn first_word(s: &String) -> usize {
#     let bytes = s.as_bytes();
#
#     for (i, &item) in bytes.iter().enumerate() {
#         if item == b' ' {
#             return i;
#         }
#     }
#
#     s.len()
# }
#
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s); // word aura 5 comme valeur.

    s.clear(); // Ceci vide le String, il faut maintenant "".

    // word a toujours la valeur 5 ici, mais il n'y a plus de chaîne qui donne
    // du sens à la valeur 5. word est maintenant complètement invalide !
}
```

<!--
<span class="caption">Listing 4-8: Storing the result from calling the
`first_word` function and then changing the `String` contents</span>
-->

<span class="caption">Entrée 4-6 : On stocke le résultat de l'appel à la
fonction `first_word` et ensuite on change le contenu du `String`</span>

<!--
This program compiles without any errors and would also do so if we used `word`
after calling `s.clear()`. Because `word` isn’t connected to the state of `s`
at all, `word` still contains the value `5`. We could use that value `5` with
the variable `s` to try to extract the first word out, but this would be a bug
because the contents of `s` have changed since we saved `5` in `word`.
-->

Ce programme se compile sans aucune erreur et serait toujours OK si nous
utilisions `word` après avoir appelé `s.clear()`. `word` n'est pas du tout lié
à l'état de `s`, donc `word` contient toujours la valeur `5`. Nous pourrions
utiliser cette valeur `5` avec la variable `s` pour essayer d'en extraire le
premier mot, mais cela serait un bogue, car le contenu de `s` a changé depuis
que nous avons enregistré `5` dans `word`.

<!--
Having to worry about the index in `word` getting out of sync with the data in
`s` is tedious and error prone! Managing these indices is even more brittle if
we write a `second_word` function. Its signature would have to look like this:
-->

Se préoccuper en permanence que l'index dans `word` ne soit plus synchronisé
avec les données dans `s` est fastidieux et source d'erreur ! La gestion de ces
index est encore plus risquée si nous écrivons une fonction second_word. Sa
signature ressemblerait à quelque chose comme ceci :

<!--
```rust,ignore
fn second_word(s: &String) -> (usize, usize) {
```
-->

```rust,ignore
fn second_word(s: &String) -> (usize, usize) {
```

<!--
Now we’re tracking a starting *and* an ending index, and we have even more
values that were calculated from data in a particular state but aren’t tied to
that state at all. We now have three unrelated variables floating around that
need to be kept in sync.
-->

Maintenant nous gérons un index de début *et* un index de fin, et nous avons
encore plus de valeurs qui sont calculées à partir de la donnée dans un
état particulier, mais qui n'est pas lié à son état en temps réel. Nous avons
maintenant trois variables isolées qui ont besoin d'être maintenu à jour.

<!--
Luckily, Rust has a solution to this problem: string slices.
-->

Heureusement, Rust a une solution pour ce problème : les slices de chaînes de
caractères.

<!--
### String Slices
-->

### Les slices de chaînes de caractères

<!--
A *string slice* is a reference to part of a `String`, and it looks like this:
-->

Un *slice de chaîne de caractère* est une référence à une partie d'un `String`,
et ressemble à ceci :

<!--
```rust
let s = String::from("hello world");

let hello = &s[0..5];
let world = &s[6..11];
```
-->

```rust
let s = String::from("hello world");

let hello = &s[0..5];
let world = &s[6..11];
```

<!--
This is similar to taking a reference to the whole `String` but with the extra
`[0..5]` bit. Rather than a reference to the entire `String`, it’s a reference
to a portion of the `String`.
-->

Ce serait comme prendre une référence pour tout le `String`, mais avec en plus le
mot `[0..5]`. Plutôt qu'une référence vers tout le `String`, c'est une
référence à une partie du `String`. La syntaxe `début..fin` est une intervalle
qui commence à `start` et comprends la suite jusqu'à `end` exclus.

<!--
We can create slices using a range within brackets by specifying
`[starting_index..ending_index]`, where `starting_index` is the first position
in the slice and `ending_index` is one more than the last position in the
slice. Internally, the slice data structure stores the starting position and
the length of the slice, which corresponds to `ending_index` minus
`starting_index`. So in the case of `let world = &s[6..11];`, `world` would be
a slice that contains a pointer to the 7th byte of `s` with a length value of 5.
-->

Nous pouvons créer des slices en utilisant une intervalle entre crochets en
spécifiant `[index_debut..index_fin]`, où `index_debut` est la première
position dans le slice et `index_fin` est une position en plus que la dernière
position dans le slice. En interne, la structure de données du slice enregistre
la position de départ et la longeur du slice, ce qui correspond à `index_fin`
moins `index_debut`. Donc dans le cas de `let world = &s[6..11];`, `world` va
être un slice qui a un pointeur vers le sixième octet de `s` et une longueur
de 5.

<!--
Figure 4-6 shows this in a diagram.
-->

L'illustration 4-6 montre cela dans un diagramme.

<!-->
<img alt="world containing a pointer to the 6th byte of String s and a length 5" src="img/trpl04-06.svg" class="center" style="width: 50%;" />
-->

<img alt="world contient un pointeur vers le sixième octet du String s et une longueur de 5" src="img/trpl04-06.svg" class="center" style="width: 50%;" />

<!--
<span class="caption">Figure 4-6: String slice referring to part of a
`String`</span>
-->

<span class="caption">Illustration 4-6 : un slice de String qui pointe vers
une partie de `String`</span>

<!--
With Rust’s `..` range syntax, if you want to start at the first index (zero),
you can drop the value before the two periods. In other words, these are equal:
-->

Avec la syntaxe d'interface `..` de Rust, si vous voulez commencer au premier
index (zéro), vous pouvez ne rien mettre avant les deux points. Autrement dit,
ceci est identique :

<!--
```rust
let s = String::from("hello");

let slice = &s[0..2];
let slice = &s[..2];
```
-->

```rust
let s = String::from("hello");

let slice = &s[0..2];
let slice = &s[..2];
```

<!--
By the same token, if your slice includes the last byte of the `String`, you
can drop the trailing number. That means these are equal:
-->

De la même manière, si votre slice contient les derniers octets du `String`,
vous pouvez ne rien mettre à la fin. Cela veut dire que ces deux instructions
sont identiques :

<!--
```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[3..len];
let slice = &s[3..];
```
-->

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[3..len];
let slice = &s[3..];
```

<!--
You can also drop both values to take a slice of the entire string. So these
are equal:
-->

Vous pouvez aussi ne mettre aucune limite pour faire un slice de toute la
chaîne de caractères. Donc ces deux cas sont identiques :

<!--
```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[0..len];
let slice = &s[..];
```
-->

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[0..len];
let slice = &s[..];
```

<!--
> Note: String slice range indices must occur at valid UTF-8 character
> boundaries. If you attempt to create a string slice in the middle of a
> multibyte character, your program will exit with an error. For the purposes
> of introducing string slices, we are assuming ASCII only in this section; a
> more thorough discussion of UTF-8 handling is in the [“Storing UTF-8 Encoded
> Text with Strings”][strings]<!-- ignore -- > section of Chapter 8.
-->

> Note : Les indexes de l'intervalle d'un slice d'un String doivent toujours
> être des valeurs compatibles avec l'UTF-8. Si vous essayez de créer un slice
> d'une chaîne de caractères au millieu d'un caractère codé sur plusieurs
> octets, votre programme va se fermer avec une erreur. Pour que nous abordions
> simplement les slice de chaînes de caractères, nous supposerons que nous
> utilisons l'ASCII uniquement dans cette section; nous discuterons plus en
> détails de la gestion UTF-8 dans la section “Chaînes de caractères” au
> chapitre 8.

<!--
With all this information in mind, let’s rewrite `first_word` to return a
slice. The type that signifies “string slice” is written as `&str`:
-->

Avec toutes ces informations, essayons de ré-écrire `first_word` pour retourner
un slice. Le type pour les “slices de chaînes de caractères” s'écrit `&str` :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Nom du fichier : src/main.rs</span>

<!--
```rust
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```
-->

```rust
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

<!--
We get the index for the end of the word in the same way as we did in Listing
4-7, by looking for the first occurrence of a space. When we find a space, we
return a string slice using the start of the string and the index of the space
as the starting and ending indices.
-->

Nous récupérons l'index de la fin du mot de la même façon que nous l'avons fait
dans l'entrée 4-5, en cherchant la première occurrence d'un espace. Quand nous
trouvons un espace, nous retournons un slice de chaîne de caractère en
utilisant le début de la chaîne de caractères et l'index de l'espace comme
indices de début et fin.

<!--
Now when we call `first_word`, we get back a single value that is tied to the
underlying data. The value is made up of a reference to the starting point of
the slice and the number of elements in the slice.
-->

Maintenant, quand nous appelons `first_word`, nous récupérons une seule valeur
qui est liée à la donnée de base. La valeur est construite avec une référence
vers le point de départ du slice et nombre d'éléments dans le slice.

<!--
Returning a slice would also work for a `second_word` function:
-->

Retourner un slice fonctionnerait aussi pour une fonction `second_word` :

<!--
```rust,ignore
fn second_word(s: &String) -> &str {
```
-->

```rust,ignore
fn second_word(s: &String) -> &str {
```

<!--
We now have a straightforward API that’s much harder to mess up, because the
compiler will ensure the references into the `String` remain valid. Remember
the bug in the program in Listing 4-8, when we got the index to the end of the
first word but then cleared the string so our index was invalid? That code was
logically incorrect but didn’t show any immediate errors. The problems would
show up later if we kept trying to use the first word index with an emptied
string. Slices make this bug impossible and let us know we have a problem with
our code much sooner. Using the slice version of `first_word` will throw a
compile-time error:
-->

Nous avons maintenant une API simple qui est bien plus difficile à perturber,
puisque le compilateur va s'assurer que les références dans le `String` seront
toujours en vigueur. Souvenez-vous du bogue dans le programme de l'entrée 4-6,
quand nous avions un index vers la fin du premier mot mais qu'ensuite nous
avions vidé la chaîne de caractères et que notre index n'était plus valide ?
Ce code était logiquement incorrect, mais nous n'avons pas immédiatement vu
d'erreurs. Les problèmes vont arriver plus tard si nous essayons d'utiliser
l'index du premier mot avec une chaîne de caractère qui a été vidée. Les slices
rendent ce bogue impossible et nous fait savoir bien plus tôt quand nous avons
un problème avec notre code. Utiliser la version avec le slice de `first_word`
va lever une erreur au moment de la compilation :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Nom du fichier : src/main.rs</span>

<!--
```rust,ignore,does_not_compile
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // error!

    println!("the first word is: {}", word);
}
```
-->

```rust,ignore
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // Erreur !
}
```

<!--
Here’s the compiler error:
-->

Voici l'erreur du compilateur :

<!--
```text
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
  -- > src/main.rs:18:5
   |
16 |     let word = first_word(&s);
   |                           -- immutable borrow occurs here
17 |
18 |     s.clear(); // error!
   |     ^^^^^^^^^ mutable borrow occurs here
19 |
20 |     println!("the first word is: {}", word);
   |                                       ---- immutable borrow later used here
```
-->

```text
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
 --> src/main.rs:6:5
  |
4 |     let word = first_word(&s);
  |                            - immutable borrow occurs here
5 |
6 |     s.clear(); // Error!
  |     ^ mutable borrow occurs here
7 | }
  | - immutable borrow ends here
```

<!--
Recall from the borrowing rules that if we have an immutable reference to
something, we cannot also take a mutable reference. Because `clear` needs to
truncate the `String`, it needs to get a mutable reference. Rust disallows
this, and compilation fails. Not only has Rust made our API easier to use, but
it has also eliminated an entire class of errors at compile time!
-->

Rappellons-nous que d'après les règles de référencement, si nous avons une
référence immuable vers quelque chose, nous ne pouvons pas avoir une référence
modifiable en même temps. Etant donné que `clear` a besoin de raccourcir le
`String`, il essaye de prendre une référence modifiable, ce qui échoue. Non
seulement Rust a simplifié l'utilisation de notre API, mais il a aussi éliminé
une catégorie entière d'erreurs au moment de la compilation !

<!--
#### String Literals Are Slices
-->

#### Les chaînes de caractères pures sont des Slices

<!--
Recall that we talked about string literals being stored inside the binary. Now
that we know about slices, we can properly understand string literals:
-->

Souvenez-vous lorsque nous avons vu les chaînes des caractères pures qui
étaient enregistrées dans le binaire. Maintenant que nous connaissons les
slices, nous pouvons comprendre comme il faut les chaînes des caractères pures.

<!--
```rust
let s = "Hello, world!";
```
-->

```rust
let s = "Hello, world!";
```

<!--
The type of `s` here is `&str`: it’s a slice pointing to that specific point of
the binary. This is also why string literals are immutable; `&str` is an
immutable reference.
-->

Ici, le type de `s` est un `&str` : c'est un slice qui pointe vers un endroit
spécifique du binaire. C'est pourquoi les chaînes des caractères pures sont
immuables; `&str` est une référence immuable.

<!--
#### String Slices as Parameters
-->

#### Des slices de chaînes de caractères en paramètres

<!--
Knowing that you can take slices of literals and `String` values leads us to
one more improvement on `first_word`, and that’s its signature:
-->

Apprendre que vous pouvez utiliser des slices de texte et de `String` nous
amène à apporter quelques améliorations sur `first_word`, voici sa signature :

<!--
```rust,ignore
fn first_word(s: &String) -> &str {
```
-->

```rust,ignore
fn first_word(s: &String) -> &str {
```

<!--
A more experienced Rustacean would write the signature shown in Listing 4-9
instead because it allows us to use the same function on both `&String` values
and `&str` values.
-->

Un Rustacéen plus expérimenté écrirait plutôt la ligne suivante, car cela nous
permet d'utiliser la même fonction sur les `String` et les `&str` :

<!--
```rust,ignore
fn first_word(s: &str) -> &str {
```
-->

```rust,ignore
fn first_word(s: &str) -> &str {
```

<!--
<span class="caption">Listing 4-9: Improving the `first_word` function by using
a string slice for the type of the `s` parameter</span>
-->

<span class="caption">Listing 4-9: Improving the `first_word` function by using
a string slice for the type of the `s` parameter</span>

<!--
If we have a string slice, we can pass that directly. If we have a `String`, we
can pass a slice of the entire `String`. Defining a function to take a string
slice instead of a reference to a `String` makes our API more general and useful
without losing any functionality:
-->

Si nous avions un slice de chaîne de caractères, nous pouvons lui envoyer
directement. Si nous avions un `String`, nous pourrions envoyer un slice de
tout le `String`. Concevoir une fonction pour prendre un slice de chaîne de
caractères plutôt qu'une référence à une chaîne de caractères rend notre API
plus générique et plus utile sans perdre aucune fonctionnalité :

<!--
<span class="filename">Filename: src/main.rs</span>
-->

<span class="filename">Nom du fichier : src/main.rs</span>

<!--
```rust
# fn first_word(s: &str) -> &str {
#     let bytes = s.as_bytes();
#
#     for (i, &item) in bytes.iter().enumerate() {
#         if item == b' ' {
#             return &s[0..i];
#         }
#     }
#
#     &s[..]
# }
fn main() {
    let my_string = String::from("hello world");

    // first_word works on slices of `String`s
    let word = first_word(&my_string[..]);

    let my_string_literal = "hello world";

    // first_word works on slices of string literals
    let word = first_word(&my_string_literal[..]);

    // Because string literals *are* string slices already,
    // this works too, without the slice syntax!
    let word = first_word(my_string_literal);
}
```
-->

```rust
# fn first_word(s: &str) -> &str {
#     let bytes = s.as_bytes();
#
#     for (i, &item) in bytes.iter().enumerate() {
#         if item == b' ' {
#             return &s[0..i];
#         }
#     }
#
#     &s[..]
# }
fn main() {
    let my_string = String::from("hello world");

    // first_word travaille avec un slice de `String`
    let word = first_word(&my_string[..]);

    let my_string_literal = "hello world";

    // first_word travaille avec un slice de chaîne de caractères pure
    let word = first_word(&my_string_literal[..]);

    // puisque les chaînes de caractères *sont* déjà des slices de chaînes
    // de caractères, ceci fonctionne aussi, sans la syntaxe de slice !
    let word = first_word(my_string_literal);
}
```

<!--
### Other Slices
-->

### Les autres slices

<!--
String slices, as you might imagine, are specific to strings. But there’s a
more general slice type, too. Consider this array:
-->

Les slices de chaînes de caractères, comme vous pouvez l'imaginer, sont
spécifiques aux chaînes de caractères. Mais il y a aussi un type plus
générique. Admettons ce tableau :

<!--
```rust
let a = [1, 2, 3, 4, 5];
```
-->

```rust
let a = [1, 2, 3, 4, 5];
```

<!--
Just as we might want to refer to a part of a string, we might want to refer
to part of an array. We’d do so like this:
-->

Comme nous pouvons nous référer à une partie de chaîne de caractères, nous
pouvons nous référer à une partie d'un tableau et nous le faisons comme ceci :

<!--
```rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];
```
-->

```rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];
```

<!--
This slice has the type `&[i32]`. It works the same way as string slices do, by
storing a reference to the first element and a length. You’ll use this kind of
slice for all sorts of other collections. We’ll discuss these collections in
detail when we talk about vectors in Chapter 8.
-->

Ce slice est de type `&[i32]`. Il fonctionne de la même manière que les slices
de chaînes de caractères, en enregistrant une référence vers le premier élément
et une longueur. Vous pouvez utiliser ce type de slice pour tous les autres
types de collections. Nous discuterons de ces collections en détail quand nous
verrons les vecteurs au Chapitre 8.

<!--
## Summary
-->

## Résumé

<!--
The concepts of ownership, borrowing, and slices ensure memory safety in Rust
programs at compile time. The Rust language gives you control over your memory
usage in the same way as other systems programming languages, but having the
owner of data automatically clean up that data when the owner goes out of scope
means you don’t have to write and debug extra code to get this control.
-->

Les concepts d'appartenance, d'emprunt, et les slices garantissent la sécurité
de la mémoire dans les programmes Rust au moment de la compilation. Le langage
Rust vous donne le contrôle sur l'utilisation de la mémoire comme tous les
systèmes de langages de programmation, mais avoir le propriétaire des données
qui nettoie automatiquement ces données quand il sort de la portée vous permet
de ne pas avoir à écrire et déboguer du code en plus pour avoir ce contrôle.

<!--
Ownership affects how lots of other parts of Rust work, so we’ll talk about
these concepts further throughout the rest of the book. Let’s move on to
Chapter 5 and look at grouping pieces of data together in a `struct`.
-->

L'appropriation influe sur de nombreux fonctionnements de Rust, donc nous
allons encore parler de ces concepts plus loin dans le livre. Allons
maintenant au chapitre suivant et regardons comment regrouper des données
ensemble dans un `struct`.

[strings]: ch08-02-strings.html#storing-utf-8-encoded-text-with-strings
