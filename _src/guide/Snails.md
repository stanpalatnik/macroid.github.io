# Snails

Snails provide a dataflow mini-language for animations and things like that.

## What are they

Snails are almost like [tweaks](Tweaks.html), but are used when changing a widget property takes time.
A snail is defined by a function `WidgetClass ⇒ Future[Unit]`.

## Defining

Snails can be defined using the `Snail` companion object:

```scala
import macroid.Snail

// plays a sound
// and returns a future to track when it ends
def playSound = {
  val done = Promise[Unit]()
  // set up media player
  // play sound
  // when complete, call `done.success(())`
  done.future
}

// set focus and play a sound
val focusLoudly = Snail[View] { view ⇒
  view.setFocus()
  playSound
}
```

## Composing

Snails can be composed by using a funny `+@+` operator (`@` should look like a snail):

```scala
val wink = fadeIn +@+ fadeOut
```

It is also possible to combine snails with tweaks by using either `+@` or `@+` (the `@` should be facing the snail):

```scala
def textAndFade(str: String) =
  text(str) +@ fadeIn

val fadeAndDisappear =
  fadeOut @+ hide
```

## “Snailing”

“Snailing” is a made-up word that denotes the process of applying snails to widgets.
Snailing is very similar to [tweaking](Tweaks.html#tweaking), except it uses another
funny operator (again, `@` should resemble a snail!):

```scala
textView <@~ fadeIn
```

It can be chained:

```scala
textView <@~ fadeIn <@~ fadeOut
```

Snailing can be also mixed with tweaking:

```scala
editText <~ text("foo") <@~ fadeIn <~ enable
```

Snailing returns a [UI action](UiActions.html), to be more specific, a `Ui[Future[...]]`.
Since calling a `run` method of the UI action and getting `Future[Future[...]]` is not very
convenient, a `flatRun` method is provided, that flattens the `Future`s.

## Snailing workflows

With [scala-async](https://github.com/scala/async) multiple snailings can be performed in a sequence.
Note the usage if `flatRun`, which returns a `Future[...]` instead of `Future[Future[...]]`.

```scala
async {
  await((textView <@~ fadeIn).flatRun)
  await((button <@~ fadeOut).flatRun)
}
```

It is possible that a more convenient combinator will be introduced later.

## Standard snails

A number of standard snails are included. Please refer to the [scaladoc](../api/macroid/Snails$.html).
Note that all these snails can be imported with `import macroid.FullDsl._`.