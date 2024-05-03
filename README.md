
import pygame
import random

WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
GREEN = (0, 255, 0)

CELL_SIZE = 20
WIDTH = 800
HEIGHT = 600

snake = [(WIDTH // 2, HEIGHT // 2)]
dx, dy = 1, 0

food = (random.randint(0, (WIDTH - CELL_SIZE) // CELL_SIZE) * CELL_SIZE,
        random.randint(0, (HEIGHT - CELL_SIZE) // CELL_SIZE) * CELL_SIZE)

score = 0
game_time = 0

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
font = pygame.font.SysFont(None, 36)


def draw_snake_and_food():
    screen.fill(BLACK)
    for segment in snake:
        pygame.draw.rect(screen, GREEN, (segment[0], segment[1], CELL_SIZE, CELL_SIZE))
    pygame.draw.rect(screen, RED, (food[0], food[1], CELL_SIZE, CELL_SIZE))

    score_text = font.render("Score: " + str(score), True, WHITE)
    time_text = font.render("Time: " + str(game_time // 1000) + " s", True, WHITE)
    screen.blit(score_text, (10, 10))
    screen.blit(time_text, (10, 40))

    pygame.display.update()


def check_food_collision():
    global food, score
    if snake[0] == food:
        snake.append(snake[-1])
        food = (random.randint(0, (WIDTH - CELL_SIZE) // CELL_SIZE) * CELL_SIZE,
                random.randint(0, (HEIGHT - CELL_SIZE) // CELL_SIZE) * CELL_SIZE)
        score += 1
        return True
    return False


def check_collision():
    head = snake[0]
    if head in snake[1:]:
        return True
    if head[0] < 0 or head[0] >= WIDTH or head[1] < 0 or head[1] >= HEIGHT:
        return True
    return False


def ai_control():
    global dx, dy
    head = snake[0]
    food_x, food_y = food
    if head[0] < food_x:
        dx, dy = 1, 0
    elif head[0] > food_x:
        dx, dy = -1, 0
    elif head[1] < food_y:
        dx, dy = 0, 1
    elif head[1] > food_y:
        dx, dy = 0, -1


running = True
start_time = pygame.time.get_ticks()
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    ai_control()

    new_head = (snake[0][0] + dx * CELL_SIZE, snake[0][1] + dy * CELL_SIZE)
    snake = [new_head] + snake[:-1]

    if check_collision():
        running = False

    if check_food_collision():
        continue

    draw_snake_and_food()

    pygame.display.flip()
    clock.tick(8)

    game_time = pygame.time.get_ticks() - start_time

pygame.quit()
