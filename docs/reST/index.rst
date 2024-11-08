import pygame
import random
import sys

# Initialize pygame
pygame.init()

# Screen settings
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Penalty Kick Game")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (34, 177, 76)
BLUE = (0, 162, 232)
RED = (237, 28, 36)

# Load assets
ball_img = pygame.image.load("path/to/your/ball.png")  # Replace with the path to your ball image
goalkeeper_img = pygame.image.load("path/to/your/goalkeeper.png")  # Replace with path to your goalkeeper image
ball_img = pygame.transform.scale(ball_img, (50, 50))  # Resize ball
goalkeeper_img = pygame.transform.scale(goalkeeper_img, (100, 100))  # Resize goalkeeper

# Game variables
ball_pos = [WIDTH // 2, HEIGHT - 100]
goalkeeper_pos = [WIDTH // 2 - 50, 200]
goalkeeper_move = random.choice([-1, 1])
ball_speed = [0, -15]
score = 0
attempts = 5

# Fonts
font = pygame.font.Font(None, 36)

def reset_ball():
    global ball_pos, ball_speed
    ball_pos = [WIDTH // 2, HEIGHT - 100]
    ball_speed = [0, -15]

def display_text(text, color, position):
    text_surface = font.render(text, True, color)
    screen.blit(text_surface, position)

# Game loop
running = True
kick = False
goal = False
while running:
    screen.fill(GREEN)

    # Draw goal post
    pygame.draw.rect(screen, WHITE, (WIDTH // 2 - 150, 100, 300, 10))  # Top bar
    pygame.draw.rect(screen, WHITE, (WIDTH // 2 - 150, 100, 10, 100))  # Left post
    pygame.draw.rect(screen, WHITE, (WIDTH // 2 + 140, 100, 10, 100))  # Right post

    # Player input
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
            pygame.quit()
            sys.exit()
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                ball_speed[0] = -5
                kick = True
            elif event.key == pygame.K_RIGHT:
                ball_speed[0] = 5
                kick = True
            elif event.key == pygame.K_SPACE:
                kick = True

    # Move goalkeeper back and forth
    goalkeeper_pos[0] += goalkeeper_move * 5
    if goalkeeper_pos[0] <= WIDTH // 2 - 150 or goalkeeper_pos[0] >= WIDTH // 2 + 50:
        goalkeeper_move *= -1  # Change direction at boundaries

    # Move ball if kicked
    if kick:
        ball_pos[0] += ball_speed[0]
        ball_pos[1] += ball_speed[1]

    # Check if goal scored
    if (WIDTH // 2 - 150 < ball_pos[0] < WIDTH // 2 + 140) and ball_pos[1] < 100:
        if abs(ball_pos[0] - goalkeeper_pos[0]) > 50:  # Goalkeeper misses
            goal = True
            score += 1
            display_text("GOAL!", BLUE, (WIDTH // 2 - 40, HEIGHT // 2))
            pygame.display.update()
            pygame.time.delay(1000)
            reset_ball()
            kick = False
        else:
            display_text("SAVED!", RED, (WIDTH // 2 - 40, HEIGHT // 2))
            pygame.display.update()
            pygame.time.delay(1000)
            reset_ball()
            kick = False

    # Check if ball goes out of bounds
    if ball_pos[1] < 0 or ball_pos[0] < 0 or ball_pos[0] > WIDTH:
        reset_ball()
        kick = False
        attempts -= 1

    # Draw ball and goalkeeper
    screen.blit(ball_img, ball_pos)
    screen.blit(goalkeeper_img, goalkeeper_pos)

    # Display score and attempts
    display_text(f"Score: {score}", WHITE, (10, 10))
    display_text(f"Attempts: {attempts}", WHITE, (WIDTH - 150, 10))

    # Check game over
    if attempts <= 0:
        display_text("Game Over!", RED, (WIDTH // 2 - 60, HEIGHT // 2))
        pygame.display.update()
        pygame.time.delay(2000)
        running = False

    pygame.display.update()
    pygame.time.delay(30)

pygame.quit()

