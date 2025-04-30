#projeto diário

import tkinter as tk
from tkinter import messagebox, scrolledtext, simpledialog
from datetime import datetime
import os

ARQUIVO = "diario.txt"

# Função para salvar anotação
def salvar_anotacao():
    texto = entrada_texto.get("1.0", tk.END).strip()
    if texto:
        with open(ARQUIVO, "a", encoding="utf-8") as f:
            f.write(f"[{datetime.now().strftime('%d/%m/%Y %H:%M:%S')}] {texto}\n")
        entrada_texto.delete("1.0", tk.END)
        messagebox.showinfo("Salvo!", "Anotação salva com sucesso!")

# Função para mostrar anotações
def mostrar_anotacoes():
    if os.path.exists(ARQUIVO):
        with open(ARQUIVO, "r", encoding="utf-8") as f:
            conteudo = f.readlines()
        if conteudo:
            janela = tk.Toplevel(root)
            janela.title("Anotações")
            text_widget = scrolledtext.ScrolledText(janela, width=60, height=15)
            text_widget.pack(padx=10, pady=10)
            for i, linha in enumerate(conteudo, 1):
                text_widget.insert(tk.END, f"{i}. {linha.strip()}\n")
            text_widget.config(state=tk.DISABLED)
        else:
            messagebox.showinfo("Diário", "O diário está vazio.")
    else:
        messagebox.showinfo("Diário", "O diário está vazio.")

# Função para excluir uma anotação específica
def excluir_anotacao():
    if os.path.exists(ARQUIVO):
        with open(ARQUIVO, "r", encoding="utf-8") as f:
            linhas = f.readlines()
        
        if linhas:
            janela = tk.Toplevel(root)
            janela.title("Excluir Anotação")
            listbox = tk.Listbox(janela, width=60, height=10)
            for i, linha in enumerate(linhas, 1):
                listbox.insert(tk.END, f"{i}. {linha.strip()}")
            listbox.pack(padx=10, pady=10)
            
            def excluir_escolhida():
                try:
                    selecionada = listbox.curselection()[0]
                    linhas.pop(selecionada)
                    with open(ARQUIVO, "w", encoding="utf-8") as f:
                        f.writelines(linhas)
                    messagebox.showinfo("Excluído", "Anotação excluída com sucesso!")
                    janela.destroy()
                except IndexError:
                    messagebox.showwarning("Aviso", "Selecione uma anotação para excluir.")
            
            tk.Button(janela, text="Excluir", command=excluir_escolhida, bg="salmon").pack(pady=10)
        else:
            messagebox.showinfo("Diário", "O diário está vazio.")
    else:
        messagebox.showinfo("Diário", "O diário está vazio.")

# Função para editar uma anotação específica
def editar_anotacao():
    if os.path.exists(ARQUIVO):
        with open(ARQUIVO, "r", encoding="utf-8") as f:
            linhas = f.readlines()
        
        if linhas:
            janela = tk.Toplevel(root)
            janela.title("Editar Anotação")
            listbox = tk.Listbox(janela, width=60, height=10)
            for i, linha in enumerate(linhas, 1):
                listbox.insert(tk.END, f"{i}. {linha.strip()}")
            listbox.pack(padx=10, pady=10)

            # Função para editar a anotação selecionada
            def editar_escolhida():
                try:
                    selecionada = listbox.curselection()[0]
                    nova_anotacao = simpledialog.askstring("Editar Anotação", f"Editar: {linhas[selecionada]}\nDigite o novo texto:")
                    if nova_anotacao:
                        linhas[selecionada] = f"[{datetime.now().strftime('%d/%m/%Y %H:%M:%S')}] {nova_anotacao}\n"
                        with open(ARQUIVO, "w", encoding="utf-8") as f:
                            f.writelines(linhas)
                        messagebox.showinfo("Editado", "Anotação editada com sucesso!")
                        janela.destroy()
                    else:
                        messagebox.showwarning("Aviso", "O texto da anotação não foi alterado.")
                except IndexError:
                    messagebox.showwarning("Aviso", "Selecione uma anotação para editar.")
            
            tk.Button(janela, text="Editar", command=editar_escolhida, bg="lightyellow").pack(pady=10)
        else:
            messagebox.showinfo("Diário", "O diário está vazio.")
    else:
        messagebox.showinfo("Diário", "O diário está vazio.")

# Interface gráfica
root = tk.Tk()
root.title("📘 Diário Eletrônico")
root.geometry("500x400")

tk.Label(root, text="Escreva sua anotação:", font=("Arial", 12)).pack(pady=10)
entrada_texto = scrolledtext.ScrolledText(root, width=60, height=10)
entrada_texto.pack(pady=5)

frame_botoes = tk.Frame(root)
frame_botoes.pack(pady=10)

# Botões
tk.Button(frame_botoes, text="Salvar", command=salvar_anotacao, bg="lightgreen").pack(side=tk.LEFT, padx=10)
tk.Button(frame_botoes, text="Ver", command=mostrar_anotacoes, bg="lightblue").pack(side=tk.LEFT, padx=10)
tk.Button(frame_botoes, text="Excluir", command=excluir_anotacao, bg="salmon").pack(side=tk.LEFT, padx=10)
tk.Button(frame_botoes, text="Editar", command=editar_anotacao, bg="lightyellow").pack(side=tk.LEFT, padx=10)

root.mainloop()
