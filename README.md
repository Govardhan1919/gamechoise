# gamechoise
TWO GAMES IN ONE CODE 
# Game Selector

import pygame
import time
import random
import sys

pygame.init()

# Constants for Screen
screen_width = 800
screen_height = 800
screen = pygame.display.set_mode((screen_width, screen_height))
pygame.display.set_caption("Game Selector")

# Colors
black = (0, 0, 0)
white = (255, 255, 255)
red = (213, 50, 80)
green = (0, 255, 0)
blue = (50, 153, 213)
BG_COLOR = (28, 170, 156)
LINE_COLOR = (23, 145, 135)
CIRCLE_COLOR = (239, 231, 200)
CROSS_COLOR = (66, 66, 66)
TEXT_COLOR = (255, 255, 255)

# Font
font_style = pygame.font.SysFont(None, 50)
font = pygame.font.Font(None, 74)

def display_message(msg, color):
    mesg = font_style.render(msg, True, color)
    screen.blit(mesg, [screen_width / 6, screen_height / 3])
    pygame.display.update()

def snake_game():
    snake_block = 10
    snake_speed = 10
    snake_length = 0

    def snake(snake_block, snake_list):
        for x in snake_list:
            pygame.draw.rect(screen, green, [x[0], x[1], snake_block, snake_block])

    def gameLoop():
        nonlocal snake_length
        game_over = False
        game_close = False

        x1 = screen_width / 2
        y1 = screen_height / 2

        x1_change = 0
        y1_change = 0

        snake_list = []
        length_of_snake = 1

        foodx = round(random.randrange(0, screen_width - snake_block) / 10.0) * 10.0
        foody = round(random.randrange(0, screen_height - snake_block) / 10.0) * 10.0

        while not game_over:
            while game_close:
                screen.fill(black)
                display_message("Game Over! Q-Quit or C-Play Again", white)
                for event in pygame.event.get():
                    if event.type == pygame.KEYDOWN:
                        if event.key == pygame.K_q:
                            game_over = True
                            game_close = False
                        if event.key == pygame.K_c:
                            gameLoop()

            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    game_over = True
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_LEFT:
                        x1_change = -snake_block
                        y1_change = 0
                    elif event.key == pygame.K_RIGHT:
                        x1_change = snake_block
                        y1_change = 0
                    elif event.key == pygame.K_UP:
                        y1_change = -snake_block
                        x1_change = 0
                    elif event.key == pygame.K_DOWN:
                        y1_change = snake_block
                        x1_change = 0

            if x1 >= screen_width or x1 < 0 or y1 >= screen_height or y1 < 0:
                game_close = True
            x1 += x1_change
            y1 += y1_change
            screen.fill(black)
            pygame.draw.rect(screen, blue, [foodx, foody, snake_block, snake_block])
            snake_head = [x1, y1]
            snake_list.append(snake_head)
            if len(snake_list) > length_of_snake:
                del snake_list[0]

            for x in snake_list[:-1]:
                if x == snake_head:
                    game_close = True

            snake(snake_block, snake_list)
            snake_length = length_of_snake  # Update snake length
            length_text = font_style.render(f'Snake Length: {snake_length}', True, white)
            screen.blit(length_text, [10, 10])  # Display snake length
            pygame.display.update()

            if x1 == foodx and y1 == foody:
                foodx = round(random.randrange(0, screen_width - snake_block) / 10.0) * 10.0
                foody = round(random.randrange(0, screen_height - snake_block) / 10.0) * 10.0
                length_of_snake += 1

            time.sleep(1 / snake_speed)

        main_menu()

    gameLoop()

def tic_tac_toe_game():
    WIDTH, HEIGHT = 800, 800
    LINE_WIDTH = 15
    BOARD_ROWS, BOARD_COLS = 3, 3
    SQUARE_SIZE = WIDTH // BOARD_COLS
    CIRCLE_RADIUS = SQUARE_SIZE // 3
    CIRCLE_WIDTH = 15
    CROSS_WIDTH = 25
    SPACE = SQUARE_SIZE // 4

    screen.fill(BG_COLOR)
    pygame.display.set_caption('Tic Tac Toe')

    def draw_lines():
        pygame.draw.line(screen, LINE_COLOR, (0, SQUARE_SIZE), (WIDTH, SQUARE_SIZE), LINE_WIDTH)
        pygame.draw.line(screen, LINE_COLOR, (0, SQUARE_SIZE * 2), (WIDTH, SQUARE_SIZE * 2), LINE_WIDTH)
        pygame.draw.line(screen, LINE_COLOR, (SQUARE_SIZE, 0), (SQUARE_SIZE, HEIGHT), LINE_WIDTH)
        pygame.draw.line(screen, LINE_COLOR, (SQUARE_SIZE * 2, 0), (SQUARE_SIZE * 2, HEIGHT), LINE_WIDTH)

    draw_lines()

    board = [[None for _ in range(BOARD_COLS)] for _ in range(BOARD_ROWS)]
    player = 1
    game_over = False

    def draw_figures():
        for row in range(BOARD_ROWS):
            for col in range(BOARD_COLS):
                if board[row][col] == 1:
                    pygame.draw.line(screen, CROSS_COLOR, (col * SQUARE_SIZE + SPACE, row * SQUARE_SIZE + SQUARE_SIZE - SPACE),
                                     (col * SQUARE_SIZE + SQUARE_SIZE - SPACE, row * SQUARE_SIZE + SPACE), CROSS_WIDTH)
                    pygame.draw.line(screen, CROSS_COLOR, (col * SQUARE_SIZE + SPACE, row * SQUARE_SIZE + SPACE),
                                     (col * SQUARE_SIZE + SQUARE_SIZE - SPACE, row * SQUARE_SIZE + SQUARE_SIZE - SPACE), CROSS_WIDTH)
                elif board[row][col] == 2:
                    pygame.draw.circle(screen, CIRCLE_COLOR, (col * SQUARE_SIZE + SQUARE_SIZE // 2, row * SQUARE_SIZE + SQUARE_SIZE // 2), CIRCLE_RADIUS, CIRCLE_WIDTH)

    def check_win(player):
        for row in range(BOARD_ROWS):
            if all([cell == player for cell in board[row]]):
                return True
        for col in range(BOARD_COLS):
            if all([board[row][col] == player for row in range(BOARD_ROWS)]):
                return True
        if all([board[i][i] == player for i in range(BOARD_ROWS)]) or all([board[i][BOARD_ROWS - 1 - i] == player for i in range(BOARD_ROWS)]):
            return True
        return False

    def check_draw():
        return all(cell is not None for row in board for cell in row)

    def display_winner(winner):
        text = font.render(f'Player {winner} wins!R-Restart..Q-Quit', True, TEXT_COLOR)
        screen.blit(text, (WIDTH // 200, HEIGHT // 2))
        pygame.display.update()
        while True:
            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_r:
                        reset_game()
                        return
                    if event.key == pygame.K_q:
                        main_menu()

    def display_draw():
        text = font.render(f'Draw!R-Restart..Q-Quit', True, TEXT_COLOR)
        screen.blit(text, (WIDTH // 20, HEIGHT // 2))
        pygame.display.update()
        while True:
            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_r:
                        reset_game()
                        return
                    if event.key == pygame.K_q:
                        main_menu()

    def reset_game():
        nonlocal board, player, game_over
        board = [[None for _ in range(BOARD_COLS)] for _ in range(BOARD_ROWS)]
        player = 1
        game_over = False
        screen.fill(BG_COLOR)
        draw_lines()

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.MOUSEBUTTONDOWN and not game_over:
                mouseX = event.pos[0]
                mouseY = event.pos[1]
                clicked_row = mouseY // SQUARE_SIZE
                clicked_col = mouseX // SQUARE_SIZE
                if board[clicked_row][clicked_col] is None:
                    board[clicked_row][clicked_col] = player
                    if check_win(player):
                        game_over = True
                        display_winner(player)
                    elif check_draw():
                        game_over = True
                        display_draw()
                    player = 2 if player == 1 else 1
                    screen.fill(BG_COLOR)
                    draw_lines()
                    draw_figures()
            if event.type == pygame.KEYDOWN and game_over:
                if event.key == pygame.K_q:
                    main_menu()

        pygame.display.update()

def main_menu():
    while True:
        screen.fill(black)
        display_message("Press 1:Snake, 2:Tic Tac Toe, Q:Quit", white)
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_1:
                    snake_game()
                elif event.key == pygame.K_2:
                    tic_tac_toe_game()
                elif event.key == pygame.K_q:
                    pygame.quit()
                    sys.exit()

main_menu()
