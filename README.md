# project_diario

import tkinter as tk
from tkinter import messagebox, scrolledtext
from datetime import datetime
import os

ARQUIVO = "diario.txt"

def salvar_anotacao_texto(texto):
    data_hora = datetime.now().strftime("%d/%m/%Y %H:%M:%S")
    with open(ARQUIVO, "a", encoding="utf-8") as f:
        f.write(f"[{data_hora}] {texto}\n")

def ler_anotacoes():
    if not os.path.exists(ARQUIVO):
        return ""
    with open(ARQUIVO, "r", encoding="utf-8") as f:
        return f.read()


# === MODO TERMINAL ===
def modo_terminal():
    while True:
        escolha = input("Escolha uma opção (1 - Anotar / 2 - Ver / 3 - Sair): ")

        if escolha == "1":
            texto = input("Digite sua anotação: ")
            salvar_anotacao_texto(texto)
            print("✅ Anotação salva.")
        elif escolha == "2":
            conteudo = ler_anotacoes()
            if conteudo.strip():
                print("\n📝 Anotações:")
                print(conteudo)
            else:
                print("📭 Diário vazio.")
        elif escolha == "3":
            print("👋 Saindo do diário.")
            break
        else:
            print("❌ Opção inválida.")


# === MODO TKINTER ===
def modo_tkinter():
    def salvar_anotacao_gui():
        texto = entrada_texto.get("1.0", tk.END).strip()
        if texto:
            salvar_anotacao_texto(texto)
            entrada_texto.delete("1.0", tk.END)
            messagebox.showinfo("Salvo!", "Anotação salva com sucesso!")
        else:
            messagebox.showwarning("Aviso", "A anotação está vazia!")

    def mostrar_anotacoes_gui():
        conteudo = ler_anotacoes()
        if not conteudo.strip():
            messagebox.showinfo("Diário", "O diário ainda está vazio.")
            return

        janela_anotacoes = tk.Toplevel(root)
        janela_anotacoes.title("Anotações salvas")
        texto_anotacoes = scrolledtext.ScrolledText(janela_anotacoes, width=60, height=20)
        texto_anotacoes.pack(padx=10, pady=10)
        texto_anotacoes.insert(tk.END, conteudo)
        texto_anotacoes.config(state=tk.DISABLED)

    # Interface principal
    global root, entrada_texto
    root = tk.Tk()
    root.title("📘 Diário Eletrônico")
    root.geometry("500x400")

    tk.Label(root, text="Escreva sua anotação:", font=("Arial", 12)).pack(pady=10)

    entrada_texto = scrolledtext.ScrolledText(root, width=60, height=10)
    entrada_texto.pack(pady=5)

    frame_botoes = tk.Frame(root)
    frame_botoes.pack(pady=10)

    tk.Button(frame_botoes, text="Salvar Anotação", command=salvar_anotacao_gui,
              bg="lightgreen").pack(side=tk.LEFT, padx=10)
    tk.Button(frame_botoes, text="Ver Anotações", command=mostrar_anotacoes_gui,
              bg="lightblue").pack(side=tk.LEFT, padx=10)

    root.mainloop()


# === ESCOLHA DO USUÁRIO AO INICIAR ===
modo = input("Escolha o modo: 1 - Terminal | 2 - Gráfico (GUI): ")
if modo == "1":
    modo_terminal()
elif modo == "2":
    modo_tkinter()
else:
    print("❌ Modo inválido.")
