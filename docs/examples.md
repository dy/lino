# Examples

Sketches in the consolidated dialect (see README reference). Canonical, tested examples live in README; these are working notes. Freeverb, ZZFX, floatbeat moved to README.

## Oscillators

```
1s = 44100;

fract(x) = x % 1;

sine(t, f = 432 -< 20..20k) = sin(2π * f * t);
saw(t, f)   = 1 - 2 * fract(f * t);
tri(t, f)   = abs(1 - (2 * t * f) % 2) * 2 - 1;
sqr(t, f)   = (fract(f * t) < 0.5) * 2 - 1;
pulse(t, f, w = 0.5) = (fract(f * t) < w) * 2 - 1;
noise()     = rand() * 2 - 1;
```

## Delay

Hermite-interpolated delay line, after [opendsp/delay](https://github.com/opendsp/delay/blob/master/index.js).

```
1s = 44100;

delay(x, time = 0.25 -< 0..2, feedback = 0.5 -< 0..1) = (
  *buf = [..2s];
  *i = 0;; i++;

  back = i - time * 1s;
  back < 0 ? back += 2s;
  i0 = floor(back);
  (i_1, i1, i2) = (i0 - 1, i0 + 1, i0 + 2);   # neighbors; negative index wraps

  (y_1, y0, y1, y2) = (buf[i_1], buf[i0], buf[i1], buf[i2]);

  f = back - i0;
  c0 = y0;
  c1 = 0.5 * (y1 - y_1);
  c2 = y_1 - 2.5 * y0 + 2.0 * y1 - 0.5 * y2;
  c3 = 0.5 * (y2 - y_1) + 1.5 * (y0 - y1);

  out = ((c3*f + c2)*f + c1)*f + c0;
  buf[i % 2s] = x + out * feedback;
  out
);
```

## NoPop

Declicker, after [opendsp/nopop](https://github.com/opendsp/nopop/blob/master/index.js).

```
nopop(x, threshold = 0.05, amount = 0.12) = (
  *prev = 0;
  diff = x - prev;
  abs(diff) > threshold ? prev += diff * amount : prev = x;
  prev
);
```

## Step

Gate signal on a bpm grid, after [opendsp/step](https://github.com/opendsp/step/blob/master/index.js).

```
1s = 44100;

step(bpm = 120, sig = 0.25) = (
  *frame = 0;; frame++;
  len = round(60 / bpm * 4 * 1s * sig);
  frame % len == 0
);
```

## Bytebeat drone

```
1s = 44100;

fract(x) = x % 1;
mix(a, b, c) = a * (1 - c) + b * c;
noise(x) = sin((x + 10) * sin((x + 10) ** (fract(x) + 10)));

drone() = (
  *t = 0;; t++;
  time = t / 1s / 4;

  a = 0;
  0..13 |> a += sin((2100 + noise(($ + 2) + floor(time)) * 2500) * time) *
    (1 - fract(time * floor(mix(1, 5, noise(($ + 5.24) + floor(time))))));

  a / 9
);
```

## Gain, k-rate

Block-processing variant of README's gain: volume applied per block.

```
gain(block, volume -< 0..1) = (
  block[..] |> $ *= volume;
);
```
