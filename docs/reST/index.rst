import pygame
import random

# Initialize Pygame
pygame.init()

# Define screen dimensions
SCREEN_WIDTH = 400
SCREEN_HEIGHT = 600

# Define colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)

# Create screen
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Flappy Bird")

# Define FPS (frames per second) controller
clock = pygame.time.Clock()

# Load images (bird and pipes)
bird_img = pygame.Surface((30, 30))
bird_img.fill(BLUE)

# Define game variables
bird_x = 50
bird_y = SCREEN_HEIGHT // 2
bird_velocity = 0
gravity = 0.5
flap_power = -10
pipe_width = 60
pipe_gap = 150
pipe_velocity = 3
pipes = []
score = 0

# Font for score
font = pygame.font.SysFont('Arial', 30)

def create_pipe():
    """Create a new pipe with random height."""
    height = random.randint(100, SCREEN_HEIGHT - 100)
    top_rect = pygame.Rect(SCREEN_WIDTH, 0, pipe_width, height)
    bottom_rect = pygame.Rect(SCREEN_WIDTH, height + pipe_gap, pipe_width, SCREEN_HEIGHT - height - pipe_gap)
    return top_rect, bottom_rect

def draw_pipes():
    """Draw all pipes on the screen."""
    global pipes
    for pipe in pipes:
        pygame.draw.rect(screen, GREEN, pipe[0])
        pygame.draw.rect(screen, GREEN, pipe[1])

def move_pipes():
    """Move pipes to the left and remove off-screen pipes."""
    global pipes, score
    for pipe in pipes:
        pipe[0].x -= pipe_velocity
        pipe[1].x -= pipe_velocity
    pipes = [pipe for pipe in pipes if pipe[0].x > -pipe_width]
    
    # Check for scoring
    for pipe in pipes:
        if pipe[0].x == bird_x:
            score += 1

def check_collisions():
    """Check for collisions with the pipes or ground."""
    global bird_y
    # Check collision with ground
    if bird_y >= SCREEN_HEIGHT - 30:
        return True
    
    # Check collision with pipes
    for pipe in pipes:
        if pipe[0].colliderect(pygame.Rect(bird_x, bird_y, 30, 30)) or pipe[1].colliderect(pygame.Rect(bird_x, bird_y, 30, 30)):
            return True
    return False

def main():
    global bird_y, bird_velocity, pipes, score
    
    # Main game loop
    running = True
    while running:
        clock.tick(60)  # Set FPS
        
        # Handle events
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    bird_velocity = flap_power  # Flap bird upwards
        
        # Apply gravity to the bird
        bird_velocity += gravity
        bird_y += bird_velocity
        
        # Create pipes
        if random.randint(1, 100) <= 2:  # Random chance to generate a pipe
            pipes.append(create_pipe())
        
        # Move pipes and check for collisions
        move_pipes()
        
        if check_collisions():
            running = False
        
        # Fill the screen with white
        screen.fill(WHITE)
        
        # Draw pipes
        draw_pipes()
        
        # Draw bird
        screen.blit(bird_img, (bird_x, bird_y))
        
        # Draw score
        score_text = font.render(f"Score: {score}", True, BLACK)
        screen.blit(score_text, (10, 10))
        
        # Update display
        pygame.display.flip()
    
    pygame.quit()

# Run the game
if __name__ == "__main__":
    main()
