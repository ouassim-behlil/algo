# fast Clicker game:
```python
import pygame
import random

pygame.init()

# --- Fenêtre ---
HEIGHT = 500
WIDTH = 700
mw = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Fast Clicker")

clock = pygame.time.Clock()

# --- Couleurs ---
BACK = (200, 255, 255)
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 100, 100)

# --- Classe Area ---
class Area:
    def __init__(self, x=0, y=0, w=100, h=100, color=None):
        self.rect = pygame.Rect(x, y, w, h)
        self.fill_color = color

    def color(self, new_color):
        self.fill_color = new_color

    def fill(self):
        pygame.draw.rect(mw, self.fill_color, self.rect)

    def outline(self, frame_color, thickness):
        pygame.draw.rect(mw, frame_color, self.rect, thickness)

# --- Classe Label ---
class Label(Area):
    def __init__(self, x, y, w, h, color, text="", text_color=BLACK, size=35):

        super().__init__(x, y, w, h, color)

        self.text = text
        self.text_color = text_color
        self.size = size

    def set_text(self, text):
        self.text = text

    def draw(self, shift_x=0, shift_y=0):
        self.fill()
        self.outline(BLACK, 2)

        font = pygame.font.SysFont("verdana", self.size)
        img = font.render(self.text, True, self.text_color)

        mw.blit(img, (self.rect.x + shift_x,
                      self.rect.y + shift_y))

# --- Création des cartes ---
cards = []

x = 40
y = 180
w = 140
h = 150
gap = 20

for i in range(4):
    card = Label(x, y, w, h, WHITE, "")
    cards.append(card)

    x += w + gap

# --- Variables du jeu ---
score = 0
time_left = 20

active_card = random.randint(0, 3)

# Timer pour changer la carte rouge
CHANGE_CARD = pygame.USEREVENT
pygame.time.set_timer(CHANGE_CARD, 900)

# Timer du jeu
GAME_TIME = pygame.USEREVENT + 1
pygame.time.set_timer(GAME_TIME, 1000)

# --- Boucle principale ---
running = True

while running:

    mw.fill(BACK)

    # --- Events ---
    for event in pygame.event.get():

        if event.type == pygame.QUIT:
            running = False

        # Changer la carte rouge
        if event.type == CHANGE_CARD:
            active_card = random.randint(0, 3)

        # Temps
        if event.type == GAME_TIME:
            time_left -= 1

            if time_left <= 0:
                running = False

        # Clic souris
        if event.type == pygame.MOUSEBUTTONDOWN:

            pos = event.pos

            for i in range(len(cards)):

                if cards[i].rect.collidepoint(pos):

                    if i == active_card:
                        score += 1

    # --- Dessin des cartes ---
    for i in range(len(cards)):

        if i == active_card:
            cards[i].color(RED)
            cards[i].set_text("CLICK")
        else:
            cards[i].color(WHITE)
            cards[i].set_text("")

        cards[i].draw(15, 50)

    # --- Texte score ---
    font = pygame.font.SysFont("verdana", 30)

    score_text = font.render("Score : " + str(score),
                             True, BLACK)

    time_text = font.render("Temps : " + str(time_left),
                            True, BLACK)

    mw.blit(score_text, (40, 40))
    mw.blit(time_text, (500, 40))

    pygame.display.update()
    clock.tick(60)

# --- Fin du jeu ---
mw.fill(BACK)

font = pygame.font.SysFont("verdana", 45)

end_text = font.render("Score final : " + str(score),
                       True, BLACK)

mw.blit(end_text, (180, 220))

pygame.display.update()

pygame.time.delay(3000)

pygame.quit()
```
