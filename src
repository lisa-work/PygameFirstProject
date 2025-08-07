import pygame
import random
from pygame.constants import QUIT, K_DOWN, K_UP, K_LEFT, K_RIGHT

#PYGAME INITIALIZATION:
pygame.font.init()
pygame.display.init()
pygame.init()

#CONSTANTS:
FPS = pygame.time.Clock()  # Manage the game frame rate
HEIGHT = 800
WIDTH = 1200
COLOR_WHITE = (255, 255, 255)
COLOR_YELLOW = (255, 255, 0)
FONT = pygame.font.SysFont('Dosis', 45)
GAME_OVER_FONT = pygame.font.SysFont('Dosis', 270)
RESTART_GAME_FONT = pygame.font.SysFont('Dosis', 40)
PAUSE_GAME_FONT = pygame.font.SysFont('Dosis', 40)
PAUSE_FONT = pygame.font.SysFont('Dosis', 250)
main_display = pygame.display.set_mode((WIDTH, HEIGHT))

#LOAD IMAGES WITH ERROR HANDLING:
try:
    bg = pygame.transform.scale(pygame.image.load('space.png'), (WIDTH, HEIGHT))
    player = pygame.transform.scale(pygame.image.load('marvel.png').convert_alpha(), (100, 100))
    enemy_img = pygame.transform.scale(pygame.image.load('rocket.png'), (100, 50))
    bonus_img = pygame.transform.scale(pygame.image.load('crystal.png'), (45, 75))
    continue_img = pygame.transform.scale(pygame.image.load('continue.png'), (100, 75))
except pygame.error as e:
    print(f"Error loading assets: {e}")
    pygame.quit()
    exit()

#RESET GAME SETTINGS:
def reset_game():
    global player_rect, enemies, bonuses, score, bg_x1, bg_x2, game_over, pause
    #Reset player position
    player_rect = player.get_rect()
    player_rect.center = main_display.get_rect().center
    #Reset game elements
    enemies = []
    bonuses = []
    score = 0
    #Reset background positions
    bg_x1 = 0
    bg_x2 = bg.get_width()
    game_over = False
    pause = False

#BACKGROUND SETTINGS:
bg_x1 = 0
bg_x2 = bg.get_width()
bg_move = 1

#PLAYER SETTINGS:
player_rect = player.get_rect()
player_rect.center = main_display.get_rect().center
player_move = {
    K_DOWN: [0, 7],
    K_UP: [0, -7],
    K_LEFT: [-7, 0],
    K_RIGHT: [7, 0]
}

#ENEMY SETTINGS:
def create_enemy():
    enemy_rect = pygame.Rect(WIDTH, random.randint(1, HEIGHT - enemy_img.get_height() // 2), *enemy_img.get_size())
    enemy_move = [random.randint(-7, -4), 0]
    return [enemy_img, enemy_rect, enemy_move]

#BONUS SETTINGS:
def create_bonus():
    bonus_rect = pygame.Rect(random.randint(3, 1100 - bonus_img.get_width()), 0, *bonus_img.get_size())
    bonus_move = [0, random.randint(3, 6)]
    return [bonus_img, bonus_rect, bonus_move]

#CUSTOM EVENTS:
CREATE_ENEMY = pygame.USEREVENT + 1
pygame.time.set_timer(CREATE_ENEMY, 1000)
CREATE_BONUS = pygame.USEREVENT + 2
pygame.time.set_timer(CREATE_BONUS, 2500)

#PAUSE - CONTINUE SETTINGS:
continue_rect = pygame.Rect(WIDTH - 100, 25, *continue_img.get_size())
def draw_pause_screen():
    main_display.blit(continue_img, continue_rect)
    pause_text = PAUSE_FONT.render("Game Paused", True, COLOR_WHITE)
    main_display.blit(pause_text, (WIDTH // 2 - pause_text.get_width() // 2, HEIGHT // 2 - pause_text.get_height() // 2))
    continue_text = RESTART_GAME_FONT.render("Click on the screen or press any keys to continue", True, COLOR_WHITE)
    main_display.blit(continue_text, (WIDTH // 2 - 100, 750))

#STATUS SETTINGS:
reset_game()
playing = True
pause = False

#ACTION SETTINGS:
while playing:
    FPS.tick(80)  #Run the game at 80 frames per second

    for event in pygame.event.get():
        #When player quit the game
        if event.type == QUIT:
            playing = False

        #Resume the game
        elif not game_over and pause:
            #Resume the game by pressing any keys
            if event.type == pygame.KEYDOWN:
                pause = False
            #Resume the game by clicking anywhere on the screen
            if event.type == pygame.MOUSEBUTTONDOWN:
                pause = False

        #While the game is being played
        elif not game_over and not pause:
            #Pause the game by clicking anywhere on the screen
            if event.type == pygame.MOUSEBUTTONDOWN:
                pause = True
            #Append enemy and bonus to the corresponding list
            if event.type == CREATE_ENEMY:
                enemies.append(create_enemy())
            if event.type == CREATE_BONUS:
                bonuses.append(create_bonus())

        #Reset the game by clicking on the screen or pressing any keys after game over
        elif game_over:
            if event.type == pygame.KEYDOWN:
                reset_game()
            if event.type == pygame.MOUSEBUTTONDOWN:
                reset_game()

    #PLAYING ACTION:
    #Move the background for scrolling effect
    if not game_over and not pause:
        bg_x1 -= bg_move
        bg_x2 -= bg_move
        if bg_x1 < -bg.get_width():
            bg_x1 = bg.get_width()
        if bg_x2 < -bg.get_width():
            bg_x2 = bg.get_width()
        #Update the main display
        main_display.blit(bg, (bg_x1, 0))
        main_display.blit(bg, (bg_x2, 0))
        #Set up keys get pressed
        keys = pygame.key.get_pressed()
        #Move and update the player's movement
        for key, move in player_move.items():
            if keys[key]:
                player_rect = player_rect.move(move)
        main_display.blit(player, player_rect)
        #Move and update the enemies' movement
        for enemy in enemies:
            enemy[1] = enemy[1].move(enemy[2])
            main_display.blit(enemy[0], enemy[1])
            #End the game on collision with an enemy
            if player_rect.colliderect(enemy[1]):
                game_over = True  
        #Move and update the bonuses' movement
        for bonus in bonuses:
            bonus[1] = bonus[1].move(bonus[2])
            main_display.blit(bonus[0], bonus[1])
           #Increase score on collecting a bonus 
            if player_rect.colliderect(bonus[1]):
                bonuses.pop(bonuses.index(bonus))
                score += 1
    #PAUSE ACTION:
    elif pause and not game_over:
        draw_pause_screen()
    #GAME OVER ACTION:
    elif game_over:
        #Display "Game Over" message
        game_over_text = GAME_OVER_FONT.render("Game Over", True, COLOR_WHITE)
        main_display.blit(game_over_text, (WIDTH // 2 - game_over_text.get_width() // 2, HEIGHT // 2 - game_over_text.get_height() // 2))
        #Display "Restart Game" message
        restart_text = RESTART_GAME_FONT.render("Click on the screen or press any keys to replay", True, COLOR_WHITE)
        main_display.blit(restart_text, (WIDTH // 2 - 50, 750))

    #UPDATE THE DISPLAY
    #Display scores
    main_display.blit(FONT.render(("Score: ")+str(score), True, COLOR_WHITE), (12, 750))
    #Update the display
    pygame.display.flip()
    #Remove enemies and bonuses that have moved off the screen.
    for enemy in enemies:
        if enemy[1].left < 0:
            enemies.pop(enemies.index(enemy))
    for bonus in bonuses:
        if bonus[1].bottom > HEIGHT:
            bonuses.pop(bonuses.index(bonus))

#QUIT THE GAME
pygame.quit()
