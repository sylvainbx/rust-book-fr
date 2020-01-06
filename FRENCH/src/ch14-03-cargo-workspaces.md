<!--
## Cargo Workspaces
-->

## Les espaces de travail de cargo

<!--
In Chapter 12, we built a package that included a binary crate and a library
crate. As your project develops, you might find that the library crate
continues to get bigger and you want to split up your package further into
multiple library crates. In this situation, Cargo offers a feature called
*workspaces* that can help manage multiple related packages that are developed
in tandem.
-->

Dans le chapitre 12, nous avons construit un paquet qui comprenait une crate
binaire et une crate de bibliothèque. Au fur et à mesure que votre projet se
développe, vous pourrez constater que la crate de bibliothèque continue de
s'agrandir et vous voudriez alors peut-être diviser votre paquet en plusieurs
crates de bibliothèque. Pour cette situation, cargo a une fonctionnalité qui
s'appelle *les espaces de travail* qui peuvent aider à gérer plusieurs paquets
liés qui sont développés en tandem.

<!--
### Creating a Workspace
-->

### Créer un espace de travail

<!--
A *workspace* is a set of packages that share the same *Cargo.lock* and output
directory. Let’s make a project using a workspace—we’ll use trivial code so we
can concentrate on the structure of the workspace. There are multiple ways to
structure a workspace; we’re going to show one common way. We’ll have a
workspace containing a binary and two libraries. The binary, which will provide
the main functionality, will depend on the two libraries. One library will
provide an `add_one` function, and a second library an `add_two` function.
These three crates will be part of the same workspace. We’ll start by creating
a new directory for the workspace:
-->

Un *espace de travail* est un jeu de paquets qui partagent tous le même
*Cargo.lock* et le même dossier de sortie. Créons donc un projet en utilisant un
espace de travail — nous allons utiliser du code trivial afin de nous concentrer
sur la structure de l'espace de travail. Il existe plusieurs façons de
structurer un espace de travail ; nous allons vous montrer une manière commune
d'organisation. Nous allons avoir un espace de travail contenant un binaire et
deux bibliothèques. Le binaire, qui devrait fournir les fonctionnalités
principales, va dépendre des deux bibliothèques. Une bibliothèque va fournir une
fonction `ajouter_un`, et la seconde bibliothèque, une fonction `ajouter_deux`.
Ces trois crates feront partie du même espace de travail. Nous allons commencer
par créer un nouveau dossier pour cet espace de travail :

<!--
```text
$ mkdir add
$ cd add
```
-->

```text
$ mkdir ajout
$ cd ajout
```

<!--
Next, in the *add* directory, we create the *Cargo.toml* file that will
configure the entire workspace. This file won’t have a `[package]` section or
the metadata we’ve seen in other *Cargo.toml* files. Instead, it will start
with a `[workspace]` section that will allow us to add members to the workspace
by specifying the path to our binary crate; in this case, that path is *adder*:
-->

Ensuite, dans le dossier *ajout*, nous créons le fichier *Cargo.toml* qui va
configurer l'intégralité de l'espace de travail. Ce fichier n'aura pas de
section `[package]` ou les métadonnées que nous avons vues dans les autres
fichiers *Cargo.toml*. A la place, il commencera par une section `[workspace]`
qui va nous permettre d'ajouter des membres à l'espace de travail en
renseignant le chemin vers notre crate binaire ; dans ce cas, ce chemin est
*additioneur* :

<!--
<span class="filename">Filename: Cargo.toml</span>
-->

<span class="filename">Fichier : Cargo.toml</span>

<!--
```toml
[workspace]

members = [
    "adder",
]
```
-->

```toml
[workspace]

members = [
    "additioneur",
]
```

<!--
Next, we’ll create the `adder` binary crate by running `cargo new` within the
*add* directory:
-->

Ensuite, nous allons créer la crate binaire `additioneur` en lançant `cargo new`
dans le dossier *ajout* :

<!--
```text
$ cargo new adder
     Created binary (application) `adder` project
```
-->

```text
$ cargo new additioneur
     Created binary (application) `additioneur` project
```

<!--
At this point, we can build the workspace by running `cargo build`. The files
in your *add* directory should look like this:
-->

A partir de ce moment, nous pouvons compiler l'espace de travail en lançant
`cargo build`. Les fichiers dans votre dossier *ajout* devraient ressembler à
ceci :

<!--
```text
├── Cargo.lock
├── Cargo.toml
├── adder
│   ├── Cargo.toml
│   └── src
│       └── main.rs
└── target
```
-->

```text
├── Cargo.lock
├── Cargo.toml
├── additioneur
│   ├── Cargo.toml
│   └── src
│       └── main.rs
└── target
```

<!--
The workspace has one *target* directory at the top level for the compiled
artifacts to be placed into; the `adder` crate doesn’t have its own *target*
directory. Even if we were to run `cargo build` from inside the *adder*
directory, the compiled artifacts would still end up in *add/target* rather
than *add/adder/target*. Cargo structures the *target* directory in a workspace
like this because the crates in a workspace are meant to depend on each other.
If each crate had its own *target* directory, each crate would have to
recompile each of the other crates in the workspace to have the artifacts in
its own *target* directory. By sharing one *target* directory, the crates can
avoid unnecessary rebuilding.
-->

L'espace de travail a un dossier *target* au niveau le plus haut pour y placer
les artefacts compilés ; la crate `additioneur` n'a pas son propre dossier
*target*. Même si nous lancions `cargo build` à l'intérieur du dossier
*additioneur*, les artefacts compilés finirons toujours dans *ajout/target*
plutôt que dans *ajout/additioneur/target*. Cargo organise ainsi le dossier
*target* car les crates d'un espace de travail sont censés dépendre l'une de
l'autre. Si chaque crate avait son propre dossier *target*, chaque crate
devrait recompiler chacune des autres crates présentes dans l'espace de
travail pour avoir les artefacts dans son propre dossier *target*. En
partageant un seul dossier *target*, les crates peuvent éviter des
re-compilations inutiles.

<!--
### Creating the Second Crate in the Workspace
-->

### Créer la seconde crate dans l'espace de travail

<!--
Next, let’s create another member crate in the workspace and call it `add-one`.
Change the top-level *Cargo.toml* to specify the *add-one* path in the
`members` list:
-->

Ensuite, créons une autre crate, membre de l'espace de travail et appelons-la
`ajouter-un`. Changeons le *Cargo.toml* du niveau le plus haut pour renseigner
le chemin vers *ajouter-un* dans la liste `members` :

<!--
<span class="filename">Filename: Cargo.toml</span>
-->

<span class="filename">Fichier : Cargo.toml</span>

<!--
```toml
[workspace]

members = [
    "adder",
    "add-one",
]
```
-->

```toml
[workspace]

members = [
    "additioneur",
    "ajouter-un",
]
```

<!--
Then generate a new library crate named `add-one`:
-->

Ensuite, générons une nouvelle crate de bibliothèque `ajouter-un` :

<!--
```text
$ cargo new add-one --lib
     Created library `add-one` project
```
-->

```text
$ cargo new ajouter-un --lib
     Created library `ajouter-un` project
```

<!--
Your *add* directory should now have these directories and files:
-->

Votre dossier *ajout* devrait maintenant avoir ces dossiers et fichiers :

<!--
```text
├── Cargo.lock
├── Cargo.toml
├── add-one
│   ├── Cargo.toml
│   └── src
│       └── lib.rs
├── adder
│   ├── Cargo.toml
│   └── src
│       └── main.rs
└── target
```
-->

```text
├── Cargo.lock
├── Cargo.toml
├── ajouter-un
│   ├── Cargo.toml
│   └── src
│       └── lib.rs
├── addition
│   ├── Cargo.toml
│   └── src
│       └── main.rs
└── target
```

<!--
In the *add-one/src/lib.rs* file, let’s add an `add_one` function:
-->

Dans le fichier *ajouter-un/src/lib.rs*, ajoutons une fonction `ajouter_un` :

<!--
<span class="filename">Filename: add-one/src/lib.rs</span>
-->

<span class="filename">Fichier : ajouter-un/src/lib.rs</span>

<!--
```rust
pub fn add_one(x: i32) -> i32 {
    x + 1
}
```
-->

```rust
pub fn ajouter_un(x: i32) -> i32 {
    x + 1
}
```

<!--
Now that we have a library crate in the workspace, we can have the binary crate
`adder` depend on the library crate `add-one`. First, we’ll need to add a path
dependency on `add-one` to *adder/Cargo.toml*.
-->

Maintenant que nous avons une crate de bibliothèque dans l'espace de travail,
nous pouvons faire en sorte que la crate binaire `addition` dépende de la crate
de bibliothèque `ajouter-un`. D'abord, nous devons ajouter un chemin de
dépendance à `ajouter-un` dans *addition/Cargo.toml*.

<!--
<span class="filename">Filename: adder/Cargo.toml</span>
-->

<span class="filename">Fichier : addition/Cargo.toml</span>

<!--
```toml
[dependencies]

add-one = { path = "../add-one" }
```
-->

```toml
[dependencies]

ajouter-un = { path = "../ajouter-un" }
```

<!--
Cargo doesn’t assume that crates in a workspace will depend on each other, so
we need to be explicit about the dependency relationships between the crates.
-->

Cargo ne fait pas la supposition que les crates d'un espace de travail
dépendent l'une de l'autre, donc vous devez être explicites sur les relations
de dépendance entre les crates.

<!--
Next, let’s use the `add_one` function from the `add-one` crate in the `adder`
crate. Open the *adder/src/main.rs* file and add a `use` line at the top to
bring the new `add-one` library crate into scope. Then change the `main`
function to call the `add_one` function, as in Listing 14-7.
-->

Ensuite, utilisons la fonction `ajouter_un` de la crate `ajouter-un` dans la
crate `addition`. Ouvrez le fichier *addition/src/main.rs* et ajoutez une
ligne `use` tout en haut pour importer la bibliothèque `ajouter-un` dans la
portée. Changez ensuite la fonction `main` pour appeler la fonction
`ajouter_un`, comme dans l'encart 14-7.

<!--
<span class="filename">Filename: adder/src/main.rs</span>
-->

<span class="filename">Fichier : addition/src/main.rs</span>

<!--
```rust,ignore
use add_one;

fn main() {
    let num = 10;
    println!("Hello, world! {} plus one is {}!", num, add_one::add_one(num));
}
```
-->

```rust,ignore
use ajouter_un;

fn main() {
    let nombre = 10;
    println!("Hello, world ! {} + 1 = {} !", nombre, ajouter_un::ajouter_un(nombre));
}
```

<!--
<span class="caption">Listing 14-7: Using the `add-one` library crate from the
`adder` crate</span>
-->

<span class="caption">Encart 14-7 : utilisation de la bibliothèque `ajouter-un`
dans la crate `addition`</span>

<!--
Let’s build the workspace by running `cargo build` in the top-level *add*
directory!
-->

Compilons l'espace de travail en lançant `cargo build` dans le niveau le plus
haut du dossier *ajout* !

<!--
```text
$ cargo build
   Compiling add-one v0.1.0 (file:///projects/add/add-one)
   Compiling adder v0.1.0 (file:///projects/add/adder)
    Finished dev [unoptimized + debuginfo] target(s) in 0.68 secs
```
-->

```text
$ cargo build
   Compiling ajouter-un v0.1.0 (file:///projects/ajout/ajouter-un)
   Compiling addition v0.1.0 (file:///projects/ajout/addition)
    Finished dev [unoptimized + debuginfo] target(s) in 0.68 secs
```

<!--
To run the binary crate from the *add* directory, we need to specify which
package in the workspace we want to use by using the `-p` argument and the
package name with `cargo run`:
-->

Pour lancer la crate binaire à partir du dossier *ajout*, nous devons
préciser quel paquet nous souhaitons utiliser dans l'espace de travail en
utilisant l'argument `-p` suivi du nom du paquet avec `cargo run` :

<!--
```text
$ cargo run -p adder
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/adder`
Hello, world! 10 plus one is 11!
```
-->

```text
$ cargo run -p addition
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/addition`
Hello, world ! 10 + 1 = 11 !
```

<!--
This runs the code in *adder/src/main.rs*, which depends on the `add-one` crate.
-->

Cela exécute le code de *addition/src/main.rs*, qui dépend de la crate
`ajouter-un`.

<!--
#### Depending on an External Crate in a Workspace
-->

#### Dépendre d'une crate externe dans un espace de travail

<!--
Notice that the workspace has only one *Cargo.lock* file at the top level of
the workspace rather than having a *Cargo.lock* in each crate’s directory. This
ensures that all crates are using the same version of all dependencies. If we
add the `rand` crate to the *adder/Cargo.toml* and *add-one/Cargo.toml*
files, Cargo will resolve both of those to one version of `rand` and record
that in the one *Cargo.lock*. Making all crates in the workspace use the same
dependencies means the crates in the workspace will always be compatible with
each other. Let’s add the `rand` crate to the `[dependencies]` section in the
*add-one/Cargo.toml* file to be able to use the `rand` crate in the `add-one`
crate:
-->

Notez que l'espace de travail a un seul fichier *Cargo.lock* dans le niveau le
plus haut de l'espace de travail plutôt que d'avoir un *Cargo.lock* dans chaque
dossier de chaque crate. Cela garantit que toutes les crates utilisent la même
version de toutes les dépendances. Si nous ajoutons la crate `rand` aux fichiers
*addition/Cargo.toml* et *ajouter-un/Cargo.toml*, cargo va réunir ces deux en
une seule version de `rand` et enregistrer cela dans un seul *Cargo.lock*.
Faire en sorte que toutes les crates de l'espace de travail utilisent la même
dépendance signifie que les crates dans l'espace de travail seront toujours
compatibles l'une avec l'autre. Ajoutons la crate `rand` à la section
`[dependencies]` du fichier *ajouter-un/Cargo.toml* pour pouvoir utiliser la
crate `rand` dans la crate `ajouter-un` :

<!-- When updating the version of `rand` used, also update the version of
`rand` used in these files so they all match:
* ch02-00-guessing-game-tutorial.md
* ch07-04-bringing-paths-into-scope-with-the-use-keyword.md
-->

<!--
<span class="filename">Filename: add-one/Cargo.toml</span>
-->

<span class="filename">Fichier : ajouter-un/Cargo.toml</span>

```toml
[dependencies]
rand = "0.5.5"
```

<!--
We can now add `use rand;` to the *add-one/src/lib.rs* file, and building the
whole workspace by running `cargo build` in the *add* directory will bring in
and compile the `rand` crate:
-->

Nous pouvons maintenant ajouter `use rand;` au fichier *ajouter-un/src/lib.rs*,
et compiler l'ensemble de l'espace de travail en lançant `cargo build` dans le
dossier *ajout*, ce qui va importer et compiler la crate `rand` :

<!--
```text
$ cargo build
    Updating crates.io index
  Downloaded rand v0.5.5
   --snip--
   Compiling rand v0.5.5
   Compiling add-one v0.1.0 (file:///projects/add/add-one)
   Compiling adder v0.1.0 (file:///projects/add/adder)
    Finished dev [unoptimized + debuginfo] target(s) in 10.18 secs
```
-->

```text
$ cargo build
    Updating crates.io index
  Downloaded rand v0.5.5
   -- dans un encart --
   Compiling rand v0.5.5
   Compiling ajouter-un v0.1.0 (file:///projects/ajout/ajouter-un)
   Compiling addition v0.1.0 (file:///projects/ajout/addition)
    Finished dev [unoptimized + debuginfo] target(s) in 10.18 secs
```

<!--
The top-level *Cargo.lock* now contains information about the dependency of
`add-one` on `rand`. However, even though `rand` is used somewhere in the
workspace, we can’t use it in other crates in the workspace unless we add
`rand` to their *Cargo.toml* files as well. For example, if we add `use rand;`
to the *adder/src/main.rs* file for the `adder` crate, we’ll get an error:
-->

Le *Cargo.lock* du niveau le plus haut contient maintenant les informations
de dépendance à `rand` pour `ajouter-un`. Cependant, même si `rand` est
utilisé quelque part dans l'espace de travail, nous ne pouvons pas l'utiliser
dans d'autres crates de l'espace de travail tant que nous n'ajoutons pas
`rand` dans leurs fichiers *Cargo.toml*. Par exemple, si nous ajoutons
`use rand;` dans le fichier *addition/src/main.rs* pour la crate `addition`,
nous allons avoir une erreur :

<!--
```text
$ cargo build
   Compiling adder v0.1.0 (file:///projects/add/adder)
error: use of unstable library feature 'rand': use `rand` from crates.io (see
issue #27703)
 -- > adder/src/main.rs:1:1
  |
1 | use rand;
```
-->

```text
$ cargo build
   Compiling addition v0.1.0 (file:///projects/ajout/addition)
error: use of unstable library feature 'rand': use `rand` from crates.io (see
issue #27703)
 -- > addition/src/main.rs:1:1
  |
1 | use rand;
```

<!--
To fix this, edit the *Cargo.toml* file for the `adder` crate and indicate that
`rand` is a dependency for that crate as well. Building the `adder` crate will
add `rand` to the list of dependencies for `adder` in *Cargo.lock*, but no
additional copies of `rand` will be downloaded. Cargo has ensured that every
crate in the workspace using the `rand` crate will be using the same version.
Using the same version of `rand` across the workspace saves space because we
won’t have multiple copies and ensures that the crates in the workspace will be
compatible with each other.
-->

Pour corriger cela, modifiez le fichier *Cargo.toml* pour la crate `addition` et
indiquez que `rand` est aussi une dépendance de cette crate. La compilation de
la crate `addition` va rajouter `rand` à la liste des dépendances pour
`addition` dans *Cargo.lock*, mais aucune copie supplémentaire de `rand` sera
téléchargé. Cargo s'est assuré que toutes les crates de l'espace de travail qui
utilise `rand` serait de la même version. Utiliser la même version de `rand`
dans les espaces de travail économise de l'espace car nous n'avons pas à
multiplier les copies, ni s'assurer que les crates dans l'espace de travail
seraient compatibles l'une envers l'autre.

<!--
#### Adding a Test to a Workspace
-->

#### Ajouter un test à l'espace de travail

<!--
For another enhancement, let’s add a test of the `add_one::add_one` function
within the `add_one` crate:
-->

Afin de procéder à une autre amélioration, ajoutons un test de la fonction
`ajouter_un::ajouter_un` dans la crate `ajouter_un` :

<!--
<span class="filename">Filename: add-one/src/lib.rs</span>
-->

<span class="filename">Fihcier : add-one/src/lib.rs</span>

<!--
```rust
pub fn add_one(x: i32) -> i32 {
    x + 1
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn it_works() {
        assert_eq!(3, add_one(2));
    }
}
```
-->

```rust
pub fn ajouter_un(x: i32) -> i32 {
    x + 1
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn cela_fonctionne() {
        assert_eq!(3, ajouter_un(2));
    }
}
```

<!--
Now run `cargo test` in the top-level *add* directory:
-->

Lancez maintenant `cargo test` dans le niveau le plus haut du
dossier *ajout* :

<!--
```text
$ cargo test
   Compiling add-one v0.1.0 (file:///projects/add/add-one)
   Compiling adder v0.1.0 (file:///projects/add/adder)
    Finished dev [unoptimized + debuginfo] target(s) in 0.27 secs
     Running target/debug/deps/add_one-f0253159197f7841

running 1 test
test tests::it_works ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

     Running target/debug/deps/adder-f88af9d2cc175a5e

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

   Doc-tests add-one

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```
-->

```text
$ cargo test
   Compiling ajouter-un v0.1.0 (file:///projects/ajout/ajouter-un)
   Compiling addition v0.1.0 (file:///projects/ajout/addition)
    Finished dev [unoptimized + debuginfo] target(s) in 0.27 secs
     Running target/debug/deps/ajouter_un-f0253159197f7841

running 1 test
test tests::cela_fonctionne ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

     Running target/debug/deps/addition-f88af9d2cc175a5e

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

   Doc-tests ajouter-un

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

<!--
The first section of the output shows that the `it_works` test in the `add-one`
crate passed. The next section shows that zero tests were found in the `adder`
crate, and then the last section shows zero documentation tests were found in
the `add-one` crate. Running `cargo test` in a workspace structured like this
one will run the tests for all the crates in the workspace.
-->

La première section de la sortie indique que le test `cela_fonctionne` de la
crate `ajouter-un` a réussi. La section suivante indique qu'aucun test n'a été
trouvé dans la crate `addition`, et ensuite la dernière section indique elle
aussi qu'aucun test de documentation a été trouvé dans la crate `ajouter-un`.
Lancer `cargo test` dans un espace de travail structuré comme celui-ci va
exécuter les tests pour toutes les crates de cet espace de travail.

<!--
We can also run tests for one particular crate in a workspace from the
top-level directory by using the `-p` flag and specifying the name of the crate
we want to test:
-->

Nous pouvons aussi lancer des tests pour une crate en particulier dans un
espace de travail à partir du dossier du plus haut niveau en utilisant le
drapeau `-p` et en renseignant le nom de la crate que nous voulons tester :

<!--
```text
$ cargo test -p add-one
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running target/debug/deps/add_one-b3235fea9a156f74

running 1 test
test tests::it_works ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

   Doc-tests add-one

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```
-->

```text
$ cargo test -p ajouter-un
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running target/debug/deps/ajouter_un-b3235fea9a156f74

running 1 test
test tests::cela_fonctionne ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

   Doc-tests ajouter-un

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

<!--
This output shows `cargo test` only ran the tests for the `add-one` crate and
didn’t run the `adder` crate tests.
-->

Cette sortie montre que `cargo test` a lancé les tests uniquement pour la
crate `ajouter-un` et n'a pas lancé les tests de la crate `addition`.

<!--
If you publish the crates in the workspace to [crates.io](https://crates.io/),
each crate in the workspace will need to be published separately. The `cargo
publish` command does not have an `--all` flag or a `-p` flag, so you must
change to each crate’s directory and run `cargo publish` on each crate in the
workspace to publish the crates.
-->

Si vous publiez les crates présentes dans l'espace de travail sur
[crates.io](https://crates.io/), chaque crate de l'espace de travail va avoir
besoin d'être publiée de manière séparée. La commande `cargo publish` n'a pas
de drapeau `--all` ou `-p`, donc vous devrez vous rendre dans chaque dossier de
chaque crate et lancer `cargo publish` sur chaque crate présente dans l'espace
de travail pour publier les crates.

<!--
For additional practice, add an `add-two` crate to this workspace in a similar
way as the `add-one` crate!
-->

En guise d'entrainement supplémentaire, ajoutez une crate `ajouter-deux` dans
cet espace de travail de la même manière que nous l'avons fait pour la crate
`ajouter-un` !

<!--
As your project grows, consider using a workspace: it’s easier to understand
smaller, individual components than one big blob of code. Furthermore, keeping
the crates in a workspace can make coordination between them easier if they are
often changed at the same time.
-->

Au fur et à mesure que votre projet se développe, pensez à utiliser un espace
de travail : il est plus facile de comprendre des composants individuels, plus
petits, plutôt qu'un gros tas de code. De plus, garder les crates dans un
espace de travail peut améliorer la collation entre elles si elles sont souvent
modifiées ensemble.
