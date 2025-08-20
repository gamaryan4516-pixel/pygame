import pygame
import sys
import random

# Inicializa o Pygame
pygame.init()

# --- Configurações da Janela ---
LARGURA_TELA, ALTURA_TELA = 800, 600
tela = pygame.display.set_mode((LARGURA_TELA, ALTURA_TELA))
pygame.display.set_caption("Star Pixel Menu")

# --- Cores ---
PRETO = (13, 13, 13)
BRANCO = (224, 224, 224)
COR_TEMA_PRIMARIA = (51, 255, 51)
COR_TEMA_ESCURA = (77, 255, 77)
COR_BOTAO = (50, 50, 50)
COR_BOTAO_HOVER = COR_TEMA_PRIMARIA

# --- Fontes ---
# Tenta usar uma fonte de pixel art, se não encontrar, usa a padrão
try:
    fonte_titulo = pygame.font.Font("font/PressStart2P-Regular.ttf", 50)
    fonte_botao = pygame.font.Font("font/PressStart2P-Regular.ttf", 20)
except FileNotFoundError:
    print("Aviso: Fonte 'PressStart2P-Regular.ttf' não encontrada. Usando fonte padrão.")
    fonte_titulo = pygame.font.Font(None, 74)
    fonte_botao = pygame.font.Font(None, 30)

# --- Classe para as Estrelas de Fundo ---
class Estrela:
    def __init__(self):
        self.x = random.randint(0, LARGURA_TELA)
        self.y = random.randint(0, ALTURA_TELA)
        self.tamanho = random.randint(1, 3)
        self.velocidade = random.uniform(0.5, 1.5)

    def mover(self):
        self.y += self.velocidade
        if self.y > ALTURA_TELA:
            self.y = 0
            self.x = random.randint(0, LARGURA_TELA)

    def desenhar(self, superficie):
        pygame.draw.circle(superficie, BRANCO, (self.x, self.y), self.tamanho)

# --- Classe para os Botões ---
class Botao:
    def __init__(self, x, y, largura, altura, texto, acao=None):
        self.rect = pygame.Rect(x, y, largura, altura)
        self.texto = texto
        self.acao = acao
        self.cor = COR_BOTAO
        self.cor_texto = BRANCO
        self.hover = False

    def desenhar(self, superficie):
        # Altera a cor se o mouse estiver sobre o botão
        cor_atual = COR_BOTAO_HOVER if self.hover else self.cor
        cor_texto_atual = PRETO if self.hover else self.cor_texto
        
        pygame.draw.rect(superficie, cor_atual, self.rect, border_radius=8)
        
        texto_surf = fonte_botao.render(self.texto, True, cor_texto_atual)
        texto_rect = texto_surf.get_rect(center=self.rect.center)
        superficie.blit(texto_surf, texto_rect)

    def verificar_hover(self, pos_mouse):
        self.hover = self.rect.collidepoint(pos_mouse)

    def executar_acao(self):
        if self.acao:
            self.acao()

# --- Funções de Ação para os Botões (placeholders) ---
def acao_novo_jogo():
    print("Botão 'Novo Jogo' clicado!")

def acao_continuar():
    print("Botão 'Continuar' clicado!")

def acao_opcoes():
    print("Botão 'Opções' clicado!")

def acao_sair():
    print("Botão 'Sair' clicado! Fechando o programa.")
    pygame.quit()
    sys.exit()

# --- Função Principal do Menu ---
def menu_principal():
    # Cria as estrelas
    estrelas = [Estrela() for _ in range(100)]

    # Cria os botões do menu
    botoes = [
        Botao(LARGURA_TELA // 2 - 150, 250, 300, 50, "Novo Jogo", acao_novo_jogo),
        Botao(LARGURA_TELA // 2 - 150, 310, 300, 50, "Continuar", acao_continuar),
        Botao(LARGURA_TELA // 2 - 150, 370, 300, 50, "Opcoes", acao_opcoes),
        Botao(LARGURA_TELA // 2 - 150, 430, 300, 50, "Sair", acao_sair),
    ]

    clock = pygame.time.Clock()

    # Loop principal do menu
    while True:
        pos_mouse = pygame.mouse.get_pos()

        # Processamento de eventos
        for evento in pygame.event.get():
            if evento.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if evento.type == pygame.MOUSEBUTTONDOWN:
                if evento.button == 1: # Botão esquerdo do mouse
                    for botao in botoes:
                        if botao.hover:
                            botao.executar_acao()

        # Atualiza a lógica
        for estrela in estrelas:
            estrela.mover()
        
        for botao in botoes:
            botao.verificar_hover(pos_mouse)

        # Desenha na tela
        tela.fill(PRETO)

        # Desenha as estrelas
        for estrela in estrelas:
            estrela.desenhar(tela)

        # Desenha o título
        titulo_surf = fonte_titulo.render("Star Pixel", True, COR_TEMA_PRIMARIA)
        titulo_rect = titulo_surf.get_rect(center=(LARGURA_TELA // 2, 150))
        tela.blit(titulo_surf, titulo_rect)

        # Desenha os botões
        for botao in botoes:
            botao.desenhar(tela)

        # Atualiza a tela
        pygame.display.flip()

        # Controla o FPS
        clock.tick(60)

# Inicia o menu
if __name__ == "__main__":
    menu_principal()
