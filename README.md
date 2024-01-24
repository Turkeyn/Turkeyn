import pygame
import sys
import random

# กำหนดขนาดของบล็อกและขนาดของหน้าต่างเกม
block_size = 20
window_size = 400

# กำหนดสี
white = (255, 255, 255)
green = (0, 255, 0)
red = (255, 0, 0)

# กำหนดเวลาในการอัปเดตตำแหน่งของงู
clock = pygame.time.Clock()
speed = 15

# กำหนดเริ่มต้นของงู
snake = [(10, 10), (10, 11)]
direction = "right"

# กำหนดตำแหน่งเริ่มต้นของอาหาร
food = (15, 15)

def draw_snake(snake):
    for segment in snake:
        pygame.draw.rect(screen, white, (segment[0] * block_size, segment[1] * block_size, block_size, block_size))

def draw_food(food):
    pygame.draw.rect(screen, red, (food[0] * block_size, food[1] * block_size, block_size, block_size))

def check_collision(head, body):
    return any(segment == head for segment in body)

def spawn_food():
    return random.randint(0, window_size // block_size - 1), random.randint(0, window_size // block_size - 1)

def game_over():
    font = pygame.font.Font(None, 36)
    text = font.render("Game Over", True, white)
    screen.blit(text, (window_size // 2 - 100, window_size // 2 - 50))
    pygame.display.flip()
    pygame.time.wait(2000)
    pygame.quit()
    sys.exit()

pygame.init()
screen = pygame.display.set_mode((window_size, window_size))
pygame.display.set_caption("Snake Game")

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_UP and direction != "down":
                direction = "up"
            elif event.key == pygame.K_DOWN and direction != "up":
                direction = "down"
            elif event.key == pygame.K_LEFT and direction != "right":
                direction = "left"
            elif event.key == pygame.K_RIGHT and direction != "left":
                direction = "right"

    # อัปเดตตำแหน่งของงู
    head = list(snake[0])
    if direction == "up":
        head[1] -= 1
    elif direction == "down":
        head[1] += 1
    elif direction == "left":
        head[0] -= 1
    elif direction == "right":
        head[0] += 1

    # ตรวจสอบการชนกับขอบหรือตัวเอง
    if (
        head[0] < 0
        or head[0] >= window_size // block_size
        or head[1] < 0
        or head[1] >= window_size // block_size
        or check_collision(head, snake[1:])
    ):
        game_over()

    # ตรวจสอบการกินอาหาร
    if head[0] == food[0] and head[1] == food[1]:
        snake.insert(0, tuple(head))
        food = spawn_food()
    
    else:
        snake.insert(0, tuple(head))
        snake.pop()

    # ล้างหน้าต่าง
    screen.fill((0, 0, 0))

    # วาดงูและอาหาร
    draw_snake(snake)
    draw_food(food)

    # อัปเดตหน้าต่าง
    pygame.display.flip()

    # จัดการความเร็ว
    clock.tick(speed)
    
