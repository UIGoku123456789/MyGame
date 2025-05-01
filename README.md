# Evade With Tank
It is a simple 2D game Made With Pygame

    import pygame
    from sys import exit
    
    pygame.init()  # Initializes all of pygame
    
    def dynamic_score():
        cur_time = int(pygame.time.get_ticks() / 100) - start_time
        score = score_font.render(f'Score:{cur_time}', False, 'Black')  # text, antialiasing, colour of text
        score_rect = score.get_rect(center=(767.5, 70))
        screen.blit(score, score_rect)
        return cur_time
    
    p = 0
    start_time = 0
    missile_cooldown = 50  # 5 seconds
    missile_ready_time = 0
    missile_active = False
    missile_speed = 8
    missile_rect = pygame.Rect(0, 0, 0, 0)
    
    mob1_respawn_time = 0
    mob1_active = True
    mob2_respawn_time = 0
    mob2_active = True
    
    game_active = False
    Score = 0
    screen = pygame.display.set_mode((1535, 840))  # width,height
    pygame.display.set_caption('Evade With Tank')
    Time = pygame.time.Clock()
    
    nice_surface = pygame.image.load('Graphics/BKG.jpg').convert()
    img = pygame.image.load('Graphics/player.png').convert()
    mob1 = pygame.image.load('Graphics/Mob1.png').convert_alpha()
    mob2 = pygame.image.load('Graphics/Mob2.png').convert_alpha()
    player = pygame.image.load('Graphics/player.png').convert_alpha()
    game_intro = pygame.image.load('Graphics/Game_intro.png').convert_alpha()
    missile_img = pygame.image.load('Graphics/missile.png').convert_alpha()
    
    player_rect = player.get_rect(midbottom=(100, 800))  # makes a rectangle of player size around player(hit box)
    mob1_rect = mob1.get_rect(midbottom=(1650, 815))  # makes a rectangle of mob1 size around mob1(hit box)
    mob2_rect = mob2.get_rect(midbottom=(1850, 790))
    game_intro_rect = game_intro.get_rect(midbottom=(767.5, 600))
    
    pygame.display.set_icon(img)
    
    score_font = pygame.font.Font(None, 50)  # font type, font size
    intro_font = pygame.font.Font(None, 50)
    help_font = pygame.font.Font(None, 30)
    intro = intro_font.render('Press space to start New Game', True, 'Green')
    intro_rect = intro.get_rect(center=(767.5, 700))
    game_over_font = pygame.font.Font(None, 75)
    game_over = game_over_font.render('GAME OVER', False, 'Red')
    game_over_rect = game_over.get_rect(center=(767.5, 150))
    
    # New code for How To Play button and help screen
    how_to_play_font = pygame.font.Font(None, 40)
    how_to_play = how_to_play_font.render('How To Play', True, 'White')
    how_to_play_rect = how_to_play.get_rect(center=(767.5, 770))
    how_to_play_box = pygame.Rect(how_to_play_rect.left - 10, how_to_play_rect.top - 5, how_to_play_rect.width + 20, how_to_play_rect.height + 10)
    
    show_help = False  # Flag to track if the help screen is being shown
    
    player_gravity = 0
    
    while True:  # makes the game not close instantly(after the 1st frame)
        # This is where all the dynamic aspects of the game's code will reside
    
        for event in pygame.event.get():  # This is called event loop
            if event.type == pygame.QUIT:
                pygame.quit()  # Does the opposite of init(). uninitializes everything.
                exit()  # Terminates the execution of the program
            if game_active:
                if event.type == pygame.KEYDOWN:  # checks if any key on keyboard is being pressed
                    if event.key == pygame.K_SPACE and player_rect.bottom == 790:  # checks if space bar is being pressed
                        player_gravity = -30
                    if event.key == pygame.K_LSHIFT:
                        current_time = int(pygame.time.get_ticks() / 100)
                        if current_time - missile_ready_time >= missile_cooldown:
                            missile_active = True
                            missile_ready_time = current_time
                            missile_rect = pygame.Rect(player_rect.centerx + 25, player_rect.centery - 35, 20, 10)
            else:
                if event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
                    game_active = True
                    mob1_rect.left = 1650
                    mob2_rect.left = 1850
                    start_time = int(pygame.time.get_ticks() / 100)
                    missile_ready_time = start_time  # missile not ready immediately
    
                # New event for button click
                if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
                    if how_to_play_box.collidepoint(event.pos):
                        show_help = True
    
                # Event for going back to intro screen (help section)
                if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
                    show_help = False
    
        if game_active:
            screen.blit(nice_surface, (0, 0))  # blit stands for block image transfer
            Score = dynamic_score()
    
            current_time = int(pygame.time.get_ticks() / 100)
    
            if mob1_active:
                if mob1_rect.left <= -150:
                    mob1_rect.left = 1650
                mob1_rect.left -= 5
                screen.blit(mob1, mob1_rect)
            elif current_time - mob1_respawn_time >= 20:  # 2 seconds in 100ms units
                mob1_active = True
                mob1_rect.left = 1650
    
            if mob2_active:
                if mob2_rect.left <= -150:
                    mob2_rect.left = 1650
                mob2_rect.left -= 3.5
                screen.blit(mob2, mob2_rect)
            elif current_time - mob2_respawn_time >= 20:
                mob2_active = True
                mob2_rect.left = 1650
    
            player_gravity += 1
            player_rect.y += player_gravity
            if player_rect.bottom >= 790:
                player_rect.bottom = 790
            screen.blit(player, player_rect)
    
            keys = pygame.key.get_pressed()
            if keys[pygame.K_RIGHT]:
                player_rect.right += 4.5
            elif keys[pygame.K_LEFT]:
                player_rect.left -= 4.5
    
            if player_rect.left <= -150:
                player_rect.left = 10
            if player_rect.right >= 1650:
                player_rect.left = 1400
    
            if missile_active:
                missile_rect.x += missile_speed
                screen.blit(missile_img, missile_rect)
                if missile_rect.left > 1600:
                    missile_active = False
    
                if mob1_active and missile_rect.colliderect(mob1_rect):
                    mob1_active = False
                    mob1_respawn_time = current_time
                    missile_active = False
    
                if mob2_active and missile_rect.colliderect(mob2_rect):
                    mob2_active = False
                    mob2_respawn_time = current_time
                    missile_active = False
    
            if (mob1_active and player_rect.colliderect(mob1_rect)) or (mob2_active and player_rect.colliderect(mob2_rect)):
                game_active = False
        else:
            mob2_rect.left = 1850
            mob1_rect.left = 1650
            player_rect.left = 100
            screen.fill('Blue')
            score_message = score_font.render(f'Your Score:{Score}', False, 'Black')
            score_message_rect = score_message.get_rect(center=(767.5, 700))
    
            if show_help:
                # Help screen display
                screen.fill('Blue')
                help_title = game_over_font.render('How To Play', False, 'Black')
                help_title_rect = help_title.get_rect(center=(767.5, 100))
                screen.blit(help_title, help_title_rect)
    
                controls = [
                    "Dodge the incoming mobs.",
                    "Use SPACE to jump (No double/triple jumps).",
                    "Use LEFT and RIGHT arrow keys to move.",
                    "Press LEFT SHIFT to shoot a missile (5-second cooldown).",
                    "Missiles can destroy mobs. If they touch you, the game ends.",
                    "Press ESC to go back to the main menu."
                ]
    
                for i, line in enumerate(controls):
                    line_surface = help_font.render(line, True, 'Green')
                    line_rect = line_surface.get_rect(center=(767.5, 180 + i * 40))
                    screen.blit(line_surface, line_rect)
    
            else:
                # Don't show the intro screen if help screen is showing
                if Score == 0:
                    screen.blit(game_intro, game_intro_rect)
                    pygame.draw.rect(screen, 'DarkGreen', how_to_play_box)
                    screen.blit(how_to_play, how_to_play_rect)
                else:
                    screen.blit(score_message, score_message_rect)
                    screen.blit(game_over, game_over_rect)
    
            # Show the intro only when the game is inactive and help isn't shown
            if not show_help and Score == 0:
                screen.blit(intro, intro_rect)
    
        pygame.display.update()  # updates the variable "screen"
        Time.tick(60)  # This sets the maximum frame rate to 60
