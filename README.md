# PROJETO DIÁRIO

# Importação dos módulos necessários
import tkinter as tk  # Módulo principal para criar interfaces gráficas em Python
from tkinter import messagebox, scrolledtext  # Widgets adicionais do tkinter: caixas de mensagem e campo de texto com rolagem
from datetime import datetime  # Usado para obter data e hora atual
import sqlite3  # Usado para manipular banco de dados SQLite

# Função responsável por criar o banco de dados e a tabela, se ainda não existirem
def inicializar_banco():
    # Conecta ao banco de dados (ou cria, se não existir)
    conn = sqlite3.connect("diario.db")
    cursor = conn.cursor()
    
    # Cria a tabela 'anotacoes' com os campos id, data_hora e texto
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS anotacoes (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            data_hora TEXT NOT NULL,
            texto TEXT NOT NULL
        )
    ''')
    
    # Salva as alterações e fecha a conexão
    conn.commit()
    conn.close()

# Função que salva a anotação digitada pelo usuário
def salvar_anotacao():
    # Obtém o conteúdo do campo de texto a partir da posição 1.0 até o final
    texto = entrada_texto.get("1.0", tk.END).strip()  # strip() remove espaços em branco no início e fim
    
    # Verifica se o texto não está vazio
    if texto:
        # Obtém a data e hora atual no formato dd/mm/aaaa hh:mm:ss
        data_hora = datetime.now().strftime("%d/%m/%Y %H:%M:%S")
        
        # Abre conexão com o banco de dados e insere a nova anotação
        conn = sqlite3.connect("diario.db")
        cursor = conn.cursor()
        cursor.execute("INSERT INTO anotacoes (data_hora, texto) VALUES (?, ?)", (data_hora, texto))
        conn.commit()
        conn.close()
        
        # Limpa o campo de entrada após salvar
        entrada_texto.delete("1.0", tk.END)
        
        # Exibe uma mensagem de sucesso para o usuário
        messagebox.showinfo("Salvo!", "Anotação salva com sucesso!")
    else:
        # Exibe um aviso se o campo estiver vazio
        messagebox.showwarning("Aviso", "A anotação está vazia!")

# Função para exibir as anotações salvas no banco de dados
def mostrar_anotacoes():
    # Conecta ao banco de dados
    conn = sqlite3.connect("diario.db")
    cursor = conn.cursor()
    
    # Recupera todas as anotações, ordenadas da mais recente para a mais antiga
    cursor.execute("SELECT data_hora, texto FROM anotacoes ORDER BY id DESC")
    dados = cursor.fetchall()
    conn.close()
    
    # Cria uma nova janela para mostrar as anotações
    janela_anotacoes = tk.Toplevel(root)  # Toplevel cria uma nova janela separada da principal
    janela_anotacoes.title("Anotações Salvas")
    janela_anotacoes.geometry("600x400")  # Define o tamanho da janela
    
    # Campo de texto com rolagem para exibir o conteúdo do diário
    texto_anotacoes = scrolledtext.ScrolledText(
        janela_anotacoes, width=70, height=20, font=("Arial", 10))
    texto_anotacoes.pack(padx=10, pady=10)
    
    # Insere o conteúdo do banco de dados no campo de texto
    for data, texto in dados:
        texto_anotacoes.insert(tk.END, f"[{data}] {texto}\n\n")
    
    # Torna o campo de texto somente leitura
    texto_anotacoes.config(state=tk.DISABLED)

# Função chamada ao clicar no botão "Entrar no Diário"
def abrir_diario():
    # Fecha a janela de boas-vindas
    janela_boas_vindas.destroy()
    
    # Declara variáveis globais que serão usadas em outras funções
    global root, entrada_texto
    
    # Cria a janela principal do diário
    root = tk.Tk()
    root.title("📘 Diário Eletrônico")
    root.geometry("520x420")  # Define o tamanho da janela
    
    # Rótulo que orienta o usuário
    tk.Label(root, text="Escreva sua anotação:", font=("Arial", 12, "bold")).pack(pady=10)
    
    # Campo de texto com barra de rolagem para entrada da anotação
    entrada_texto = scrolledtext.ScrolledText(root, width=60, height=10, font=("Arial", 10))
    entrada_texto.pack(pady=5)
    
    # Frame para agrupar os botões
    frame_botoes = tk.Frame(root)
    frame_botoes.pack(pady=10)
    
    # Botão para salvar a anotação
    tk.Button(frame_botoes, text="Salvar Anotação", command=salvar_anotacao,
              bg="lightgreen", font=("Arial", 10)).pack(side=tk.LEFT, padx=10)
    
    # Botão para visualizar as anotações
    tk.Button(frame_botoes, text="Minhas Anotações", command=mostrar_anotacoes,
              bg="lightblue", font=("Arial", 10)).pack(side=tk.LEFT, padx=10)
    
    # Inicia o loop principal da interface gráfica
    root.mainloop()

# === Início da aplicação ===

# Inicializa o banco de dados (cria a tabela, se necessário)
inicializar_banco()

# Criação da janela de boas-vindas
janela_boas_vindas = tk.Tk()
janela_boas_vindas.configure(bg="#c7e3eb")  # Define a cor de fundo da tela
janela_boas_vindas.title("Bem-vindo")
janela_boas_vindas.geometry("400x250")  # Define o tamanho da janela

# Mensagem de boas-vindas
tk.Label(janela_boas_vindas, text="Seja Bem-vindo ao seu Diário!",
         font=("Arial", 14, "bold"), bg="#a9dceb").pack(pady=40)

# Subtítulo
tk.Label(janela_boas_vindas, text="Iniciar anotações",
         font=("Arial", 14), bg="#a9dceb").pack(pady=10)

# Botão para entrar no diário
tk.Button(janela_boas_vindas, text="Entrar no Diário", font=("Arial", 12, "bold"),
          bg="lightblue", command=abrir_diario).pack(pady=20)

# Inicia o loop principal da interface gráfica
janela_boas_vindas.mainloop()
