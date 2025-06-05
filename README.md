#Projeto Diário
#José Marcos de Sousa de Lima

import tkinter as tk
from tkinter import messagebox, scrolledtext
from datetime import datetime
import os

# Nome do arquivo onde as anotações serão salvas
ARQUIVO = "diario.txt"


# Função para salvar uma nova anotação
def salvar_anotacao():
    texto = entrada_texto.get("1.0", tk.END).strip()
    if texto:
        data_hora = datetime.now().strftime("%d/%m/%Y %H:%M:%S")
        with open(ARQUIVO, "a", encoding="utf-8") as f:
            f.write(f"[{data_hora}] {texto}\n")
        entrada_texto.delete("1.0", tk.END)
        messagebox.showinfo("Salvo!", "Anotação salva com sucesso!")
    else:
        messagebox.showwarning("Aviso", "A anotação está vazia!")


# Função para mostrar todas as anotações salvas
def mostrar_anotacoes():
    if not os.path.exists(ARQUIVO):
        messagebox.showinfo("Diário", "O diário ainda está vazio.")
        return

    with open(ARQUIVO, "r", encoding="utf-8") as f:
        conteudo = f.read()

    # Criação da nova janela para exibir anotações
    janela_anotacoes = tk.Toplevel(root)
    janela_anotacoes.title("Anotações Salvas")
    janela_anotacoes.geometry("600x400")

    texto_anotacoes = scrolledtext.ScrolledText(
        janela_anotacoes, width=70, height=20, font=("Arial", 10))
    texto_anotacoes.pack(padx=10, pady=10)
    texto_anotacoes.insert(tk.END, conteudo)
    texto_anotacoes.config(state=tk.DISABLED)


# Janela principal da aplicação
root = tk.Tk()
root.title("📘 Diário Eletrônico")
root.geometry("520x420")

# Título
tk.Label(root, text="Escreva sua anotação:", font=("Arial", 12)).pack(pady=10)

# Campo de texto para escrever a anotação
entrada_texto = scrolledtext.ScrolledText(root, width=60, height=10, font=("Arial", 10))
entrada_texto.pack(pady=5)

# Frame para os botões
frame_botoes = tk.Frame(root)
frame_botoes.pack(pady=10)

# Botão de salvar anotação
tk.Button(frame_botoes, text="Salvar Anotação", command=salvar_anotacao,
          bg="lightgreen", font=("Arial", 10)).pack(side=tk.LEFT, padx=10)

# Botão de ver anotações
tk.Button(frame_botoes, text="Minhas Anotações", command=mostrar_anotacoes,
          bg="lightblue", font=("Arial", 10)).pack(side=tk.LEFT, padx=10)

# Rodar o programa
root.mainloop()
