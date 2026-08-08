# Sky Full of Stars

A single-page romantic night-sky experience, built as a gift.

The page opens on a deep-navy sky, traces a name in the stars, and reveals the
**Aries** constellation — with one heartfelt message waiting each day.

Everything lives in one self-contained `index.html`. No build step, no
dependencies, no framework.

---

## Run it

Open `index.html` in any modern browser. That's it.

To serve it locally instead (PowerShell, no Node required):

```powershell
$root = $PWD
$l = [System.Net.HttpListener]::new()
$l.Prefixes.Add('http://localhost:8087/')
$l.Start()
while ($true) {
  $c = $l.GetContext()
  $p = [Uri]::UnescapeDataString($c.Request.Url.LocalPath)
  if ($p -eq '/') { $p = '/index.html' }
  $f = Join-Path $root $p.TrimStart('/')
  if (Test-Path $f -PathType Leaf) {
    $c.Response.ContentType = 'text/html; charset=utf-8'
    $b = [System.IO.File]::ReadAllBytes($f)
    $c.Response.ContentLength64 = $b.Length
    $c.Response.OutputStream.Write($b, 0, $b.Length)
  } else { $c.Response.StatusCode = 404 }
  $c.Response.OutputStream.Close()
}
```

Then visit <http://localhost:8087>.

---

## What's in it

**A live starfield.** The sky is drawn on a `<canvas>`, not a video — so it never
loops or seams, stays sharp at any resolution, and costs almost nothing to load.
Stars are distributed across depth layers, each with its own twinkle phase and
slow drift that wraps at the screen edge. Shooting stars fire on their own timer
every 6–18 seconds.

**A real constellation.** The four stars of Aries — 41 Arietis, Hamal, Sheratan
and Mesarthim — are plotted from their true right ascension and declination, and
sized by actual stellar magnitude. The shape on screen is the shape in the sky.

**A name written in stars.** On the first visit of a session, background stars
migrate out of the sky, arrange themselves into a name, hold, then drift back and
resume twinkling. The letterforms are sampled from real glyph outlines.

**One message a day.** Clicking the reveal button fades in a frosted-glass card
holding a single message, while the constellation glows brighter. The same
message stays all day; a new one arrives at midnight. The full list is shuffled
through before anything repeats, and the same message never lands two days
running.

---

## Making it yours

The messages are a plain array at the top of the `<script>` block:

```js
const COMPLIMENTS = [
  "The stars agree — you were born to shine.",
  ...
];
```

Add, remove or reword freely — the daily rotation adapts to whatever length the
list is. The name shown in the heading, and the one traced in the stars, are
just as easy to find:

```js
const NAME = "PHENYO";
```

---

## Notes

- Fully responsive: verified with no overflow at 1440×900, 1280×720, 768×1024,
  375×812 and 360×640, with a dedicated two-column layout for phones held
  sideways.
- Honours `prefers-reduced-motion` — the intro, cursor stardust and shooting
  stars all stand down.
- Daily state is kept in `localStorage`; the once-per-session intro uses
  `sessionStorage`. Nothing leaves the browser.
