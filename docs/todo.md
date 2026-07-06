## 0.2 — consolidation (2026-07)

Docs are consolidated to one dialect (see r&d.md "Consolidation 2026-07"). Impl migration:

* [ ] comments: `;;` → `#`
* [ ] defer: `^` → `;;`
* [ ] exits: `/x` → block-relative `./ ../ .../` (exit block / loop / function)
* [ ] topic: `#` → `$`; remove `$`, `@` from identifier chars
* [ ] writable topic over lvalue ranges: `x[..] |> $ *= 2` (slot semantics, in-place map)
* [ ] `./` emits nothing in comprehensions: `[xs |> (cond ? ./; $)]` (filter)
* [ ] one-armed `?` = statement only, error in value position (currently yields 0); `a ?: b` = nan-coalesce binary
* [ ] scoping: python rule — assignment in fn body makes a local; globals read-only in fns (update vars/loops tests: `c++` case)
* [ ] case-sensitive identifiers (drop case-folding plans)
* [ ] `>>>` = unsigned right shift (as JS); `<<<` = rotl; drop rotr
* [ ] units: predefine π/∞; reject unit defs colliding with number grammar (`1e=`, `1x=`, ...)
* [ ] stdlib: core math without imports (sin, cos, asin, floor, round, abs, sign, sqrt, min, max, exp, log, rand, ...)
* [ ] precedence: document table; `|>`/`?` bodies extend right
* [ ] readme test covers ALL examples (gain, biquad, zzfx, freeverb, floatbeat) — gate against dialect drift
* [ ] backend: spike piezo → jz AST lowering (gain, biquad, zzfx), diff vs direct WAT; adopt at parity (see r&d)

## 0

* [x] make subscript generate lispy tree (wasm, wat, js, ast targets are possible)
  . we need not immediately compile, we need intermediary state to codegenerate after.
  . parsing time can be improved via wasm version;
  * [x] ~~make subscript generate wasm binary for evaluator.~~ nah, later
    * Example: https://crypto.stanford.edu/~blynn/asm/wasm.html
* [x] Watr
  * [x] compiler
  * [x] parser: very basic commands, no legacy
  * [x] Compare vs wabt, wat-compiler
* [x] Parser

* [ ] jasm - JS-compatible min surface

## 0.1

* [x] update subscript
* [x] fix parsing
* [x] a++, a--, a+=1, a-=n
* [x] fix compiling
* [x] account ops for expected number of returns instead of forcing drops
* [x] make `;;` for comments
* [x] make `.` for 'return nothing'
  * [x] undo `.` - make semicolon-based void
* [x] make globals readable, but locals writable
* [x] first bytebeat: viznut
* [x] make `#` as topic placeholder
* [x] make writable globals, init on the first line
* [x] make simple static variables as `*b=a`
  * [x] ~~clone fn static `a()=(), b()=(*clone=a;clone())`~~
  * [x] init detection
  * [x] `nan:` and constant expressions for watr
  * [x] static groups `*(a,b,c)`
* [ ] Decide about way to import
* [ ] Car signalisation gen
* [ ] Metronome gen
* [ ] Other famous sounds/tropes
* [ ] -<, -*, -/
* [x] `^` for deferred
  * [x] preliminary return with defer `f()=(^i++;i>10?./1;)` - likely via block as `f()=(ret;(i>10?ret=1);i++;ret)`
* [x] conciliate returns types
* [x] remove op `info`
* [x] unvoid `a ? b;`
* [x] make `/`, `/a` as function return
* [ ] make intersect `a >< b`
* [ ] make all viznut bytebeats work (ints rotate)
* [ ] make nan as null in fn arguments (`config(a,,b,c)`)
* [ ] range iterator via pipe
* [ ] convert all string instructions to builder calls
* [ ] list comprehension
* [x] ~~make use of `./`, `../` for loops only (not parent)~~ → block-relative exits (see consolidation)
* [ ] all readmes test
* [ ] case-insensitive variables
* [ ] strings
* [x] make use of watr compiler
  * [x] fix watr discrepancies with wabt in lino context
* [x] Add proper scope mechanism (streamline compiler)
* [x] merge analyzer into compile
  + the difference between them is unclear, compiler can map expression results
  + compiler follows scopes anyways, easier to merge scope vars if needed
  + we use desc anyways from analyser all the time
  + we don't test analyser anyways, it's not stable
  + simplifies parent state management
* [x] precompile
  * [x] static precalc (no FLOAT/INT in compiler)
  * [x] units
  * [x] normalize args/array cases (), (a), (a,b)
* [x] static arrays
  * [x] unwrap static ranges as plain sequences
* [x] group read with prop access
* [x] group write with prop access
* [x] pow operator
* [x] use f64 everywhere
* [x] implement array pointer as float64
* [x] ~~`x=1; x=1.0` - add float dup~~
* [x] arity of ops, for proper `(drop)` amount, eg. `a ? b;` has 0 items in stack;
* [x] early return detection
* [ ] while loop
* [x] Readable generated code
* [x] Per-function vars scope: we don't need parens as scope
* [x] Change `,` precedence to be under `=` to allow `^a,b,c`, `x?1,2,3:4,5,6`, `a,b<|x,i->x,i`
* [ ] Include config params properly
* [ ] Sobel https://github.com/mattdesl/wasm-bench
  * [x] `(y1, y2) >= height ? (y1, y2) = height - 1` group ops
  * [ ] `(val0, val1) = data[ptr0 + (x, x1)];`
  * [x] ~~`\` comments~~
  * [ ] optimized array prop access (track static vars / static-length arrays)
* [ ] Biquad
  * [ ] Clamp args
  * [x] Imports - nice `<>` syntax with href internals and options
  * [x] Units
  * [x] basic maths
  * [x] Comments
* [ ] Oka encoder/decoder
* [x] Group operators
  * [x] multiply
  * [x] floats
  * [x] ints
  * [x] binary
  * [x] logical
  * [x] conditions
* [ ] Group operators with unmatching number of args
* [ ] Group operators with skipped values
* [ ] Group operators with range
* [ ] Complex group operators (via heap)
* [ ] Internalize memory: only imported memory is available
* [x] CLI
* [x] get rid of a.1: we can do that via static knowledge
* [ ] dispose static arrays once ref is lost `[1,2,3]`
* [ ] better error messages phrasing (via chatgpt)
* [x] Indent/format output (like mono via watr)
* [ ] static arrays
* [ ] list comprehension
* [x] static expressions optimization
  * [x] static expressions for units: dynamic units
  * [ ] static constants with usage count (ignore from output if usage is 0)
  * [x] a+0 -> a
  * [x] a**0.5
* [ ] optimize pick for simple arg as `pick(global.get x)` - just do `(global.get)` multiple times
* [x] static arrays via `data` section
* [ ] loops
  * [x] range loop in range loop
* [x] comments: must put comment into a previous token, instead of stripping
* [ ] release static arrays if ref is lost, to allocate memory eg `[..100]`
* [x] implement no-ptr buffer lengths: either i64 or 2 values in stack
* [x] ~~refactor `pick(N,arg)` into `dup(arg,3)`~~
* [x] implement computed ranges in lists creation
* [x] ~~implement scopes as either tmp functions or blocks~~ -> just resolve variables
* [x] Implement storing buffer items
* [x] Make all `local.set` / `global.set` a `set(name, value)` function: we don't need to know about tmp/define etc.
  * [x] ~~Think if we need to expose fully-js API for building wasm code, similar to wasmati~~ no
* [x] ~~Test nested scopes variables `(x=0;(y=1);y)`~~
* [x] ~~`(a=1,c; (b=2; c=a+b;); c)` test~~
* [x] ~~Extend i32 to i64 whereever possible~~ why?
* [ ] Test optional arguments
* [ ] Test a,,c = d,e,f; a,b,c = d,,f
* [x] Test if memory grows properly
* [x] Simple loops
* [x] Track memory via runtime variable: check against array len/address
* [ ] All lang reference cases
* [ ] Nice errors: line number to AST nodes?
* [ ] Stub all wrong syntax error cases, like `++a=123;` etc - any operators on the lhs of `=` etc., all permutations
* [ ] maths: sin, cos, log, pow etc.
* [x] *state
  * [x] `*x=[0..10]`
  * [x] `x()=(*i);y()=(x())`
  * [ ] `x()=(*(a,b,c))`
  * [ ] `x(_sin)=(sin() + _sin())`
* [ ] Make `br_if` loops everywhere - it doesn't create control block (more lightweight)
* [ ] Pass fns by reference `x(a,_osc)=(osc(a)+_osc(a))`
* [ ] Readme examples
  * [ ] Audio gain example
  * [ ] Sine gen example
* [ ] Ignore unassigned immediate constructs like `[1,2,3]` or `"abc"`.
* [ ] All FIXMEs
* [ ] customizable float type, int type, array

## 1.0: good stable release

* [ ] All Bytebeats/Floatbeats
  * [ ] classics http://viznut.fi/demos/unix/bytebeat_formulas.txt
  * [ ] https://github.com/darius/bytebeat
  * [ ] https://dollchan.net/bytebeat
  * [ ] http://canonical.org/~kragen/bytebeat/
  * [ ] https://tinyrave.com/
  * [ ] https://llllllll.co/t/bytebeats-a-beginner-s-guide/16491
  * [ ] https://www.reddit.com/r/bytebeat/
  * [ ] https://games.greggman.com/game/html5-bytebeat/
  * [ ] https://github.com/greggman/html5bytebeat#rant
  * [ ] https://github.com/Butterroach/jstebeat
  * [ ] https://butterroach.github.io/jstebeat/
  * [ ] https://sarpnt.github.io/bytebeat-composer
  * [ ] http://wavepot.com/
  * [ ] https://github.com/stellartux/websynth
  * [ ] https://github.com/radavis/bytebeat
* [ ] ZZFX
* [ ] other bytebeat-like library (forgot name)
* [ ] Web-Audio-API
* [ ] Metronome app: variable speed, variable tone

## Studio

* [ ] Code editor
* [ ] Playback
* [ ] Display spectrogram, like glitch
* [ ] Display waveform, like glitch
* [ ] Choose output context: 8k, 44k
* [ ] Select & lock params knobs

## Backlog

* [ ] extend internal arrays as separate `addr` and `len` variables: speeds up perf, allows enhanced precision
* [ ] Processing modules collection

* [ ] Demo page in style of assembly AI with wavefont marquee player

* [ ] @audio/gain module
  * compiles basic son file to multicased function `gain`
  * [ ] wasm gain node: browser
  * [ ] wasm gain node: node

* [ ] sonr expressions
  * `sin(432 + 10*sin(8)) * adsr(0, 0, .4, 0)`

* [ ] Loudness meter for waveplay

* [ ] Repl with compiler selector
* [ ] Web-audio-api
* [ ] musi
* [ ] latr
* [ ] Neural audio effects
* [ ] Essentia

## Test cases

* sonr expressions
* FM, AM signals sin(f + sin(m) * 100), sin(f) * (cos(m) + 1)
* weighted mixing signals sin(a) * .75 + cos(b) * .25
* periodic wave eg. wave(1204) - seed indicates overtone weights
* mixing: `+`, `-`, `*` and `mix(a,b,c)`
* amplifying: `a*x` and `| gain(amt)`
* shifting: `a | delay(±1s)`
* stretching (pitch): `a | rate(1.05)`
* inverting: `-a`
* reversing direction: `a | reverse(x)`?
* join/concat: `a|trim(..2s) + b|delay(2s) + c|delay(4s))`?
* static shift: `a+c`
* multiplexing: `a > 2 ? x : a > 1 ? y : z` and `mux(select, a, b, c, ...)`?
* input: recording, file etc: `mic()`, `file()`
* loop: `%`? `a | repeat(2)`? `a.repeat(2)`? `a(t % 2)`?
* trim: `a / dur`? `dur&&a`? `a(min(t, dur))`?
* cap/limit/compress max/min/clamp?
* pan, channels: `a & b`?
* math stdlib: common function names exposed from `Math`?
* adsr: `a * adsr(params)`? `a | adsr(params)`?
* convolve: `a ^ b`?
* filters `a | lp(f, q)`? `a | filter(type, f, q)`?
* effects: crush, curve, unisson, flanger etc.  `a | crush(param)`?
* map `a | abs(it)`? `a | abs($)`?
* get sample:  `a[x]`? `a.at(x)`? or sample-less fns?
* get channel: `a[0]`?
* get value at a time `a(t)`?
* integrate: `∫(a, dt)`? `acc(a, dt)`?

## Real Use Cases

* Speech enhancer for any videos (from Prabhupada to...)
  * Dereverb
* Feedback suppressor, soft loudness processor for live stream, compressor
*
