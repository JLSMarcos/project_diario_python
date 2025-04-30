# project_diario

import tkinter as tk
from tkinter import messagebox, scrolledtext, simpledialog
from datetime import datetime
import os

ARQUIVO = "diario.txt"  # Nome do arquivo onde as anotações serão salvas

# Função para salvar uma anotação
def salvar_anotacao():
    # Obtém o texto inserido pelo usuário no campo de texto
    texto = entrada_texto.get("1.0", tk.END).strip()
    
    # Se o texto não estiver vazio, salva no arquivo
    if texto:
        # Formata a data e hora atual
        data_hora = datetime.now().strftime("%d/%m/%Y %H:%M:%S")
        
        try:
            # Abre o arquivo em modo de adição e salva a anotação
            with open(ARQUIVO, "a", encoding="utf-8") as f:
                f.write(f"[{data_hora}] {texto}\n")
            
            # Limpa o campo de texto após salvar
            entrada_texto.delete("1.0", tk.END)
            
            # Exibe uma mensagem informando que a anotação foi salva com sucesso
            messagebox.showinfo("Salvo!", "Anotação salva com sucesso!")
        except Exception as e:
            # Caso ocorra um erro ao salvar, exibe uma mensagem de erro
            messagebox.showerror("Erro", f"Ocorreu um erro ao salvar a anotação: {e}")
    else:
        # Se o campo de texto estiver vazio, exibe um aviso
        messagebox.showwarning("Aviso", "A anotação está vazia!")

# Função para mostrar todas as anotações salvas
def mostrar_anotacoes():
    # Verifica se o arquivo existe
    if not os.path.exists(ARQUIVO):
        messagebox.showinfo("Diário", "O diário ainda está vazio.")
        return

    # Lê o conteúdo do arquivo e exibe em uma nova janela
    with open(ARQUIVO, "r", encoding="utf-8") as f:
        conteudo = f.read()

    # Cria uma nova janela para mostrar as anotações
    janela_anotacoes = tk.Toplevel(root)
    janela_anotacoes.title("Anotações salvas")
    
    # Adiciona um campo de texto rolável para exibir as anotações
    texto_anotacoes = scrolledtext.ScrolledText(janela_anotacoes, width=60, height=20)
    texto_anotacoes.pack(padx=10, pady=10)
    
    # Insere o conteúdo do arquivo no campo de texto e desativa a edição
    texto_anotacoes.insert(tk.END, conteudo)
    texto_anotacoes.config(state=tk.DISABLED)

# Função para excluir a última anotação
def excluir_anotacao():
    # Verifica se o arquivo existe
    if not os.path.exists(ARQUIVO):
        messagebox.showinfo("Diário", "O diário está vazio.")
        return

    try:
        # Abre o arquivo e lê todas as linhas (anotações)
        with open(ARQUIVO, "r", encoding="utf-8") as f:
            linhas = f.readlines()

        # Se o arquivo não estiver vazio, remove a última anotação
        if linhas:
            linhas.pop()  # Remove a última linha (última anotação)
            
            # Sobrescreve o arquivo com as anotações restantes
            with open(ARQUIVO, "w", encoding="utf-8") as f:
                f.writelines(linhas)
            
            # Exibe uma mensagem informando que a anotação foi excluída
            messagebox.showinfo("Excluído", "Última anotação excluída com sucesso!")
        else:
            # Se o arquivo estiver vazio, exibe uma mensagem
            messagebox.showinfo("Diário", "O diário está vazio.")
    except Exception as e:
        # Caso ocorra um erro ao excluir, exibe uma mensagem de erro
        messagebox.showerror("Erro", f"Ocorreu um erro ao excluir a anotação: {e}")

# Função para editar a última anotação
def editar_anotacao():
    # Verifica se o arquivo existe
    if not os.path.exists(ARQUIVO):
        messagebox.showinfo("Diário", "O diário está vazio.")
        return

    try:
        # Abre o arquivo e lê todas as linhas (anotações)
        with open(ARQUIVO, "r", encoding="utf-8") as f:
            linhas = f.readlines()

        # Se houver anotações no arquivo
        if linhas:
            ultima_anotacao = linhas[-1]  # Pega a última anotação
            # Exibe a última anotação e solicita ao usuário um novo texto
            nova_anotacao = simpledialog.askstring("Editar Anotação", f"Última anotação:\n{ultima_anotacao}\nDigite o novo texto:")
            
            # Se o usuário não deixou o campo vazio, substitui a última anotação
            if nova_anotacao:
                linhas[-1] = f"[{datetime.now().strftime('%d/%m/%Y %H:%M:%S')}] {nova_anotacao}\n"
                # Sobrescreve o arquivo com a anotação editada
                with open(ARQUIVO, "w", encoding="utf-8") as f:
                    f.writelines(linhas)
                # Exibe uma mensagem informando que a anotação foi editada com sucesso
                messagebox.showinfo("Editado", "Última anotação editada com sucesso!")
            else:
                messagebox.showwarning("Aviso", "A anotação não foi alterada.")
        else:
            # Se o arquivo estiver vazio, exibe uma mensagem
            messagebox.showinfo("Diário", "O diário está vazio.")
    except Exception as e:
        # Caso ocorra um erro ao editar, exibe uma mensagem de erro
        messagebox.showerror("Erro", f"Ocorreu um erro ao editar a anotação: {e}")


# Interface principal do programa
root = tk.Tk()
root.title("📘 Diário Eletrônico")  # Título da janela
root.geometry("500x400")  # Tamanho da janela

# Label com o texto "Escreva sua anotação:"
tk.Label(root, text="Escreva sua anotação:", font=("Arial", 12)).pack(pady=10)

# Campo de texto onde o usuário pode escrever a anotação
entrada_texto = scrolledtext.ScrolledText(root, width=60, height=10)
entrada_texto.pack(pady=5)

# Frame para organizar os botões
frame_botoes = tk.Frame(root)
frame_botoes.pack(pady=10)

# Botões para interagir com o diário
tk.Button(frame_botoes, text="Salvar Anotação", command=salvar_anotacao, bg="lightgreen").pack(side=tk.LEFT, padx=10)
tk.Button(frame_botoes, text="Ver Anotações", command=mostrar_anotacoes, bg="lightblue").pack(side=tk.LEFT, padx=10)
tk.Button(frame_botoes, text="Excluir Última Anotação", command=excluir_anotacao, bg="salmon").pack(side=tk.LEFT, padx=10)
tk.Button(frame_botoes, text="Editar Última Anotação", command=editar_anotacao, bg="lightyellow").pack(side=tk.LEFT, padx=10)

# Roda o programa
root.mainloop()
