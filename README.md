# space-invaders-game
import pygame
import random as r
from pygame import mixer

# initializing the pygame class
pygame.init()
# creating the window

screen = pygame.display.set_mode((800, 600))
# the background image setting
background = pygame.image.load('back.jpg')
# background sound
mixer.music.load('background.wav')
mixer.music.play(-1)
# the title and icon
pygame.display.set_caption("SPACE STRIKE")
icon = pygame.image.load('001-ufo.png')
pygame.display.set_icon(icon)
# when the flag is in ready state means no firing ready to Fire
# when the flag is in fire state means it is firing
# the enemy's
enemy = []
enemyx = []
enemyy = []
changex = []
changey = []
number_of_enemies = 7
for i in range(number_of_enemies):
    enemy.append(pygame.image.load("alien.png"))
    enemyx.append(r.randint(0, 736))
    enemyy.append(r.randint(50, 150))
    changex.append(0.5)
    changey.append(20)

# the player
playerimg = pygame.image.load('001-space-invaders.png')
playerx = 370
playery = 480
change = 0
# for the bullet
bulletImg = pygame.image.load('bullet.png')
bulletX = 0
bulletY = 480
bulletX_change = 0
bulletY_change = 0.9
bullet_state = "ready"

# score calculation
score = 0
font = pygame.font.Font('freesansbold.ttf', 25)
scorex = 30
scorey = 30
#game over
over_font = pygame.font.Font('freesansbold.ttf', 64)


def player(x, y):
    screen.blit(playerimg, (x, y))

def game_over_text():
    over_text = over_font.render("GAME OVER", True, (255, 255, 255))
    screen.blit(over_text, (200, 250))

def enemy_pos(x, y, i):
    screen.blit(enemy[i], (x, y))


def fire_bullet(x, y):
    global bullet_state
    bullet_state = "fire"
    screen.blit(bulletImg, (x + 18, y + 10))


# to find out whether the bullet hits the enemy or not
def isCollision(enemyx, enemyy, bulletX, bulletY):
    distance = (((enemyx - bulletX) ** 2) + (((enemyy - bulletY) ** 2))) ** (1 / 2)
    if distance < 27:
        return True
    else:
        return False


def display_score(x, y):
    score_count = font.render("score:" + str(score), True, (255, 255, 0))
    screen.blit(score_count, (x, y))


# game loop
running = True
while running:
    # changing the color
    screen.fill((2, 0, 0))
    screen.blit(background, (0, 0))
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
            # keystrokes
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                change = -0.4
            if event.key == pygame.K_RIGHT:
                change = +0.4
            if event.key == pygame.K_SPACE:
                if bullet_state == "ready":
                    bullet_sound = mixer.Sound('laser.wav')
                    bullet_sound.play()
                    bulletX = playerx
                fire_bullet(bulletX, bulletY)
        if event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                change = 0
    playerx += (change)
    if playerx <= 0:
        playerx = 0
    elif playerx >= 736:
        playerx = 736
    for i in range(number_of_enemies):
        if enemyy[i] >= 250:
            for j in range(number_of_enemies):
                enemyy[j] = 2000
            game_over_text()
            break
        enemyx[i] += changex[i]
        if enemyx[i] >= 736:
            changex[i] = -0.3
            enemyy[i] += changey[i]

        elif enemyx[i] <= 0:
            changex[i] = 0.3
            enemyy[i] += changey[i]
        collision = isCollision(bulletX, bulletY, enemyx[i], enemyy[i])
        if collision:
            expl_sound = mixer.Sound('explosion.wav')
            expl_sound.play()
            bullet_state = "ready"
            bulletY = 480
            score += 1
            enemyx[i] = r.randint(0, 736)
            enemyy[i] = r.randint(30, 150)
        enemy_pos(enemyx[i], enemyy[i], i)

    # to make the bullet move
    if bulletY <= 0:
        bulletY = 480
        bullet_state = "ready"
    if bullet_state == "fire":
        fire_bullet(bulletX, bulletY)
        bulletY -= bulletY_change
    # checking whther the bullet is hit or not

    player(playerx, playery)
    display_score(scorex, scorey)
    pygame.display.update()
