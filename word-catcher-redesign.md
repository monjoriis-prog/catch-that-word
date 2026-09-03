# Word Catcher — full redesign brief

Three changes to index.html: a mint theme across every screen, animated pet
drawings replacing the emoji, and the text colours that go with a light
background.

Work in the order below. Stop after each step so I can look before you
continue.

Keep every pet's existing name (Sparkle, Flutter, and the rest), keep which
animal each name maps to, and keep all rarity and unlock rules exactly as
they are. Nothing about how pets are earned changes here.

---

# Step 1 — The mint theme

Every screen uses this palette. No screen keeps the old dark indigo.

    --mint-bg:    #e0f2e5    page background, every screen
    --card:       #fffdf7    buttons, word pills, panels
    --ink:        #1a1730    all outlines and primary text
    --ink-soft:   #5a6b5e    subtitles, secondary text
    --ink-muted:  #6b7c6f    button blurbs, hints
    --gold:       #ffd158    reward buttons, highlights
    --coral:      #ff6b6b    lives, wrong answers
    --leaf:       #4f9e4a    correct answers

## The rule that holds it together

Every card, button, pill, panel, and pet gets a 2px `--ink` outline. On a
light background, outlines are what create structure — the old design used
a dark background for that, and it is gone.

## Screen by screen

**Start screen**
- Background `--mint-bg`. Title in `--ink`, subtitle in `--ink-soft`.
- Level buttons: `--card` fill, 2px `--ink` outline. Level name on the left
  in `--ink`, blurb on the right in `--ink-muted`.
- The My Pets button is the only solid-filled button here: `--gold` with an
  `--ink` outline. Change its label to show the total, like
  "My Pets (8 of 20)". Seeing the denominator is the point.
- Remove the star canvas entirely. Stars read as dust on a light
  background. If the sky looks empty, a few scattered dots in a slightly
  darker mint would do the same job.

**Play screen**
- The clue text at the top was gold on dark. Gold on mint is unreadable —
  make it `--ink`.
- Falling word pills: `--card` fill, 2px `--ink` outline, `--ink` text.
- Correct catch flashes `--leaf`, wrong catch flashes `--coral`. Both keep
  their outline.
- Lives stay `--coral` but gain an `--ink` outline.
- Confetti colours stay as they are — they read fine on mint.

**Everything else**
Check each of these and fix any text still using a colour meant for the old
dark background: the level-up banner, the wrong-answer panel, the review
screen, game over, the pet reward popup, and the login and account screens.

Any text that is currently light-coloured so it would show on indigo is now
invisible. That is the main thing to hunt for.

Tell me when this is done. Do not start step 2.

---

# Step 2 — The animation classes

Add this CSS once. It drives every pet, so there is no per-animal animation
code anywhere.

```css
.bob,.blink,.wl,.wr,.fl,.fr,.el,.er{transform-box:fill-box}
.bob{animation:bob 2.6s ease-in-out infinite}
@keyframes bob{0%,100%{transform:translateY(0)}50%{transform:translateY(-2.5px)}}
.blink{animation:blink 2.2s infinite;transform-origin:center}
@keyframes blink{0%,84%,100%{transform:scaleY(1)}92%{transform:scaleY(.1)}}
.wl{transform-origin:right center;animation:wl 1.8s ease-in-out infinite}
.wr{transform-origin:left center;animation:wr 1.8s ease-in-out infinite}
@keyframes wl{0%,100%{transform:rotate(0)}50%{transform:rotate(-16deg)}}
@keyframes wr{0%,100%{transform:rotate(0)}50%{transform:rotate(16deg)}}
.fl{transform-origin:right bottom;animation:wl .22s ease-in-out infinite}
.fr{transform-origin:left bottom;animation:wr .22s ease-in-out infinite}
.el{transform-origin:center bottom;animation:el 3.4s ease-in-out infinite}
.er{transform-origin:center bottom;animation:er 3.4s ease-in-out infinite .3s}
@keyframes el{0%,80%,100%{transform:rotate(0)}88%{transform:rotate(-13deg)}}
@keyframes er{0%,80%,100%{transform:rotate(0)}88%{transform:rotate(13deg)}}
@media (prefers-reduced-motion:reduce){
  .bob,.blink,.wl,.wr,.fl,.fr,.el,.er{animation:none}
}
```

## Three things that will break if you skip them

**`transform-box: fill-box` is required.** Without it, shapes scale and
rotate from the SVG's corner instead of their own centre, and the eyes fly
off across the screen.

**The bob needs its own wrapper group.** An animation that sets `transform`
overwrites any `transform` already on that element. So each pet is: an
outer group carrying the position and size, wrapping a group with
`class="bob"`, wrapping the drawing. Get this wrong and every pet piles up
in the corner at the wrong size.

**Stagger the delays.** Give each pet a random `animation-delay` between 0
and 2 seconds on both its bob and its blink. Twenty animals blinking in
unison looks mechanical, not alive.

## Which class goes where

- `.bob` on every pet's wrapper group, all twenty
- `.blink` on the eye shapes — already marked in the drawings below
- `.wl` / `.wr` slow wings: butterfly, owl, parrot, dragon
- `.fl` / `.fr` fast wings: bee only
- `.el` / `.er` twitching ears: cat, dog, rabbit, fox, koala, lion, hamster

The wing and ear classes are already applied in the drawings below. You
only need to add `.bob` on the wrappers and the staggered delays.

---

# Step 3 — The pet drawings

Replace the emoji with these. Each block is the inner markup for
`viewBox="0 0 64 64"`. Wrap each in an `<svg viewBox="0 0 64 64">` and set
the size with CSS at each place it appears, so one drawing works small in a
list and large on the collection screen. Do not add a background rect
inside any pet.

Store them in one object keyed by animal name, so each drawing exists once
and is looked up rather than pasted at every call site.

## Sizing

The pets were too small in the old layout. Render them at roughly 64 to 70
pixels on the start screen and 100 or more on the collection screen.

At that size, twenty pets will not fit in two columns of ten on the start
screen. Pick one: show only the pets she has actually earned so the columns
fill up as she plays, or show a handful and keep the full set on the
collection screen. The first is better — an empty slot is a reason to keep
playing. Ask me before you decide.

## The drawings

### cat
```svg
<polygon points="17,26 20,10 32,23" fill="#ffd158" stroke="#1a1730" stroke-width="2.2" stroke-linejoin="round"/>
<polygon points="47,26 44,10 32,23" fill="#ffd158" stroke="#1a1730" stroke-width="2.2" stroke-linejoin="round"/>
<circle cx="32" cy="38" r="18" fill="#ffd158" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="25" cy="34" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<ellipse cx="39" cy="34" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<polygon points="32,40 28,45 36,45" fill="#e0708f" stroke="#1a1730" stroke-width="1.4" stroke-linejoin="round"/>
<path d="M32 45 Q27 51 22 47 M32 45 Q37 51 42 47" fill="none" stroke="#1a1730" stroke-width="1.8" stroke-linecap="round"/>
```

### dog
```svg
<ellipse cx="13" cy="33" rx="6" ry="12" fill="#b8834f" stroke="#1a1730" stroke-width="2.2" class="el"/>
<ellipse cx="51" cy="33" rx="6" ry="12" fill="#b8834f" stroke="#1a1730" stroke-width="2.2" class="er"/>
<circle cx="32" cy="38" r="18" fill="#d9a06a" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="32" cy="46" rx="10" ry="7" fill="#f0dbc2" stroke="#1a1730" stroke-width="1.4"/>
<ellipse cx="26" cy="34" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<ellipse cx="38" cy="34" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<ellipse cx="32" cy="42" rx="3.4" ry="2.6" fill="#1a1730"/>
```

### rabbit
```svg
<ellipse cx="25" cy="15" rx="5" ry="15" fill="#f9a0cf" stroke="#1a1730" stroke-width="2.2" class="el"/>
<ellipse cx="39" cy="15" rx="5" ry="15" fill="#f9a0cf" stroke="#1a1730" stroke-width="2.2" class="er"/>
<circle cx="32" cy="40" r="17" fill="#f9a0cf" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="25" cy="37" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<ellipse cx="39" cy="37" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<ellipse cx="32" cy="45" rx="3.4" ry="2.6" fill="#d9668a" stroke="#1a1730" stroke-width="1.4"/>
```

### frog
```svg
<circle cx="20" cy="22" r="8" fill="#5fdcb0" stroke="#1a1730" stroke-width="2.2"/>
<circle cx="44" cy="22" r="8" fill="#5fdcb0" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="32" cy="41" rx="20" ry="15" fill="#5fdcb0" stroke="#1a1730" stroke-width="2.2"/>
<circle cx="20" cy="21" r="4" fill="#ffffff" stroke="#1a1730" stroke-width="1.4"/>
<circle cx="44" cy="21" r="4" fill="#ffffff" stroke="#1a1730" stroke-width="1.4"/>
<circle cx="20" cy="21" r="2" fill="#1a1730" class="blink"/>
<circle cx="44" cy="21" r="2" fill="#1a1730" class="blink"/>
<path d="M18 46 Q32 56 46 46" fill="none" stroke="#1a1730" stroke-width="2" stroke-linecap="round"/>
```

### fox
```svg
<polygon points="16,28 19,10 31,25" fill="#ff7f45" stroke="#1a1730" stroke-width="2.2" stroke-linejoin="round" class="el"/>
<polygon points="48,28 45,10 33,25" fill="#ff7f45" stroke="#1a1730" stroke-width="2.2" stroke-linejoin="round" class="er"/>
<ellipse cx="32" cy="34" rx="19" ry="15" fill="#ff7f45" stroke="#1a1730" stroke-width="2.2"/>
<polygon points="18,40 46,40 32,58" fill="#ff7f45" stroke="#1a1730" stroke-width="2.2" stroke-linejoin="round"/>
<polygon points="25,45 39,45 32,56" fill="#fff4ea" stroke="#1a1730" stroke-width="1.4" stroke-linejoin="round"/>
<ellipse cx="25" cy="32" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<ellipse cx="39" cy="32" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<circle cx="32" cy="53" r="3" fill="#1a1730"/>
```

### panda
```svg
<circle cx="18" cy="22" r="8" fill="#1a1730"/>
<circle cx="46" cy="22" r="8" fill="#1a1730"/>
<circle cx="32" cy="38" r="18" fill="#f5f2ff" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="25" cy="35" rx="5" ry="6" fill="#1a1730"/>
<ellipse cx="39" cy="35" rx="5" ry="6" fill="#1a1730"/>
<circle cx="25" cy="34" r="1.8" fill="#f5f2ff" class="blink"/>
<circle cx="39" cy="34" r="1.8" fill="#f5f2ff" class="blink"/>
<ellipse cx="32" cy="44" rx="3.4" ry="2.6" fill="#1a1730"/>
```

### koala
```svg
<circle cx="14" cy="26" r="9" fill="#b8b5c9" stroke="#1a1730" stroke-width="2.2" class="el"/>
<circle cx="50" cy="26" r="9" fill="#b8b5c9" stroke="#1a1730" stroke-width="2.2" class="er"/>
<circle cx="14" cy="26" r="5" fill="#d9d5e8"/>
<circle cx="50" cy="26" r="5" fill="#d9d5e8"/>
<circle cx="32" cy="40" r="17" fill="#b8b5c9" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="32" cy="44" rx="5" ry="7" fill="#4a4560"/>
<ellipse cx="24" cy="36" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<ellipse cx="40" cy="36" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
```

### owl
```svg
<polygon points="17,26 20,8 31,24" fill="#a78bfa" stroke="#1a1730" stroke-width="2.2" stroke-linejoin="round"/>
<polygon points="47,26 44,8 33,24" fill="#a78bfa" stroke="#1a1730" stroke-width="2.2" stroke-linejoin="round"/>
<ellipse cx="11" cy="38" rx="6" ry="14" fill="#8768f0" stroke="#1a1730" stroke-width="2.2" class="wl"/>
<ellipse cx="53" cy="38" rx="6" ry="14" fill="#8768f0" stroke="#1a1730" stroke-width="2.2" class="wr"/>
<ellipse cx="32" cy="38" rx="18" ry="20" fill="#a78bfa" stroke="#1a1730" stroke-width="2.2"/>
<circle cx="25" cy="32" r="7.5" fill="#ffffff" stroke="#1a1730" stroke-width="1.6"/>
<circle cx="39" cy="32" r="7.5" fill="#ffffff" stroke="#1a1730" stroke-width="1.6"/>
<circle cx="25" cy="32" r="3.2" fill="#1a1730" class="blink"/>
<circle cx="39" cy="32" r="3.2" fill="#1a1730" class="blink"/>
<polygon points="32,38 27,47 37,47" fill="#ffd158" stroke="#1a1730" stroke-width="1.4" stroke-linejoin="round"/>
```

### penguin
```svg
<ellipse cx="32" cy="37" rx="17" ry="21" fill="#3f3a70" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="32" cy="41" rx="10.5" ry="15" fill="#f5f2ff"/>
<circle cx="26" cy="27" r="3.6" fill="#f5f2ff"/>
<circle cx="38" cy="27" r="3.6" fill="#f5f2ff"/>
<circle cx="26" cy="27" r="1.7" fill="#1a1730" class="blink"/>
<circle cx="38" cy="27" r="1.7" fill="#1a1730" class="blink"/>
<polygon points="32,33 26,38 38,38" fill="#ff7f45" stroke="#1a1730" stroke-width="1.4" stroke-linejoin="round"/>
<ellipse cx="24" cy="58" rx="7" ry="4" fill="#ff7f45" stroke="#1a1730" stroke-width="1.4"/>
<ellipse cx="40" cy="58" rx="7" ry="4" fill="#ff7f45" stroke="#1a1730" stroke-width="1.4"/>
```

### butterfly
```svg
<ellipse cx="18" cy="27" rx="13" ry="10" fill="#ff7f45" stroke="#1a1730" stroke-width="2.2" class="wl"/>
<ellipse cx="46" cy="27" rx="13" ry="10" fill="#ff7f45" stroke="#1a1730" stroke-width="2.2" class="wr"/>
<ellipse cx="21" cy="47" rx="10" ry="8" fill="#ffb37a" stroke="#1a1730" stroke-width="2.2" class="wl"/>
<ellipse cx="43" cy="47" rx="10" ry="8" fill="#ffb37a" stroke="#1a1730" stroke-width="2.2" class="wr"/>
<circle cx="15" cy="25" r="3" fill="#fff4ea"/>
<circle cx="49" cy="25" r="3" fill="#fff4ea"/>
<ellipse cx="32" cy="38" rx="3.4" ry="16" fill="#1a1730"/>
<path d="M30 23 Q25 13 19 11 M34 23 Q39 13 45 11" fill="none" stroke="#1a1730" stroke-width="1.8" stroke-linecap="round"/>
```

### turtle
```svg
<circle cx="32" cy="15" r="7.5" fill="#8fd47f" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="28" cy="13" rx="1.6" ry="2" fill="#1a1730" class="blink"/>
<ellipse cx="36" cy="13" rx="1.6" ry="2" fill="#1a1730" class="blink"/>
<ellipse cx="12" cy="50" rx="7" ry="5" fill="#8fd47f" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="52" cy="50" rx="7" ry="5" fill="#8fd47f" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="32" cy="38" rx="22" ry="16" fill="#4f9e4a" stroke="#1a1730" stroke-width="2.2"/>
<circle cx="24" cy="34" r="4.5" fill="#6fbf68" stroke="#1a1730" stroke-width="1.4"/>
<circle cx="40" cy="34" r="4.5" fill="#6fbf68" stroke="#1a1730" stroke-width="1.4"/>
<circle cx="32" cy="44" r="4.5" fill="#6fbf68" stroke="#1a1730" stroke-width="1.4"/>
```

### octopus
```svg
<path d="M14 42 Q14 18 32 18 Q50 18 50 42 Z" fill="#d977b0" stroke="#1a1730" stroke-width="2.2" stroke-linejoin="round"/>
<circle cx="25" cy="32" r="4.5" fill="#ffffff" stroke="#1a1730" stroke-width="1.4"/>
<circle cx="39" cy="32" r="4.5" fill="#ffffff" stroke="#1a1730" stroke-width="1.4"/>
<circle cx="25" cy="32" r="2.2" fill="#1a1730" class="blink"/>
<circle cx="39" cy="32" r="2.2" fill="#1a1730" class="blink"/>
<path d="M17 42 Q12 54 18 60 M25 43 Q21 55 26 61 M32 43 Q32 55 32 61 M39 43 Q43 55 38 61 M47 42 Q52 54 46 60" fill="none" stroke="#d977b0" stroke-width="5" stroke-linecap="round"/>
```

### unicorn
```svg
<polygon points="32,6 26,26 38,26" fill="#ffd158" stroke="#1a1730" stroke-width="1.8" stroke-linejoin="round"/>
<polygon points="16,28 20,14 29,26" fill="#f5f2ff" stroke="#1a1730" stroke-width="2" stroke-linejoin="round"/>
<circle cx="32" cy="40" r="17" fill="#f5f2ff" stroke="#1a1730" stroke-width="2.2"/>
<path d="M45 26 Q57 34 52 50 Q46 36 40 30 Z" fill="#f9a0cf" stroke="#1a1730" stroke-width="1.8" stroke-linejoin="round"/>
<ellipse cx="25" cy="37" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<ellipse cx="32" cy="47" rx="7" ry="5" fill="#fdd3e8" stroke="#1a1730" stroke-width="1.4"/>
<circle cx="29" cy="45" r="1.5" fill="#1a1730"/>
```

### dolphin
```svg
<path d="M10 43 Q22 20 49 25 Q60 28 57 38 Q44 53 24 50 Z" fill="#5ab8e8" stroke="#1a1730" stroke-width="2.2" stroke-linejoin="round"/>
<path d="M24 48 Q40 54 55 40 Q42 52 26 50 Z" fill="#a8dcf5"/>
<polygon points="33,25 40,9 47,27" fill="#3f9ed0" stroke="#1a1730" stroke-width="2" stroke-linejoin="round"/>
<polygon points="11,43 3,32 7,49" fill="#3f9ed0" stroke="#1a1730" stroke-width="2" stroke-linejoin="round"/>
<circle cx="48" cy="33" r="2.6" fill="#1a1730" class="blink"/>
<path d="M53 40 Q57 43 56 46" fill="none" stroke="#1a1730" stroke-width="1.6" stroke-linecap="round"/>
```

### bee
```svg
<ellipse cx="26" cy="17" rx="10" ry="6.5" fill="#ffffff" stroke="#1a1730" stroke-width="1.8" class="fl"/>
<ellipse cx="44" cy="17" rx="10" ry="6.5" fill="#ffffff" stroke="#1a1730" stroke-width="1.8" class="fr"/>
<ellipse cx="38" cy="40" rx="17" ry="13.5" fill="#ffd158" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="34" cy="40" rx="3" ry="12" fill="#1a1730"/>
<ellipse cx="44" cy="40" rx="3" ry="10.5" fill="#1a1730"/>
<circle cx="16" cy="37" r="8.5" fill="#1a1730"/>
<circle cx="13" cy="34" r="2.4" fill="#ffffff" class="blink"/>
<path d="M11 28 Q6 20 3 18" fill="none" stroke="#1a1730" stroke-width="1.8" stroke-linecap="round"/>
```

### parrot
```svg
<polygon points="32,52 25,62 41,62" fill="#5fdcb0" stroke="#1a1730" stroke-width="1.8" stroke-linejoin="round"/>
<ellipse cx="34" cy="40" rx="14" ry="18" fill="#ff6b6b" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="40" cy="42" rx="7" ry="12" fill="#ffd158" stroke="#1a1730" stroke-width="1.6" class="wr"/>
<circle cx="30" cy="19" r="11" fill="#ff6b6b" stroke="#1a1730" stroke-width="2.2"/>
<polygon points="20,17 8,22 20,27" fill="#ffd158" stroke="#1a1730" stroke-width="1.6" stroke-linejoin="round"/>
<circle cx="27" cy="16" r="2.4" fill="#1a1730" class="blink"/>
```

### fish
```svg
<polygon points="44,38 62,26 62,50" fill="#3fc0b0" stroke="#1a1730" stroke-width="2" stroke-linejoin="round"/>
<ellipse cx="27" cy="38" rx="20" ry="14.5" fill="#5fdcb0" stroke="#1a1730" stroke-width="2.2"/>
<ellipse cx="33" cy="38" rx="5" ry="13.5" fill="#3fc0b0"/>
<polygon points="19,24 33,21 26,31" fill="#3fc0b0" stroke="#1a1730" stroke-width="1.6" stroke-linejoin="round"/>
<circle cx="16" cy="34" r="3.6" fill="#ffffff" stroke="#1a1730" stroke-width="1.4"/>
<circle cx="16" cy="34" r="1.8" fill="#1a1730" class="blink"/>
<path d="M11 45 Q17 50 24 47" fill="none" stroke="#1a1730" stroke-width="1.6" stroke-linecap="round"/>
```

### dragon
```svg
<polygon points="20,26 14,8 28,24" fill="#ffd158" stroke="#1a1730" stroke-width="1.8" stroke-linejoin="round"/>
<polygon points="38,26 44,8 30,24" fill="#ffd158" stroke="#1a1730" stroke-width="1.8" stroke-linejoin="round"/>
<ellipse cx="8" cy="40" rx="6" ry="12" fill="#4f9e4a" stroke="#1a1730" stroke-width="2" stroke-linejoin="round" class="wl"/>
<ellipse cx="28" cy="38" rx="19" ry="16" fill="#6fbf68" stroke="#1a1730" stroke-width="2.2"/>
<path d="M43 31 Q60 30 60 39 Q60 48 43 47 Z" fill="#8fd47f" stroke="#1a1730" stroke-width="2" stroke-linejoin="round"/>
<ellipse cx="22" cy="33" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<circle cx="55" cy="37" r="1.6" fill="#1a1730"/>
<path d="M44 45 L36 50 M40 50 L34 45" fill="none" stroke="#fff4ea" stroke-width="1.8" stroke-linecap="round"/>
```

### lion
```svg
<circle cx="17" cy="24" r="5.4" fill="#d97f2a" stroke="#1a1730" stroke-width="1.8" class="el"/>
<circle cx="47" cy="24" r="5.4" fill="#d97f2a" stroke="#1a1730" stroke-width="1.8" class="er"/>
<circle cx="32" cy="38" r="22" fill="#e08a2f" stroke="#1a1730" stroke-width="2.2"/>
<circle cx="32" cy="38" r="15" fill="#ffc47a" stroke="#1a1730" stroke-width="1.6"/>
<ellipse cx="26" cy="35" rx="2.6" ry="3.4" fill="#1a1730" class="blink"/>
<ellipse cx="38" cy="35" rx="2.6" ry="3.4" fill="#1a1730" class="blink"/>
<ellipse cx="32" cy="45" rx="8" ry="6" fill="#fff1e6" stroke="#1a1730" stroke-width="1.4"/>
<ellipse cx="32" cy="41" rx="3.2" ry="2.4" fill="#1a1730"/>
<path d="M32 45 Q28 50 24 47 M32 45 Q36 50 40 47" fill="none" stroke="#c96f22" stroke-width="1.6" stroke-linecap="round"/>
```

### hamster
```svg
<circle cx="20" cy="23" r="5.6" fill="#d9a06a" stroke="#1a1730" stroke-width="1.8" class="el"/>
<circle cx="44" cy="23" r="5.6" fill="#d9a06a" stroke="#1a1730" stroke-width="1.8" class="er"/>
<circle cx="32" cy="40" r="19" fill="#f0c89a" stroke="#1a1730" stroke-width="2.2"/>
<circle cx="15" cy="46" r="7" fill="#f8dcbb" stroke="#1a1730" stroke-width="1.4"/>
<circle cx="49" cy="46" r="7" fill="#f8dcbb" stroke="#1a1730" stroke-width="1.4"/>
<ellipse cx="25" cy="36" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<ellipse cx="39" cy="36" rx="3" ry="3.8" fill="#1a1730" class="blink"/>
<ellipse cx="32" cy="43" rx="3.2" ry="2.4" fill="#d9668a" stroke="#1a1730" stroke-width="1.4"/>
<path d="M32 46 Q28 51 24 48 M32 46 Q36 51 40 48" fill="none" stroke="#c98f5f" stroke-width="1.6" stroke-linecap="round"/>
```

---

# When all three steps are done

Show me the start screen, the play screen mid-game, the collection screen,
and the pet reward popup.

If a pet renders wrong or gets clipped at the edge of its box, tell me which
one and what the problem is. Do not redraw it yourself — the twenty are
meant to look like one set, and a redraw breaks that.
