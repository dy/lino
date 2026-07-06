# ⚡︎ piezo ![stability](https://img.shields.io/badge/stability-experimental-black) [![test](https://github.com/dy/piezo/actions/workflows/test.yml/badge.svg)](https://github.com/dy/piezo/actions/workflows/test.yml)

Prototype language designed for signal processing, synthesis and analysis.<br/>
Project is early experimental stage, design decisions must be consolidated.

<!-- [Examples](https://dy.github.io/piezo/examples/) | [Motivation](#motivation) -->


## Reference

```
# Operators
+ - * / % -- ++               # arithmetical (float)
** %% //                      # power, unsigned mod, flooring div
& | ^ ~ >> << >>>             # binary (integer), >>> unsigned as in JS
<<<                           # rotate left
&& || !                       # logical: &&, || return operand, as in JS
> >= < <= == !=               # comparisons (0 / 1)
? : ?:                        # condition, coalesce
x[i] x[]                      # member access, length
a..b a.. ..b ..               # ranges, bind tighter than arithmetic
|> $                          # loop/pipe, topic (current element)
./ ../ .../                   # exit block, parent block (loop), function
>< <>                         # inside, outside
-< -/ -*                      # clamp, normalize, lerp
;; #                          # defer (run after return), comment

# Numbers
16, 0x10, 0o755, 0b0;         # int, hex, oct or binary
16.0, .1, 2e-3;               # float (e is exponent, not a unit)
π, ∞;                         # constants, usable as units: 2π
1k=1000; 1s=44100; 1m=60s;    # units: define once, suffix numbers
10.1k, 2π, 1m30s;             # 10100, 6.283..., 66150

# Variables
foo=1, bar=2.0;               # declare vars
AbC, Δx, x_1;                 # names: alnum, unicode, _ (case-sensitive)
default=1, eval=fn, else=0;   # no reserved words
true = 0b1, false = 0b0;      # eg: alias bools
inf = 1/0, nan = 0/0;         # eg: alias infinity, NaN
x = 1; f() = (x * 2);         # globals are readable anywhere
g() = (x = 2; x);             # assignment in fn makes a local (as python)

# Ranges
0..10;                        # 0 to 9 (10 exclusive)
0.., ..10, ..;                # open ranges
10..1;                        # reverse range
1.08..108.0;                  # float range
(a-1)..(a+1);                 # computed range
0..3 * 2;                     # mapped range: 0*2, 1*2, 2*2
(a,b,c) = 0..3 * 2;           # destructure: a=0, b=2, c=4
a >< 0..10, a <> 0..10;       # inside(a, 0, 10), outside(a, 0, 10);
a -< 0..10, a -<= 0..10;      # clamp(a, 0, 10), a = clamp(a, 0, 10)
a -< ..10, a -< 10..;         # min(a, 10), max(a, 10)
a -* 0..10, a -/ 0..10;       # lerp(a, 0, 10), normalize(a, 0, 10)

# Groups
(a,b,c) = (1,2,3);            # assign: a=1, b=2, c=3
(a,b) = (b,a);                # swap: rhs is stashed, then assigned left-to-right
(a,b,c) = d;                  # duplicate: a=d, b=d, c=d
(a,,b) = (c,d,e);             # skip: a=c, b=e
(a,b) + (c,d);                # group ops distribute: a+c, b+d
(a, b, c)++;                  # group unary: a++, b++, c++
(a,b)[1] = c[2,3];            # props: a[1]=c[2], b[1]=c[3]
(a,..,z) = (1,2,3,4);         # pick: a=1, z=4
a = (b,c,d);                  # positions align: a=b, rest dropped (≠ js comma)
(a,(b,(c))) == (a,b,c);       # groups are always flat

# Arrays
m = [..10];                   # array of 10 elements
m = [..10 |> 2];              # filled with 2
m = [1,2,3,4];                # array of 4 elements
m = [n[..]];                  # copy n
m = [1, 2..4, 5];             # mixed definition
m = [1, [2, 3, [4, m]]];      # nested arrays (tree)
m = [0..4 |> $ ** 2];         # list comprehension
(a, z) = (m[0], m[-1]);       # get by index
(b, .., z) = m[1, 2..];       # get multiple values
length = m[];                 # get length
m[0] = 1;                     # set value
m[2..] = (1, 2..4, n[1..3]);  # set multiple values from offset 2
m[1,2] = m[2,1];              # swap
m[0..] = m[-1..];             # reverse
m[0..] = m[1..,0];            # rotate
[1, 2] + [3];                 # concat [1,2,3]: array ops act on the value
[1, 2] * 3;                   # repeat [1,2,1,2,1,2] (groups distribute instead)

# Strings
hi="Hello";                   # creates static array
string="$<hi>, world!";       # interpolate: "hello world"
string[1, 3..5, -2];          # pick elements: 'e', 'lo', 'd'
string[0..5];                 # substring: 'Hello'
string[-1..0];                # reversed: '!dlrow ,olleH'
string[];                     # length: 13
"a" + "b";                    # concat: "ab"
"a" * 3;                      # repeat: "aaa"

# Conditions
a ? b : c;                    # if a then b else c
a ? b;                        # if a then b (statement, no value)
x = a ? b;                    # error: value position needs else
a && b;                       # b if a, else 0 - fine in sums
a ?: b;                       # a, unless a is nan - then b (as js ??)
x = arg ?: 0;                 # eg: default for omitted arg
val = (                       # switch: exit block with a value
  a == 1 ? ./1;               # if a == 1 then val = 1
  a >< 2..4 ? ./2;            # if a in 2..4 then val = 2
  3                           # otherwise 3
);
a ? ./b;                      # exit block with b: in fn body = return

# Loops
(a, b, c) |> f($);            # for each item in a, b, c do f(item)
x[..] |> $ *= 2;              # $ over lvalue range is a writable slot: map in place
x[..] |> $ = lpf($, 500, 1);  # process block through stateful fn
y = x |> f($) |> g($);        # scalar is a sequence of one: y = g(f(x))
(i = 10..) |> (               # named binding (parens required), descend over range
  i < 5 ? ./;                 # skip iteration (continue)
  i < 0 ? ../;                # exit loop (break)
);
(i = 0..w) |> (               # nest iterations: name outer, $ is innermost
  (j = 0..h) |> f(i, j);      # f(x,y)
);
(x,,y) = (a,b,c) |> $ * 2;    # capture result: x = a*2, y = c*2
.. |> i < 10 ? i++ : ../;     # while i < 10: i++
s = 0; xs[..] |> s += $;      # fold: accumulate through the loop
m = [0..9 |> ($ <> 3..6 ? ./; $)]; # filter: ./ emits nothing

# Functions
double(n) = n*2;              # define a function
times(m = 1, n -< 1..) = (    # optional, clamped arg
  n == 0 ? ./n;               # early return
  m * n;                      # returns last statement
);
times(3,2);                   # 6
times(4), times(,5);          # 4, 5: optional, skipped arg
dup(x) = (x,x);               # return multiple
(a,b) = dup(b);               # destructure
x() = (a=1, b=2; a+b);        # assigned names are locals, last statement returns
fn() = ( x ;; log(x) );       # defer: log(x) after returning x
f(a, cb) = cb(a[0]);          # array, func args

# State vars
a() = ( *i=0; i++ );          # i persists value
a(), a();                     # 0, 1
a.i = 0;                      # reset state
*a1 = a;                      # clone function
a(), a(); a1(), a1();         # 0, 1; 0, 1;
f() = ( *i=0;; i++; i );      # with defer: returns i, then increments

# Export
x, y, z;                      # exports last statement
```


## Examples

<details>
<summary><strong>Gain</strong></summary>

Amplify k-rate block of samples.

```
gain(
  block,                          # block is an array argument
  volume -< 0..100                # volume is clamped to 0..100 range
) = (
  block[..] |> $ *= volume;       # $ is a writable slot: map in place
);

gain([0..5 * 0.1], 2);            # 0, .2, .4, .6, .8, 1
```

</details>


<details>
<summary><strong>Biquad Filter</strong></summary>

A-rate (per-sample) biquad filter processor.

```
1s = 44100;
1k = 1000;

lpf(
  x0,
  freq = 100 -< 1..10k,
  Q = 1.0 -< 0.001..3.0
) = (
  # filter state
  *(x1, y1, x2, y2) = 0;

  # shift state after return (defer)
  ;; (x1, x2) = (x0, x1), (y1, y2) = (y0, y1);

  # lpf formula
  w = 2π * freq / 1s;
  (sin_w, cos_w) = (sin(w), cos(w));
  α = sin_w / (2.0 * Q);

  (b0, b1) = ((1.0 - cos_w) / 2.0, 1.0 - cos_w);
  b2 = b0;                      # in-group (..., b0) would stash the old b0 (0)
  (a0, a1, a2) = (1.0 + α, -2.0 * cos_w, 1.0 - α);
  (b0, b1, b2, a1, a2) /= a0;

  y0 = b0*x0 + b1*x1 + b2*x2 - a1*y1 - a2*y2
);

samples = [0, .1, .3, .5, .3, .1];
samples[..] |> $ = lpf($, 108, 5);  # filter block in place
```

</details>

<details>
<summary><strong>ZZFX</strong></summary>

Generates ZZFX's [coin sound](https://codepen.io/KilledByAPixel/full/BaowKzv) `zzfx(...[,,1675,,.06,.24,1,1.82,,,837,.06])`.

```
1s = 44100;
1ms = 1s / 1000;

# waveform generators
oscillator = [
  tri(phase) = 1 - 4 * abs( round(phase/2π) - phase/2π ),
  sine(phase) = sin(phase)
];

# per-sample adsr envelope
adsr(
  x,
  a -< 1ms..,                   # attack, min 1ms to prevent click
  d, s, r,                      # decay, sustain, release
  sv = 1                        # sustain volume
) = (
  *i = 0;; i++;                 # internal counter
  t = i / 1s;
  total = a + d + s + r;

  t >= total ? 0 : (
    t < a ? t/a :               # attack
    t < a + d ?                 # decay
    1-((t-a)/d)*(1-sv) :        # decay falloff
    t < a + d + s ?             # sustain
    sv :                        # sustain volume
    (total - t)/r * sv          # release
  ) * x
);

# waveshaper
curve(x, amt = 1.82 -< 0..10) = sign(x) * abs(x) ** amt;

# coin = triangle with pitch jump, one sample per call
coin(freq=1675, jump=freq/2, delay=0.06, shape=0) = (
  *i = 0;; i++;
  *phase = 0;; phase += (freq + (t > delay && jump)) * 2π / 1s;
  t = i / 1s;

  oscillator[shape](phase)      # scalar pipe: each stage rebinds $
    |> adsr($, 0, 0, .06, .24)
    |> curve($, 1.82)
);

# render a block
out = [..1024];
out[..] |> $ = coin();
out
```

</details>


<details>
<summary><strong>Freeverb</strong></summary>

## [Freeverb](https://github.com/opendsp/freeverb/blob/master/index.js)

```
<./combfilter.z#comb>;
<./allpass.z#allpass>;

1s = 44100;

*(c1,c2,c3,c4,c5,c6,c7,c8) = comb;    # 8 comb instances: clones own state
*(p1,p2,p3,p4) = allpass;             # 4 allpass instances

combs = [c1,c2,c3,c4,c5,c6,c7,c8];
sizes = [1116,1188,1277,1356,1422,1491,1557,1617];

reverb(input, room=0.5, damp=0.5) = (
  wet = 0;
  0..sizes[] |> wet += combs[$](input, sizes[$], room, damp);  # parallel combs, folded by sum

  wet |> p1($, 225, room)             # series allpasses: scalar pipe chain
      |> p2($, 556, room)
      |> p3($, 441, room)
      |> p4($, 341, room)
);
```

Features:

* _function clones_ − `*c1 = comb` copies a function together with its state: per-instance delay lines without objects.
* _accumulator fold_ − `0..sizes[] |> wet += ...` reduces a sequence with a plain loop, no fold operator needed.
* _scalar pipe_ − a scalar is a sequence of one: `wet |> p1($, ...)` is `p1(wet, ...)`, stages chain like series effects.

</details>


<details>
<summary><strong>Floatbeat</strong></summary>

### [Floatbeat](https://dollchan.net/bytebeat/index.html#v3b64fVNRS+QwEP4rQ0FMtnVNS9fz9E64F8E38blwZGvWDbaptCP2kP3vziTpumVPH0qZyXzfzHxf8p7U3aNJrhK0rYHfgHAOZZkrlVVu0+saKbd5dTXazolRwnvlKuwNvvYORjiB/LpyO6pt7XhYqTNYZ1DP64WGBYgczuhAQgpiTXEtIwP29pteBZXqwTrB30jwc7i/i0jX2cF8g2WIGKlhriTRcPjSvcVMBn5NxvgCOc3TmqZ7/IdmmEnAMkX2UPB3oMHdE9WcKqVK+i5Prz+PKa98uOl60RgE6zP0+wUr+qVpZNsDUjKhtyLkKvS+LID0FYVSrJql8KdSMptKKlx9eTIbcllvdf8HxabpaJrIXEiycV7WGPeEW9Y4v5CBS07WBbUitvRqVbg7UDtQRRG3dqtZv3C7bsBbFUVcALvwH86MfSDws62fD7CTb0eIghE/mDAPyw9O9+aoa9h63zxXl2SW/GKOFNRyxbyF3N+FA8bPyzFb5misC9+J/XCC14nVKfgRQ7RY5ivKeKmmjOJMaBJSbEZJoiZZMuj2pTEPGunZhqeatOEN3zadxrXRmOw+AA==)

Transpiled floatbeat/bytebeat song:

```
1s = 44100;

fract(x) = x % 1;
mix(a, b, c) = (a * (1 - c)) + (b * c);
tri(x) = 2 * asin(sin(x)) / π;
noise(x) = sin((x + 10) * sin((x + 10) ** (fract(x) + 10)));
melodytest(time) = (
  melodyString = "00040008";
  melody = 0;

  0..5 |> (
    melody += tri(
      time * mix(
        200 + ($ * 900),
        500 + ($ * 900),
        melodyString[floor(time * 2) % melodyString[]] / 16
      )
    ) * (1 - fract(time * 4))
  );

  melody
);
hihat(time) = noise(time) * (1 - fract(time * 4)) ** 10;
kick(time) = sin((1 - fract(time * 2)) ** 17 * 100);
snare(time) = noise(floor(time * 108000)) * (1 - fract(time + 0.5)) ** 12;
melody(time) = melodytest(time) * fract(time * 2) ** 6;

song() = (
  *t=0;; t++;
  time = t / 1s;
  (kick(time) + snare(time)*.15 + hihat(time)*.05 + melody(time)) / 4
)
```

Features:

* _string literal_ − `"abc"` is a static array of char codes.
* _length operator_ − `items[]` returns number of items of an array, group, string or range.
* _stdlib_ − core math (`sin`, `asin`, `floor`, `abs`, ...) is available without imports.


</details>

<!--
* [Freeverb](/examples/freeverb.s)
* [Floatbeat](/examples/floatbeat.s)
* [Complete ZZFX](/examples/zzfx.s)

See [all examples](/examples) -->


## Usage

_piezo_ is available as CLI or JS package.

`npm i -g piezo`

### CLI

```sh
piezo source.z -o dest.wasm
```

This produces compiled WASM binary.

### JS

```js
import piezo from 'piezo'

// create wasm arrayBuffer
const buffer = piezo.compile(`
  n=1;
  mult(x) = x*PI;
  arr=[1, 2, sin(1.08)];
  mult, n, arr;
`, {
  // js objects or paths to files
  imports: {
    math: Math,
    mylib: './path/to/my/lib.z'
  },
  // optional: import memory
  memory: true
})

// create wasm instance
const module = new WebAssembly.Module(buffer)
const instance = new WebAssembly.Instance(module, {
  imports: {
    math: Math,
    // imported memory
    memory: new WebAssembly.Memory({
      initial: 10,
      maximum: 100,
    })
  }
})

// use API
const { mult, n, arr, memory } = instance.exports

// number exported as global
n.value = 2;

// function exported directly
mult(108)

// array is a pointer to memory, get values via
const arrValues = new Float64Array(arr, memory)
```


## Motivation

Audio processing has no cross-platform solution, every environment deals with audio differently, many envs don't have audio processing at all.
The _Web Audio API_ has unpredictable pauses, glitches and so on, so <q>audio is better handled in WASM worklet</q> ([@stagas](https://github.com/stagas)).

_Piezo_ attempts to provide a common layer. It is also a personal take in language design - grounded in common syntax, exploring new features like syntax groups, ranges, multiple returns, pipeline, state vars, no-OOP functional style.

<!-- WASM target gives performance and portability - browsers, [audio/worklets](https://developer.mozilla.org/en-US/docs/Web/API/AudioWorkletProcessor/process), web-workers, nodejs, [embedded systems](https://github.com/bytecodealliance/wasm-micro-runtime) etc. -->


<!--
## Projects using piezo

* [web-audio-api](https://github.com/audiojs/web-audio-api)
* [audiojs](https://github.com/audiojs/)
-->


## Principles

* _Minimal_: maximal expressivity with short syntax.
* _Intuitive_: common base, familiar patterns, visual hints.
* _No keywords_: chars for vars, symbols for operators, real i18l code.
* _Space-agnostic_: spacing changes don't change meaning (strings, line comments aside).
* _Explicit_: no implicit globals, no wildcard imports, no hidden file conventions (eg. `package.json`).
* _Inferred types_: derived by usage, focus on logic over language.
* _Normalized AST_: no complex parsing rules, just unary, binary or n-ary operators.
* _Performant_: fast compile, fast execution, good for live envs.
* _No runtime_: statically analyzable, no OOP, no dynamic structures, no lamdas.
* _No waste_: linear memory, fixed heap, no GC.
* _Low-level_: no fancy features beyond math and buffers, embeddable.
* _Readable output_: produces readable WebAssembly text, can serve as meta-language.
* _Minimal footprint_: minimally possible produced WASM output, no heavy workarounds.



### Inspiration

[_mono_](https://github.com/stagas/mono), [_zzfx_](https://killedbyapixel.github.io/ZzFX/), [_bytebeat_](https://sarpnt.github.io/bytebeat-composer/), [_glitch_](https://github.com/naivesound/glitch), [_hxos_](https://github.com/stagas/hxos), [_min_](https://github.com/r-lyeh/min), [_roland_](https://github.com/DenialAdams/roland), [_porffor_](https://github.com/CanadaHonk/porffor)

### Acknowledgement

* @stagas for initial drive & ideas

<p align=center><a href="https://github.com/krsnzd/license/">ॐ</a></p>
