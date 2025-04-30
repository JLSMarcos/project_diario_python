# project_diario

from datetime import datetime  # Importa a função para pegar data e hora
import os  # Importa o módulo para verificar se o arquivo existe

while True:  # Início do loop principal
    print("\n📓 MENU DO DIÁRIO")
    print("1 - Escrever nova anotação")
    print("2 - Ler anotações")
    print("3 - Sair")
    
    escolha = input("Escolha uma opção (1/2/3): ")  # Recebe a escolha do usuário

    if escolha == "1":
        texto = input("Digite sua anotação: ")  # Recebe anotação do usuário
        agora = datetime.now().strftime("%d/%m/%Y %H:%M:%S")  # Pega data e hora formatadas
        with open("diario.txt", "a", encoding="utf-8") as arquivo:  # Abre arquivo para adicionar
            arquivo.write(f"[{agora}] {texto}\n")  # Escreve data + anotação
        print("✅ Anotação salva com sucesso.")

    elif escolha == "2":
        if os.path.exists("diario.txt"):  # Verifica se o arquivo existe
            with open("diario.txt", "r", encoding="utf-8") as arquivo:  # Abre o arquivo para leitura
                conteudo = arquivo.read()  # Lê todo o conteúdo
                if conteudo.strip():  # Verifica se o conteúdo não está vazio
                    print("\n📝 Anotações:")
                    print(conteudo)
                else:
                    print("📭 Diário vazio.")
        else:
            print("🚫 Nenhuma anotação encontrada.")

    elif escolha == "3":
        print("👋 Saindo do diário.")
        break  # Encerra o programa

    else:
        print("❌ Opção inválida. Tente novamente.")

