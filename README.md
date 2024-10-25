# Atur4
# Variáveis globais
PLAYER_SIZE = 50
SPEED = 5
FALLING_SPEED = 5
falling_objects = []
particles = []
game_over = False

class Player:
    def __init__(self, x, y, size):
        self.x = x
        self.y = y
        self.size = size
    
    def move(self, direction):
        if direction == "LEFT" and self.x > 0:
            self.x -= SPEED
        elif direction == "RIGHT" and self.x < width - self.size:
            self.x += SPEED
    
    def draw(self):
        fill(0, 0, 255)  # Cor azul para o jogador
        rect(self.x, self.y, self.size, self.size)

class FallingObject:
    def __init__(self, x):
        self.x = x
        self.y = 0
    
    def update(self):
        self.y += FALLING_SPEED
    
    def draw(self):
        fill(255, 0, 0)  # Cor vermelha para os objetos
        rect(self.x, self.y, 30, 30)
    
    def is_off_screen(self):
        return self.y >= height

class Particle:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        self.size = random(5, 15)
        self.lifetime = 255

    def update(self):
        self.y -= 2
        self.lifetime -= 5
    
    def draw(self):
        fill(255, self.lifetime)
        ellipse(self.x, self.y, self.size, self.size)

def setup():
    size(600, 600)
    noStroke()
    global player
    player = Player(300, 500, PLAYER_SIZE)

def draw():
    global game_over
    background(200)
    
    if not game_over:
        # Desenha o jogador
        player.draw()
        
        # Cria objetos que caem
        if frameCount % 30 == 0:
            falling_objects.append(FallingObject(random(width)))
        
        # Atualiza e desenha objetos que caem
        for obj in falling_objects:
            obj.update()
            obj.draw()
        
        # Remove objetos que saíram da tela
        falling_objects[:] = [obj for obj in falling_objects if not obj.is_off_screen()]
        
        # Verifica colisões
        for obj in falling_objects:
            if (obj.x < player.x + player.size and 
                obj.x + 30 > player.x and 
                obj.y < player.y + player.size and 
                obj.y + 30 > player.y):
                game_over = True
                end_game()
        
        # Atualiza e desenha partículas
        for p in particles:
            p.update()
            p.draw()
        
        # Remove partículas que já não têm vida
        particles[:] = [p for p in particles if p.lifetime > 0]
    
    else:
        draw_restart_button()

def end_game():
    # Gera partículas na posição do jogador
    for _ in range(50):
        particles.append(Particle(player.x + PLAYER_SIZE / 2, player.y + PLAYER_SIZE / 2))
    
    fill(255)
    textSize(32)
    textAlign(CENTER, CENTER)
    text("Game Over!", width / 2, height / 2 - 40)
    textSize(20)
    text("Pressione R para reiniciar", width / 2, height / 2)

def draw_restart_button():
    fill(0, 255, 0)
    rect(width / 2 - 50, height / 2 + 20, 100, 30)
    fill(0)
    textSize(16)
    textAlign(CENTER, CENTER)
    text("Reiniciar", width / 2, height / 2 + 35)

def keyPressed():
    if game_over:
        if key == 'r' or key == 'R':
            restart_game()
    else:
        if keyCode == LEFT:
            player.move("LEFT")
        elif keyCode == RIGHT:
            player.move("RIGHT")

def restart_game():
    global falling_objects, particles, game_over, player
    falling_objects.clear()
    particles.clear()
    game_over = False
    player = Player(300, 500, PLAYER_SIZE)


