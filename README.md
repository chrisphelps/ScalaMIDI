# ScalaMIDI

## statement

ScalaMIDI is a library for accessing standard [MIDI](http://www.midi.org/) files in the Scala programming language. It is (C)opyright 2013 by Hanns Holger Rutz. All rights reserved. ScalaOSC is released under the [GNU General Public License](https://raw.github.com/Sciss/ScalaMIDI/master/LICENSE) and comes with absolutely no warranties. To contact the author, send an email to `contact at sciss.de`

The example file for Erik Satie's 'Vexations', `vexation.mid`, is (C)opyright by [Reinhard Kopiez](http://musicweb.hmt-hannover.de/satie/) and provided on a Creative Commons attribution (CC BY 3.0) type condition. This file is not included in the published library.

## linking

To link to this library:

    libraryDependencies += "de.sciss" %% "scalamidi" % v

The current version `v` is `"0.1.+"`

## building

ScalaMIDI currently builds against Scala 2.10, using sbt 0.12. It uses the MIDI API from Java (`javax.sound.midi`) under the hood.

## overview

Reading and playing a MIDI file:

```scala

    import de.sciss.midi._

    val sq  = Sequence.read("vexation.mid")
    val pl  = Sequencer.open()
    pl.play(sq)
    pl.stop()
    val t   = sq.tracks(1)  // second of three tracks
    val ev  = t.events      // all events in that track
    val pch = ev.collect { case Event(_, NoteOn(_, pch, _)) => pch }  // pitches
    pch.map(_ % 12).toSet.toList.sorted // pitch classes (all twelve!)
```

Creating an example sequence:

```scala

    val ms  = (64 to 72).flatMap { pch => NoteOn(0, pch, 80) :: NoteOff(0, pch, 0) :: Nil }
    implicit val rate = TickRate(400.0e6)
    val ev  = ms.zipWithIndex.map { case (m, i) => Event((i * 0.25 * rate.value).toLong, m) }
    val mx  = ev.map(_.tick).max
    val t   = Track(ev)
    val sq  = Sequence(Vector(t))
```

## limitations

- This is a very early version
- Currently, there is no support for anything but notes, so no control changes, pitch bend etc.
- There is a time base problem when writing sequences (`sq.write(<path>)`), although playback from within ScalaMIDI is correctly timed.

## links

Note: There is another similarly named library [scala-midi](http://code.google.com/p/scala-midi/) which is completely independent from this project.