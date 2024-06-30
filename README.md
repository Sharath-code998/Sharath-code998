import pygame
import sys
import random

# Initialize Pygame
pygame.init()

# Constants
SCREEN_WIDTH = 288
SCREEN_HEIGHT = 512
FPS = 30
GRAVITY = 0.25

# File paths
ASSET_PATH = "C:\\Users\\shara\\flappy bird\\"
BACKGROUND_DAY = ASSET_PATH + "background-day.png"
BACKGROUND_NIGHT = ASSET_PATH + "background-night.png"
BIRD = ASSET_PATH + "bird.png"
PIPE = ASSET_PATH + "pipe.png"
BASE = ASSET_PATH + "base.png"

# Load images
bird_image = pygame.image.load(BIRD).convert_alpha()
background_image = pygame.image.load(BACKGROUND_DAY).convert()
pipe_image = pygame.image.load(PIPE).convert_alpha()
base_image = pygame.image.load(BASE).convert()

# Game variables
bird_y = SCREEN_HEIGHT // 2
bird_x = SCREEN_WIDTH // 4
bird_movement = 0
pipes = []
pipe_gap = 100
pipe_frequency = 1500  # milliseconds
last_pipe = pygame.time.get_ticks() - pipe_frequency

# Set up the display
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
clock = pygame.time.Clock()

# Function to create pipes
def create_pipe():
    pipe_height = random.randint(150, 350)
    bottom_pipe = pipe_image.get_rect(midtop=(SCREEN_WIDTH + 50, pipe_height))
    top_pipe = pipe_image.get_rect(midbottom=(SCREEN_WIDTH + 50, pipe_height - pipe_gap))
    return bottom_pipe, top_pipe

# Function to move pipes
def move_pipes(pipes):
    for pipe in pipes:
        pipe.centerx -= 5
    return [pipe for pipe in pipes if pipe.right > 0]

# Function to draw pipes
def draw_pipes(pipes):
    for pipe in pipes:
        if pipe.bottom >= SCREEN_HEIGHT:
            screen.blit(pipe_image, pipe)
        else:
            flip_pipe = pygame.transform.flip(pipe_image, False, True)
            screen.blit(flip_pipe, pipe)

# Main game loop
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                bird_movement = 0
                bird_movement -= 6
    
    # Bird movement
    bird_movement += GRAVITY
    bird_y += bird_movement
    bird_rect = bird_image.get_rect(center=(bird_x, bird_y))
    
    # Pipe generation
    time_now = pygame.time.get_ticks()
    if time_now - last_pipe > pipe_frequency:
        last_pipe = time_now
        pipes.extend(create_pipe())
    
    pipes = move_pipes(pipes)
    
    # Draw everything
    screen.blit(background_image, (0, 0))
    draw_pipes(pipes)
    screen.blit(bird_image, bird_rect)
    screen.blit(base_image, (0, SCREEN_HEIGHT - base_image.get_height()))
    
    pygame.display.update()
    clock.tick(FPS)
