# PROJETO DIÁRIO


# Importação dos módulos necessários
import tkinter as tk  # Módulo principal para criar interfaces gráficas em Python
from tkinter import messagebox, scrolledtext  # Widgets adicionais do tkinter: caixas de mensagem e campo de texto com rolagem
from datetime import datetime  # Usado para obter data e hora atual
import os  # Fornece funções para interagir com o sistema de arquivos

# Constante que define o nome do arquivo onde as anotações serão salvas
ARQUIVO = "diario.txt"

# Função que salva a anotação digitada pelo usuário
def salvar_anotacao():
    # Obtém o conteúdo do campo de texto a partir da posição 1.0 (linha 1, caractere 0) até o final
    texto = entrada_texto.get("1.0", tk.END).strip()  # strip() remove espaços em branco no início e fim
    
    # Verifica se o texto não está vazio
    if texto:
        # Pega a data e hora atual no formato dia/mês/ano horas:minutos:segundos
        data_hora = datetime.now().strftime("%d/%m/%Y %H:%M:%S")
        
        # Abre o arquivo em modo de adição ('a') e escreve a anotação
        # encoding='utf-8' garante que caracteres acentuados sejam salvos corretamente
        with open(ARQUIVO, "a", encoding="utf-8") as f:
            f.write(f"[{data_hora}] {texto}\n")  # Formato: [data e hora] conteúdo da anotação
        
        # Limpa o campo de entrada após salvar
        entrada_texto.delete("1.0", tk.END)
        
        # Exibe uma mensagem de sucesso para o usuário
        messagebox.showinfo("Salvo!", "Anotação salva com sucesso!")
    else:
        # Exibe um aviso se o campo estiver vazio
        messagebox.showwarning("Aviso", "A anotação está vazia!")

# Função para exibir as anotações que estão salvas no arquivo
def mostrar_anotacoes():
    # Verifica se o arquivo existe; se não existir, o diário está vazio
    if not os.path.exists(ARQUIVO):
        messagebox.showinfo("Diário", "O diário ainda está vazio.")
        return

    # Lê todo o conteúdo do arquivo
    with open(ARQUIVO, "r", encoding="utf-8") as f:
        conteudo = f.read()

    # Cria uma nova janela (janela filha) para mostrar as anotações
    janela_anotacoes = tk.Toplevel(root)  # Toplevel cria uma nova janela separada da principal
    janela_anotacoes.title("Anotações Salvas")
    janela_anotacoes.geometry("600x400")  # Define tamanho da janela

    # Campo de texto com rolagem para exibir o conteúdo do diário
    texto_anotacoes = scrolledtext.ScrolledText(
        janela_anotacoes, width=70, height=20, font=("Arial", 10))
    texto_anotacoes.pack(padx=10, pady=10)

    # Insere o conteúdo do arquivo no campo de texto
    texto_anotacoes.insert(tk.END, conteudo)
    
    # Torna o campo de texto apenas leitura (impede edições)
    texto_anotacoes.config(state=tk.DISABLED)

# Função chamada ao clicar no botão "Entrar no Diário"
def abrir_diario():
    # Fecha a janela de boas-vindas
    janela_boas_vindas.destroy()

    # Define que essas variáveis poderão ser usadas fora desta função
    global root, entrada_texto

    # Cria a janela principal do diário
    root = tk.Tk()
    root.title("📘 Diário Eletrônico")
    root.geometry("520x420")  # Define tamanho da janela

    # Rótulo que orienta o usuário
    tk.Label(root, text="Escreva sua anotação:", font=("Arial", 12, "bold")).pack(pady=10)

    # Campo de texto com barra de rolagem para entrada da anotação
    entrada_texto = scrolledtext.ScrolledText(root, width=60, height=10, font=("Arial", 10))
    entrada_texto.pack(pady=5)

    # Frame (container) para agrupar os botões
    frame_botoes = tk.Frame(root)
    frame_botoes.pack(pady=10)

    # Botão para salvar a anotação digitada
    tk.Button(frame_botoes, text="Salvar Anotação", command=salvar_anotacao,
              bg="lightgreen", font=("Arial", 10)).pack(side=tk.LEFT, padx=10)

    # Botão para visualizar as anotações já salvas
    tk.Button(frame_botoes, text="Minhas Anotações", command=mostrar_anotacoes,
              bg="lightblue", font=("Arial", 10)).pack(side=tk.LEFT, padx=10)

    # Inicia o loop principal da interface gráfica
    root.mainloop()

# Criação da janela inicial de boas-vindas
janela_boas_vindas = tk.Tk()
janela_boas_vindas.configure(bg="#bbd2f1") # Define a cor de fundo da tela de boas vindas
janela_boas_vindas.title("Bem-vindo")
janela_boas_vindas.geometry("400x250")  # Define tamanho da janela

# Mensagem de boas-vindas
tk.Label(janela_boas_vindas, text="Seja Bem-vindo ao seu Diário!",
         font=("Arial", 14, "bold")).pack(pady=40)

# Subtítulo
tk.Label(janela_boas_vindas, text="Iniciar anotações",
         font=("Arial", 14)).pack(pady=10)

# Botão para acessar a tela principal do diário
tk.Button(janela_boas_vindas, text="Entrar no Diário", font=("Arial", 12, "bold"),
          bg="lightblue", command=abrir_diario).pack(pady=20)

# Inicia o loop principal da interface gráfica
janela_boas_vindas.mainloop()
