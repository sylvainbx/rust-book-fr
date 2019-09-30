<!--
# Error Handling
-->

# Gestion des Erreurs

<!--
Rust’s commitment to reliability extends to error handling. Errors are a fact
of life in software, so Rust has a number of features for handling situations
in which something goes wrong. In many cases, Rust requires you to acknowledge
the possibility of an error and take some action before your code will compile.
This requirement makes your program more robust by ensuring that you’ll
discover errors and handle them appropriately before you’ve deployed your code
to production!
-->

L'engagement de Rust envers la fiabilité concerne aussi la gestion des erreurs.
Les erreurs font partie de la vie des programmes informatiques, c'est pourquoi
Rust a des dispositifs pour gérer les situations où les choses se passent mal.
Dans de nombreux cas, Rust exige que vous anticipiez les erreurs possibles et
que vous preniez des dispositions avant de compiler votre code. Cette exigence
rends votre programme plus résiliant en s'assurant que vous détectiez et gérez
les erreurs correctement avant même que vous déployez votre code en production
!

<!--
Rust groups errors into two major categories: *recoverable* and *unrecoverable*
errors. For a recoverable error, such as a file not found error, it’s
reasonable to report the problem to the user and retry the operation.
Unrecoverable errors are always symptoms of bugs, like trying to access a
location beyond the end of an array.
-->

Rust classe les erreurs dans deux catégories principales : les erreurs
*récupérables* et *irrécupérables*. Les erreurs récupérables se produisent
dans des situations dans lesquelles il est utile de signaler l'erreur à
l'utilisateur et de relancer l'opération, comme par exemple une erreur lorsque
un fichier n'a pas été trouvé.
Les erreurs irrécupérables sont toujours liées à des bogues, comme essayer
d'accéder à un caractère au-delà de la fin d'un tableau.

<!--
Most languages don’t distinguish between these two kinds of errors and handle
both in the same way, using mechanisms such as exceptions. Rust doesn’t have
exceptions. Instead, it has the type `Result<T, E>` for recoverable errors and
the `panic!` macro that stops execution when the program encounters an
unrecoverable error. This chapter covers calling `panic!` first and then talks
about returning `Result<T, E>` values. Additionally, we’ll explore
considerations when deciding whether to try to recover from an error or to stop
execution.
-->

La plupart des langages de programmation ne font pas de distinction entre ces
deux types d'erreurs et les gèrent de la même manière, comme les exceptions.
Rust n'a pas d'exceptions. À la place, il a les valeurs `Result<T, E>` pour les
erreurs récupérables, et la macro `panic!` qui arrête l'exécution quand il
se heurte à des erreurs irrécupérables. Nous allons commencer ce chapitre par
expliquer l'utilisation de `panic!`, puis ensuite les valeurs de retour
`Result<T, E>`. De plus, nous allons voir les arguments à prendre en compte
pour choisir si nous devons essayer de récupérer une erreur ou d'arrêter
l'exécution.
