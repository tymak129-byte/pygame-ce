

import pygame
import sys
import json
import random

pygame.init()

# -------------------------
# SETTINGS
# -------------------------
WIDTH, HEIGHT = 960, 640
TILE = 32
COLS = WIDTH // TILE
ROWS = HEIGHT // TILE
GRAVITY = 0.5
JUMP_POWER = -10

screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Crafting & Building 2 - Survival Edition")

clock = pygame.time.Clock()

# -------------------------
# COLORS
# -------------------------
SKY_DAY = (135, 206, 235)
SKY_NIGHT = (20, 20, 60)
DIRT = (139, 69, 19)
STONE = (120, 120, 120)
WOOD = (160, 82, 45)
SAND = (237, 201, 175)

# -------------------------
# BLOCK TYPES
# -------------------------
BLOCKS = {
    0: None,
    1: DIRT,
    2: STONE,
    3: WOOD,
    4: SAND
}

# -------------------------
# WORLD
# -------------------------
world = [[0 for _ in range(COLS)] for _ in range(ROWS)]

for r in range(ROWS // 2, ROWS):
    for c in range(COLS):
        world[r][c] = random.choice([1, 1, 1, 2])

# -------------------------
# PLAYER
# -------------------------
player = pygame.Rect(200, 200, TILE, TILE)
vel_y = 0
on_ground = False
health = 100

inventory = {
    "dirt": 0,
    "stone": 0,
    "wood": 0,
    "sand": 0
}

# -------------------------
# ENEMIES
# -------------------------
enemies = []
is_night = False
day_timer = 0

# -------------------------
# ROBOT HELPER
# -------------------------
robot = pygame.Rect(300, 200, TILE, TILE)

# -------------------------
# FUNCTIONS
# -------------------------
def draw_world():
    for r in range(ROWS):
        for c in range(COLS):
            block = world[r][c]
            if block != 0:
                pygame.draw.rect(
                    screen,
                    BLOCKS[block],
                    (c*TILE, r*TILE, TILE, TILE)
                )

def apply_gravity():
    global vel_y, on_ground
    vel_y += GRAVITY
    player.y += vel_y

    on_ground = False

    for r in range(ROWS):
        for c in range(COLS):
            if world[r][c] != 0:
                block_rect = pygame.Rect(c*TILE, r*TILE, TILE, TILE)
                if player.colliderect(block_rect):
                    if vel_y > 0:
                        player.bottom = block_rect.top
                        vel_y = 0
                        on_ground = True

def spawn_enemy():
    x = random.randint(0, WIDTH - TILE)
    enemies.append(pygame.Rect(x, 100, TILE, TILE))

def enemy_ai():
    global health
    for e in enemies:
        if e.x < player.x:
            e.x += 1
        else:
            e.x -= 1

        if e.colliderect(player):
            health -= 0.1

def robot_ai():
    if robot.x < player.x:
        robot.x += 1
    else:
        robot.x -= 1

def save_world():
    with open("world_save.json", "w") as f:
        json.dump(world, f)

def load_world():
    global world
    try:
        with open("world_save.json", "r") as f:
            world = json.load(f)
    except:
        pass

# -------------------------
# MAIN LOOP
# -------------------------
running = True
while running:
    clock.tick(60)
    day_timer += 1

    if day_timer > 600:
        is_night = not is_night
        day_timer = 0
        if is_night:
            spawn_enemy()

    screen.fill(SKY_NIGHT if is_night else SKY_DAY)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and on_ground:
                vel_y = JUMP_POWER

            if event.key == pygame.K_c:
                if inventory["wood"] >= 5:
                    inventory["wood"] -= 5
                    print("Crafted crafting table!")

            if event.key == pygame.K_F5:
                save_world()

            if event.key == pygame.K_F9:
                load_world()

    keys = pygame.key.get_pressed()
    if keys[pygame.K_a]:
        player.x -= 5
    if keys[pygame.K_d]:
        player.x += 5

    apply_gravity()
    enemy_ai()
    robot_ai()

    draw_world()

    pygame.draw.rect(screen, (255, 0, 0), player)
    pygame.draw.rect(screen, (0, 255, 255), robot)

    for e in enemies:
        pygame.draw.rect(screen, (0, 0, 0), e)

    font = pygame.font.SysFont(None, 30)
    health_text = font.render(f"Health: {int(health)}", True, (255,255,255))
    screen.blit(health_text, (10, 10))

    pygame.display.flip()

pygame.quit()
sys.exit()import pygame
import sys
import json
import random

pygame.init()

# -------------------------
# SETTINGS
# -------------------------
WIDTH, HEIGHT = 960, 640
TILE = 32
COLS = WIDTH // TILE
ROWS = HEIGHT // TILE
GRAVITY = 0.5
JUMP_POWER = -10

screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Crafting & Building 2 - Survival Edition")

clock = pygame.time.Clock()

# -------------------------
# COLORS
# -------------------------
SKY_DAY = (135, 206, 235)
SKY_NIGHT = (20, 20, 60)
DIRT = (139, 69, 19)
STONE = (120, 120, 120)
WOOD = (160, 82, 45)
SAND = (237, 201, 175)

# -------------------------
# BLOCK TYPES
# -------------------------
BLOCKS = {
    0: None,
    1: DIRT,
    2: STONE,
    3: WOOD,
    4: SAND
}

# -------------------------
# WORLD
# -------------------------
world = [[0 for _ in range(COLS)] for _ in range(ROWS)]

for r in range(ROWS // 2, ROWS):
    for c in range(COLS):
        world[r][c] = random.choice([1, 1, 1, 2])

# -------------------------
# PLAYER
# -------------------------
player = pygame.Rect(200, 200, TILE, TILE)
vel_y = 0
on_ground = False
health = 100

inventory = {
    "dirt": 0,
    "stone": 0,
    "wood": 0,
    "sand": 0
}

# -------------------------
# ENEMIES
# -------------------------
enemies = []
is_night = False
day_timer = 0

# -------------------------
# ROBOT HELPER
# -------------------------
robot = pygame.Rect(300, 200, TILE, TILE)

# -------------------------
# FUNCTIONS
# -------------------------
def draw_world():
    for r in range(ROWS):
        for c in range(COLS):
            block = world[r][c]
            if block != 0:
                pygame.draw.rect(
                    screen,
                    BLOCKS[block],
                    (c*TILE, r*TILE, TILE, TILE)
                )

def apply_gravity():
    global vel_y, on_ground
    vel_y += GRAVITY
    player.y += vel_y

    on_ground = False

    for r in range(ROWS):
        for c in range(COLS):
            if world[r][c] != 0:
                block_rect = pygame.Rect(c*TILE, r*TILE, TILE, TILE)
                if player.colliderect(block_rect):
                    if vel_y > 0:
                        player.bottom = block_rect.top
                        vel_y = 0
                        on_ground = True

def spawn_enemy():
    x = random.randint(0, WIDTH - TILE)
    enemies.append(pygame.Rect(x, 100, TILE, TILE))

def enemy_ai():
    global health
    for e in enemies:
        if e.x < player.x:
            e.x += 1
        else:
            e.x -= 1

        if e.colliderect(player):
            health -= 0.1

def robot_ai():
    if robot.x < player.x:
        robot.x += 1
    else:
        robot.x -= 1

def save_world():
    with open("world_save.json", "w") as f:
        json.dump(world, f)

def load_world():
    global world
    try:
        with open("world_save.json", "r") as f:
            world = json.load(f)
    except:
        pass

# -------------------------
# MAIN LOOP
# -------------------------
running = True
while running:
    clock.tick(60)
    day_timer += 1

    if day_timer > 600:
        is_night = not is_night
        day_timer = 0
        if is_night:
            spawn_enemy()

    screen.fill(SKY_NIGHT if is_night else SKY_DAY)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and on_ground:
                vel_y = JUMP_POWER

            if event.key == pygame.K_c:
                if inventory["wood"] >= 5:
                    inventory["wood"] -= 5
                    print("Crafted crafting table!")

            if event.key == pygame.K_F5:
                save_world()

            if event.key == pygame.K_F9:
                load_world()

    keys = pygame.key.get_pressed()
    if keys[pygame.K_a]:
        player.x -= 5
    if keys[pygame.K_d]:
        player.x += 5

    apply_gravity()
    enemy_ai()
    robot_ai()

    draw_world()

    pygame.draw.rect(screen, (255, 0, 0), player)
    pygame.draw.rect(screen, (0, 255, 255), robot)

    for e in enemies:
        pygame.draw.rect(screen, (0, 0, 0), e)

    font = pygame.font.SysFont(None, 30)
    health_text = font.render(f"Health: {int(health)}", True, (255,255,255))
    screen.blit(health_text, (10, 10))

    pygame.display.flip()

pygame.quit()
sys.exit()
