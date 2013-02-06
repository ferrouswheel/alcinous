alcinous
========

Alcinous is a project to build learning systems composed of modular components.

It is inspired by OpenCog, but is a fresh codebase and is starting from a pure
information theoretic understanding of intelligent agents.

Initially the project will explore the relationship between compression,
building conceptual symbols, and involving feedback to actuators that shape the
input.

## bit-"wise" learning

To allow for rapid exploration of arbitary learning agents, the
first task will be to develop a DSL our python library to declare topologies of
input and output (ala senses and actuators).

At the basic level, we work with bits. This is the unit of information and
naturally fits with a digital computing. While signals in neural structures may
be analog, they can still be represented with an arbitary number bits.

### Assumptions

* We consider raw bit streams as the input to the system.
* We consider raw bit streams as the output from the system.
* Either of the above may in future be replaced with a higher level stream of
symbols for reasons of efficiency, but theoretically they can will always be
reducible to bitstreams.
* We assume temporal progression is a fundamental dimension for the processing of
information, without it there can be no dynamics (only stasis). Additional dimensions can
assist in helping form associative patterns (such as edge detection in
images). Additional dimensions are represented by creating associative links
between inputs and outputs. Thinking of this from the point of view of say
a camera, linking each pixel position to a bit stream, and then linking that
pixel to it's neighbours, will implicitly embed the pixels in their relative
locations.

In the simplest form, we have only the temporal dimension and a single bit a.
This bit arrives on a single neuronal input. In most computing, one can think
of this as a input stream.

Note: I'm aware that in computing, almost everything is built with streams of
data. You can send the display of a LCD screen with a single binary stream, or
receive any media that's shared on the internet regardless of how many
dimensions it's rendered form takes. However, a distinction here is that we
care about the synchronicity between streams. So two binary streams
could be interleaved, but we want the state of each to be accessible at the
same time. Regardless, it is probable that anything built on top of this
structure can be reduced back to the single stream case... which I'll leave to
the theorists.

### Input/Output DSL

```
cluster :hand {
         cluster :finger {
                  repeat 4

              dist * * 1  # all elements in cluster have associativity 1 with each other.
                              # N is number of elements, N(N-1)/2 connections. 
              # repeat this section 10 times
              input :a { repeat 10, speed 10 bps } # relative slow update of 10 bits a second
    }
    cluster :thumb {

          in :b 1 kbps # 1 kbps is default, so is not necessary
          dist :a :b 2
     }
}
```

Where

* `:label` - is an identifier to refer to a cluster/input/output
* `cluster :label {repeat <int>}` - cluster links
* `input :label {[speed <int> bps|kbps], [repeat <int>]}` - define input and rate, repetition
* `dist :label :label <int>` - associativity between labels, :label's can use wildcards.

Naming within clusters uses dot notation for the hierarchy, e.g. for the above:
`:hand.finger.1` refers to the first finger, `:hand.finger.1.a.1` refers to
sense 1 in the repeated `a` group.

As I am not a parser magician, for now I will represent this in python, using
a similar :

```python
cluster('hand', dist=1, [
   cluster('finger',
       repeat=4,
       dist=1,
       [ input(repeat=10, sdist=1, speed=10) ])
   cluster('thumb',
       [ input(repeat=10, speed=10000) ])
   ])
```

### Glossary

It is desired to use terminology from communication and information theory where
possible. No need to rename concepts that already have names!

The definition of baud is "symbols per second"
http://en.wikipedia.org/wiki/Baud - this is distinct from bits per second,
since a channel may have multiple symbols. Baud rate would be a good total
speed measure for a particular DSL given assumptions about stream sampling
rates.

The Nyquist rate is the minimum sampling required to avoid aliasing/fold-over.
The system does not need to read sensors at this rate, it is merely to indicate
potential sampling rates.

