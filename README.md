import pygame
import sys
import random

# Inicialização do Pygame
pygame.init()

# Definição de cores
PRETO = (0, 0, 0)
BRANCO = (255, 255, 255)
VERMELHO = (255, 0, 0)
VERDE = (0, 255, 0)

# Configuração da tela
LARGURA_TELA = 800
ALTURA_TELA = 600
tela = pygame.display.set_mode((LARGURA_TELA, ALTURA_TELA))

# Configuração da interface
interface_rect = pygame.Rect(50, 50, 200, 300)
interface_velocidade = 2

# Botões
botoes = [
    {"texto": "Autoparry", "x": interface_rect.x + 20, "y": interface_rect.y + 20, "ativo": False},
    {"texto": "Atacar", "x": interface_rect.x + 20, "y": interface_rect.y + 60, "ativo": False},
    {"texto": "Defender", "x": interface_rect.x + 20, "y": interface_rect.y + 100, "ativo": False},
]

# Configuração do jogo
autoparry_ativo = False
jogador_vida = 100
inimigo_vida = 100

# Função para desenhar a interface
def desenhar_interface():
    pygame.draw.rect(tela, PRETO, interface_rect)
    for botao in botoes:
        font = pygame.font.Font(None, 24)
        if botao["ativo"]:
            pygame.draw.rect(tela, VERDE, (botao["x"] - 5, botao["y"] - 5, 150, 30))
        texto = font.render(botao["texto"], True, BRANCO)
        tela.blit(texto, (botao["x"], botao["y"]))

# Função para atualizar a posição da interface
def atualizar_interface():
    global interface_rect
    interface_rect.x += interface_velocidade
    if interface_rect.x > LARGURA_TELA - interface_rect.width or interface_rect.x < 0:
        interface_velocidade *= -1
    for botao in botoes:
        botao["x"] = interface_rect.x + 20

# Função para verificar clique nos botões
def verificar_clique(pos):
    global autoparry_ativo, jogador_vida, inimigo_vida
    for botao in botoes:
        if botao["x"] - 5 < pos[0] < botao["x"] + 145 and botao["y"] - 5 < pos[1] < botao["y"] + 25:
            if botao["texto"] == "Autoparry":
                botao["ativo"] = not botao["ativo"]
                autoparry_ativo = botao["ativo"]
            elif botao["texto"] == "Atacar":
                inimigo_vida -= 10
                botao["ativo"] = True
                if autoparry_ativo:
                    jogador_vida -= 5
            elif botao["texto"] == "Defender":
                jogador_vida += 5
                botao["ativo"] = True

# Função para resetar botões
def resetar_botoes():
    for botao in botoes:
        if botao["texto"] != "Autoparry":
            botao["ativo"] = False

# Loop principal do jogo
while True:
    for evento in pygame.event.get():
        if evento.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        elif evento.type == pygame.MOUSEBUTTONDOWN:
            verificar_clique(evento.pos)

    tela.fill(BRANCO)

    desenhar_interface()
    atualizar_interface()
    resetar_botoes()

    font = pygame.font.Font(None, 36)
    texto = font.render(f"Jogador: {jogador_vida} - Inimigo: {inimigo_vida}", True, PRETO)
    tela.blit(texto, (LARGURA_TELA // 2 - 100, 20))

    pygame.display.flip()
    pygame.time.Clock().tick(60)
