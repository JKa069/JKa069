import pygame
import random

# Initialize Pygame
pygame.init()

# Screen settings
WIDTH, HEIGHT = 400, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Fly and Dodge")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# Clock and game speed
clock = pygame.time.Clock()
FPS = 60

# Bird settings
bird_width, bird_height = 30, 30
bird_x, bird_y = 50, HEIGHT // 2
bird_speed = 5
bird = pygame.Rect(bird_x, bird_y, bird_width, bird_height)

# Obstacle settings
obstacle_width = 50
obstacle_speed = 5
gap = 200
obstacles = []

# Score
score = 0
font = pygame.font.Font(None, 36)

def create_obstacle():
    top_height = random.randint(50, HEIGHT - gap - 50)
    bottom_height = HEIGHT - gap - top_height
    top_obstacle = pygame.Rect(WIDTH, 0, obstacle_width, top_height)
    bottom_obstacle = pygame.Rect(WIDTH, HEIGHT - bottom_height, obstacle_width, bottom_height)
    return top_obstacle, bottom_obstacle

def move_obstacles(obstacles):
    for obs in obstacles:
        obs.x -= obstacle_speed
    return [obs for obs in obstacles if obs.x + obstacle_width > 0]

# Main game loop
running = True
while running:
    screen.fill(WHITE)

    # Event handling
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Bird controls
    keys = pygame.key.get_pressed()
    if keys[pygame.K_UP] and bird.y > 0:
        bird.y -= bird_speed
    if keys[pygame.K_DOWN] and bird.y < HEIGHT - bird_height:
        bird.y += bird_speed

    # Obstacle generation
    if len(obstacles) == 0 or obstacles[-1][0].x < WIDTH - 300:
        obstacles.extend(create_obstacle())

    # Move and draw obstacles
    obstacles = move_obstacles([*obstacles[0]])
    for obs in obstacles:
        pygame.draw.rect(screen, RED, obs)

    # Draw bird
    pygame.draw.ellipse(screen, BLUE, bird)

    # Collision detection
    if any(bird.colliderect(obs) for obs in obstacles):
        running = False

    # Update score
    score += 1
    score_text = font.render(f"Score: {score // 100}", True, BLACK)
    screen.blit(score_text, (10, 10))

    # Update display and tick
    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
