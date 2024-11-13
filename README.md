import pygame
import random

# Initialize Pygame
pygame.init()

# Game constants
SCREEN_WIDTH = 400
SCREEN_HEIGHT = 600
BIRD_WIDTH = 40
BIRD_HEIGHT = 40
PIPE_WIDTH = 60
PIPE_GAP = 150
PIPE_VELOCITY = 3
GRAVITY = 0.5
JUMP_STRENGTH = -10
FPS = 60

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)
RED = (255, 0, 0)

# Set up the screen
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Flappy Bird")

# Clock for controlling frame rate
clock = pygame.time.Clock()

# Bird class
class Bird:
    def __init__(self):
        self.x = 50
        self.y = SCREEN_HEIGHT // 2
        self.width = BIRD_WIDTH
        self.height = BIRD_HEIGHT
        self.velocity = 0

    def move(self):
        self.velocity += GRAVITY
        self.y += self.velocity

    def jump(self):
        self.velocity = JUMP_STRENGTH

    def draw(self, screen):
        pygame.draw.rect(screen, RED, (self.x, self.y, self.width, self.height))

# Pipe class
class Pipe:
    def __init__(self):
        self.x = SCREEN_WIDTH
        self.height = random.randint(100, SCREEN_HEIGHT - PIPE_GAP - 100)
        self.gap = PIPE_GAP
        self.width = PIPE_WIDTH
        self.passed = False

    def move(self):
        self.x -= PIPE_VELOCITY

    def draw(self, screen):
        pygame.draw.rect(screen, GREEN, (self.x, 0, self.width, self.height))
        pygame.draw.rect(screen, GREEN, (self.x, self.height + self.gap, self.width, SCREEN_HEIGHT - self.height - self.gap))

# Function to display score
def draw_score(screen, score):
    font = pygame.font.SysFont(None, 36)
    score_text = font.render(f"Score: {score}", True, BLACK)
    screen.blit(score_text, (10, 10))

# Main game loop
def game():
    bird = Bird()
    pipes = [Pipe()]
    score = 0
    running = True
    while running:
        clock.tick(FPS)

        # Handle events
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            if event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
                bird.jump()

        # Move the bird
        bird.move()

        # Move pipes and check for collisions
        for pipe in pipes[:]:
            pipe.move()

            if pipe.x + pipe.width < 0:
                pipes.remove(pipe)

            # Check if bird collides with pipe
            if pipe.x < bird.x + bird.width < pipe.x + pipe.width:
                if bird.y < pipe.height or bird.y + bird.height > pipe.height + pipe.gap:
                    running = False

            # Check if bird has passed pipe
            if pipe.x + pipe.width < bird.x and not pipe.passed:
                pipe.passed = True
                score += 1

        # Add new pipes
        if pipes[-1].x < SCREEN_WIDTH - 300:
            pipes.append(Pipe())

        # Draw everything
        screen.fill(WHITE)
        bird.draw(screen)
        for pipe in pipes:
            pipe.draw(screen)
        draw_score(screen, score)

        # Check for game over (bird out of bounds)
        if bird.y >= SCREEN_HEIGHT or bird.y <= 0:
            running = False

        # Update display
        pygame.display.flip()

    # Game over screen
    font = pygame.font.SysFont(None, 48)
    game_over_text = font.render("Game Over", True, BLACK)
    screen.blit(game_over_text, (SCREEN_WIDTH // 3, SCREEN_HEIGHT // 3))
    final_score_text = font.render(f"Score: {score}", True, BLACK)
    screen.blit(final_score_text, (SCREEN_WIDTH // 3, SCREEN_HEIGHT // 2))
    pygame.display.flip()
    pygame.time.wait(2000)

# Run the game
if __name__ == "__main__":
    game()

# Quit Pygame
pygame.quit()
