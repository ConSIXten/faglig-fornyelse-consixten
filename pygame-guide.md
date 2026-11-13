# Kom godt i gang med Pygame

Denne guide er en introduktion til **Pygame** — et Python-bibliotek til at lave 2D-spil. Guiden er skrevet til klassekammerater, der aldrig har arbejdet med Pygame før, og giver både en praktisk opsætning og en forklaring på, hvorfor Pygame stadig er relevant som teknologi til spiludvikling.

## Min proces

Som en del af research til dette emne har jeg, udover selv at bygge et Flappy Bird-klon-spil i Pygame, også:

- Undersøgt andre spil-engines (bl.a. Unity og Godot) for at kunne sammenligne, hvor Pygame placerer sig i forhold til mere fuldt udbyggede engines.
- Set introduktionsvideoer til Pygame for at få overblik over bibliotekets muligheder, inden jeg selv gik i gang med at kode.
- Fulgt generelle Python-tutorials undervejs, da flere Pygame-koncepter (f.eks. klasser, globale variabler og event loops) byggede videre på grundlæggende Python-forståelse.

## Hvad er Pygame?

Pygame er et open source Python-bibliotek, der giver dig adgang til at tegne grafik, afspille lyd, håndtere input (tastatur/mus/controller) og styre et spilvindue — alt sammen bygget oven på [SDL2](https://www.libsdl.org/) (Simple DirectMedia Layer), som er den samme underliggende teknologi, mange kommercielle spil og emulatorer bruger til lavniveau grafik- og inputhåndtering.

I modsætning til en fuld spil-engine som Unity eller Godot giver Pygame dig **ingen** færdige systemer til fysik, scene-grafer eller UI — du skriver selv game loopet, kollisionstjek og tegning. Det gør Pygame enkelt at forstå fra bunden, fordi der ikke er noget "magisk" skjult bag en editor.

## Hvorfor er Pygame en relevant/fremadrettet teknologi?

- **Lavt indgangsniveau:** Hvis du allerede kan Python, kan du have et kørende spilvindue på under 10 linjer kode. Det gør Pygame til et oplagt første skridt, før man kaster sig over mere komplekse engines.
- **Aktivt videreudviklet:** Det oprindelige Pygame-projekt lever videre som **Pygame-CE** (Community Edition), en aktivt vedligeholdt fork, der løbende opdateres til nye Python-versioner og tilføjer nye features. Det underliggende SDL2 opdateres også stadig af Valve m.fl., bl.a. til brug i Steam Deck.
- **Undervisning og prototyping:** Pygame bruges bredt i undervisningssammenhænge, fordi det tvinger dig til at forstå kernekoncepter (game loop, delta time, koordinatsystemer, kollision) i stedet for at skjule dem bag en engine. Det gør det også hurtigt at prototype en spilidé, før man evt. bygger den rigtige version i en større engine.
- **Cross-platform:** Samme kode kører på Windows, macOS og Linux, så længe Python og Pygame er installeret.
- **Stort community:** Mange års dokumentation, tutorials og eksempler findes online, så man sjældent står helt fast.

## Installation

Pygame installeres nemmest via en pakkehåndtering. Her bruges [uv](https://docs.astral.sh/uv/), som er en moderne og hurtig erstatning for pip + venv, men almindelig `pip` fungerer også.

```bash
# Opret/initialiser projektet (hvis det ikke allerede findes)
uv init mit-spil
cd mit-spil

# Installér pygame som en dependency
uv add pygame
```

Med almindelig `pip`:

```bash
python -m venv .venv
source .venv/bin/activate   # på Windows: .venv\Scripts\activate
pip install pygame
```

> **OBS (macOS):** Hvis din Python-version er meget ny (nyere end den seneste Pygame-wheel understøtter), kan `pip`/`uv` forsøge at bygge Pygame fra kildekode. Det kræver SDL2-headers installeret via Homebrew (`brew install sdl2 sdl2_image sdl2_mixer sdl2_ttf`). Det er langt nemmere at bruge en Python-version, der har et færdigbygget wheel til Pygame (typisk nyeste stabile Python-version minus 1).

## Dit første Pygame-vindue

```python
import pygame
from sys import exit

pygame.init()
screen = pygame.display.set_mode((800, 600))
pygame.display.set_caption("Mit første spil")
clock = pygame.time.Clock()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            exit()

    screen.fill("skyblue")
    pygame.display.update()
    clock.tick(60)
```

Dette er **game loopet** — hjertet i ethvert Pygame-spil:

1. **Events:** Tjek om spilleren har lukket vinduet, trykket på en tast osv.
2. **Opdatér:** Flyt objekter, tjek kollisioner, opdatér score.
3. **Tegn:** Tegn baggrund, objekter og tekst til skærmen.
4. **Flip/update:** Vis det tegnede billede.
5. **Tick:** Vent til næste frame, så spillet kører med en fast framerate (her 60 FPS).

## Centrale begreber

| Begreb | Beskrivelse |
|---|---|
| `pygame.Surface` | Et "billede" i hukommelsen — kan være vinduet selv, et indlæst billede, eller tekst gjort til grafik. |
| `pygame.Rect` | Et rektangel med x, y, bredde og højde — bruges til placering og kollisionstjek (`colliderect`). |
| `pygame.image.load()` | Indlæser et billede (f.eks. `.png`) som en `Surface`. |
| `pygame.transform.scale()` | Skalerer en `Surface` op eller ned i størrelse. |
| `pygame.event.get()` | Henter alle input-events siden sidste frame (tastetryk, museklik, luk-vindue osv.). |
| `pygame.time.Clock` | Styrer framerate, så spillet ikke kører hurtigere på kraftigere computere. |

## Eksempel: Simpel fugl, der reagerer på input

```python
velocity_y = 0
gravity = 0.4

def move():
    global velocity_y
    velocity_y += gravity
    bird.y += velocity_y

# Inde i event-loopet:
if event.type == pygame.KEYDOWN:
    if event.key in (pygame.K_SPACE, pygame.K_UP):
        velocity_y = -6   # "hop"
```

Dette er præcis princippet bag klassikere som *Flappy Bird*: tyngdekraft trækker konstant objektet nedad, og et tastetryk giver et øjeblikkeligt "spark" opad.

## Gode råd til at komme videre

- Start småt: få et vindue op, derefter ét objekt der bevæger sig, derefter kollision.
- Brug `pygame.Rect` til kollisionstjek i stedet for selv at regne koordinater ud — `rect1.colliderect(rect2)` klarer det meste.
- Husk `global` nøgleordet, hvis du ændrer en variabel defineret uden for en funktion.
- Hold game loopet rent: events → opdatér state → tegn. Bland ikke tegning og logik sammen.
- Når du støder på en mur, så tjek [Pygame-CE's dokumentation](https://pyga.me/docs/) — den er velholdt og har gode eksempler.

## Opsummering

Pygame er ikke den nyeste teknologi på markedet, men det er netop derfor et solidt valg: det er stabilt, veldokumenteret, aktivt vedligeholdt via Pygame-CE, og det lærer dig de grundlæggende principper bag spiludvikling, som senere gør det lettere at gå videre til større engines. Det er et oplagt sted at starte, hvis man vil forstå *hvordan* et spil faktisk fungerer under motorhjelmen.
