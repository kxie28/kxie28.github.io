Simple Galaga type game
^^^^^^^^^^^^^^^^^^^^^^^
::

        import pygame
        import random


        width = 800
        height = 600
        FPS = 30

        pygame.init()
        display_screen = pygame.display.set_mode( (width,height) )
        pygame.display.set_caption("test game")
        clock = pygame.time.Clock() #set to keep FPS at what we need it at

        ship_img = pygame.image.load('ship.png')
        meteors_img = pygame.image.load('meteors.png')
        lasers_img = pygame.image.load('laser.png')
        FONT = "shooter_font.ttf"
        x = (width * 0.45)
        y = (height * 0.88)

        #           R    G    B
        WHITE 	= (255, 255, 255)
        GREEN 	= (78, 255, 87)
        YELLOW 	= (241, 255, 0)
        BLUE 	= (80, 255, 239)
        PURPLE 	= (203, 0, 255)
        RED 	= (237, 28, 36)
        BLACK 	= (0,	0, 	 0)

        class Background(pygame.sprite.Sprite):
        	def __init__(self, image_file, location):
        		pygame.sprite.Sprite.__init__(self) #call Sprite initializer
        		self.image = pygame.image.load('background.png')
        		self.rect = self.image.get_rect()
        		self.rect.left, self.rect.top = location
        		self.rect.center = (width/2, height/2)

        class Player(pygame.sprite.Sprite):
            def __init__(self):
                pygame.sprite.Sprite.__init__(self)
                #mself.image = pygame.Surface((50, 50))
                self.image = ship_img
                self.rect = self.image.get_rect()
                self.rect.centerx = width / 2
                self.rect.bottom = height - 10
                self.speedx = 0
        
            def update(self):
                self.speedx = 0
                keystate = pygame.key.get_pressed()
                if keystate[pygame.K_LEFT]:
                    self.speedx = -12
                if keystate[pygame.K_RIGHT]:
                    self.speedx = 12
                self.rect.x += self.speedx
                if self.rect.right > width:
                    self.rect.right = width
                if self.rect.left < 0:
                    self.rect.left = 0    
            
            def shoot(self):
                laser = Laser(self.rect.centerx, self.rect.top)
              	all_sprites.add(laser)
        	lasers.add(laser)

        class Enemy(pygame.sprite.Sprite):
        	def __init__(self):
        		pygame.sprite.Sprite.__init__(self)
        		#self.image = pygame.Surface((30,40))
        		self.image = meteors_img
        		self.image.set_colorkey(BLACK)
        		self.rect = self.image.get_rect()
        		self.radius = int(self.rect.width * .85 / 2)
        		self.rect.x = random.randrange(width - self.rect.width)
        		self.rect.y = random.randrange(-100, -40)
        		self.speedy = random.randrange(1,8) #random enemies come from the top
        		self.speedx = random.randrange(-3, 3) #random enemies can also come from the sides
        		#add rotation to the meteors
        		self.rot = 0
        		self.rot_speed = random.randrange(-8,8)
        		self.last_update = pygame.time.get_ticks()

        	def rotate(self):
                        now = pygame.time.get_ticks()
        		if now - self.last_update > 50:
        			self.last_update = now
        			self.rot = (self.rot + self.rot_speed) % 360
        			old_center = self.rect.center
        			self.rect = self.image.get_rect()
        			self.rect.center = old_center

        	def update(self):
        		self.rotate()
        		self.rect.y += self.speedy
        		self.rect.x += self.speedx
        		if self.rect.top > height + 10 or self.rect.left < -25 or self.rect.right > width +20:
        			self.rect.x = random.randrange(width - self.rect.width)
        			self.rect.y = random.randrange(-100, -40)
        			self.speedy = random.randrange(1, 8)

        class Laser(pygame.sprite.Sprite):
        	def __init__(self, x ,y):
        		pygame.sprite.Sprite.__init__(self)
        		#self.image = pygame.Surface((10,20))
        		self.image = lasers_img
        		self.image.set_colorkey(BLACK)
        		self.rect = self.image.get_rect()
        		self.rect.bottom = y
        		self.rect.centerx = x
        		self.speedy = -10

        	def update(self):
        		self.rect.y += self.speedy
        		#bullet ends when it's off the screen
        		if self.rect.bottom < 0:
        			self.kill()

        all_sprites = pygame.sprite.Group()
        enemies = pygame.sprite.Group()
        lasers = pygame.sprite.Group()
        player = Player()
        all_sprites.add(player)
        for i in range(8):
        	dots = Enemy()
        	all_sprites.add(dots)
        	enemies.add(dots)
        score = 0

        def score_text(surface, text, size, x, y):
        	font = pygame.font.Font(FONT, size)
        	surface_text = font.render(text, True, WHITE)
        	text_rect = surface_text.get_rect()
        	text_rect.midtop = (x, y)
        	surface.blit(surface_text, text_rect)

        def text(text, font):
        	textSurface = font.render(text, True, BLACK)
        	return textSurface, textSurface.get_rect()

        def crashed():
        	message_display("You died")
                game_loop()

        def button(msg,x,y,w,h,ic,ac,action=None):
        	mouse = pygame.mouse.get_pos()
        	click = pygame.mouse.get_pressed()

        	if x+w > mouse[0] > x and y+h > mouse[1] > y:
        		pygame.draw.rect(display_screen,ac,(x,y,w,h))
        		if click[0] == 1 and action != None:
        			action()
                else:
        		pygame.draw.rect(display_screen, ic, (x,y,w,h))

        	smallText = pygame.font.Font("shooter_font.ttf",20)
        	textMsg, textCoords = text(msg, smallText)
        	textCoords.center = ((x+(w/2)), (y+(h/2)))
        	display_screen.blit(textMsg, textCoords)

        def quitgame():
        	pygame.quit()
        	quit()

        def intro_screen():
        	intro = True

        	while intro:
        		for event in pygame.event.get():
        			if event.type == pygame.QUIT:
        				pygame.quit()
        				quit()
        		BackGround = Background('background.png', [0,0])
        		display_screen.fill([255,255,255])
        		display_screen.blit(BackGround.image, BackGround.rect)		
        		#display_screen.fill(WHITE)
        		myText = pygame.font.Font('shooter_font.ttf',90)
        		text = myText.render("KXSW GALAGA", 1, (255,255,255))
        		#TextMsg, TextCoords = text("KXSW GALAGA", largeText)
        		#TextCoords.center = ((width/2),(height/2))
        		display_screen.blit(text, (60,240))
        		#display_screen.blit(TextMsg, TextCoords)

                        button("GO", 150,450,100,50,GREEN,YELLOW,game_loop)
        		button("Quit",550,450,100,50,RED,YELLOW,quitgame)

        		pygame.display.update()
        		clock.tick(15)

        def game_loop():
        	score = 0
        	game_running = True
        	while game_running:
        		#FPS
        		clock.tick(FPS)
        		inputs
        		for event in pygame.event.get():
        			if event.type == pygame.QUIT:
        				game_running = False
        			elif event.type == pygame.KEYDOWN:
        				if event.key == pygame.K_SPACE:
        					player.shoot()

        		#Update the screen
        		all_sprites.update()

        		#handle bullets hitting enemies
        		collides = pygame.sprite.groupcollide(enemies, lasers, True, True)
        		for collide in collides:
        			score += 50 - collide.radius
        			dots = Enemy()
        			all_sprites.add(dots)
        			enemies.add(dots)

        		#handle collisions
        		collide = pygame.sprite.spritecollide(player, enemies, False)
        		if collide:
        			game_running = False
        		
        		#Set the background
        		BackGround = Background('background.png', [0,0])
        		display_screen.fill([255,255,255])
        		display_screen.blit(BackGround.image, BackGround.rect)
        		all_sprites.draw(display_screen)
        		score_text(display_screen, str(score), 18, width/2, 10)
        		pygame.display.flip()

        intro_screen()
        game_loop()


