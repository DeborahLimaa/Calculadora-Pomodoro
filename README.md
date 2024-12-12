import tkinter as tk
from tkinter import messagebox, colorchooser
import time
from datetime import datetime
import pytz
import requests

class App:
    def __init__(self, root):
        self.root = root
        self.root.title("Relógio com Calculadora e Pomodoro Mundial")
        self.api_key = "74469a237a81b07416e1ed2ab00cf0ec"

    # Cor inicial do texto e do fundo
        self.cor_texto = "white"
        self.cor_fundo = "black"

    # Criar frames para o Relógio, calculadora e o Pomodoro
        self.frame_relogio = tk.Frame(root, bg=self.cor_fundo)
        self.frame_calculadora = tk.Frame(root)
        self.frame_tarefas = tk.Frame(root)
        self.frame_cronometro = tk.Frame(root)

    # --- RELÓGIO ---
        self.label_tempo = tk.Label(self.frame_relogio, text="", font=("Helvetica", 48), fg=self.cor_texto, bg=self.cor_fundo)
        self.label_tempo.pack(pady=20)
        self.label_data = tk.Label(self.frame_relogio, text="", font=("Helvetica", 24), fg=self.cor_texto, bg=self.cor_fundo)
        self.label_data.pack(pady=10)
        # Dropdown para selecionar as cidades
        self.cidades = ["UTC", "America/New_York", "Europe/London", "Asia/Tokyo", "Australia/Sydney"]
        self.cidade_selecionada = tk.StringVar()
        self.cidade_selecionada.set("UTC") #Valor inicial
        self.dropdown_cidade = tk.OptionMenu(self.frame_relogio, self.cidade_selecionada, *self.cidades, command=self.atualizar_horario_mundial)
        self.dropdown_cidade.pack(pady=10)
        # Label para exibir o horário da cidade selecionada
        self.label_horario_mundial = tk.Label(self.frame_relogio, text="", font=("Helvetica", 24), fg=self.cor_texto, bg=self.cor_fundo)
        self.label_horario_mundial.pack(pady=10)
        # Label para exibir informações meteoroçógicas
        self.label_clima = tk.Label(self.frame_relogio, text="", font=("Helvetica", 24), fg=self.cor_texto, bg=self.cor_fundo)
        self.label_clima.pack(pady=10)

    # Botões de mudança de cor para o relógio
        self.button_mudar_cor_texto = tk.Button(self.frame_relogio, text="Mudar cor do texto", command=self.mudar_cor_texto)
        self.button_mudar_cor_texto.pack(pady=5)
        self.button_mudar_cor_fundo = tk.Button(self.frame_relogio, text="Mudar cor do fundo", command=self.mudar_cor_fundo)
        self.button_mudar_cor_fundo.pack(pady=5)

    # --- CRONOMETRO ---
        self.tempo = 0
        self.cronometro_rodando = False
        self.label_cronometro = tk.Label(self.frame_cronometro, text="00:00:00", font=("Helvetica", 48))
        self.label_cronometro.pack(pady=10)
        self.btn_iniciar = tk.Button(self.frame_cronometro, text="Iniciar", command=self.iniciar_cronometro)
        self.btn_iniciar.pack(side=tk.LEFT, padx=5)
        self.btn_pausar = tk.Button(self.frame_cronometro, text="Pausar", command=self.pausar_cronometro)
        self.btn_pausar.pack(side=tk.LEFT, padx=5)
        self.btn_resetar = tk.Button(self.frame_cronometro, text="Resetar", command=self.resetar_cronometro)
        self.btn_resetar.pack(side=tk.LEFT, padx=5)

    # --- TAREFAS ---
        self.label_tarefas = tk.Label(self.frame_tarefas, text="Lista de tarefas", font=("Helvetica", 24))
        self.label_tarefas.pack(pady=10)
        self.entry_tarefa = tk.Entry(self.frame_tarefas, width=40)
        self.entry_tarefa.pack(pady=5)
        self.btn_adicionar_tarefa = tk.Button(self.frame_tarefas, text="Adicionar Tarefa", command=self.adicionar_tarefa)
        self.btn_adicionar_tarefa.pack(pady=5)
        self.lista_tarefas = tk.Listbox(self.frame_tarefas, selectmode=tk.SINGLE, width=50, height=10)
        self.lista_tarefas.pack(pady=5)
        self.btn_remover_tarefa = tk.Button(self.frame_tarefas, text="Remover tarefa", command=self.remover_tarefa)
        self.btn_remover_tarefa.pack(pady=5)

    # --- CALCULADORA ---
        self.entry_calculadora = tk.Entry(self.frame_calculadora, width=20, borderwidth=5, font=("Helvetica", 18))
        self.entry_calculadora.grid(row=0, column=0, columnspan=4, pady=10)

        botoes = [
            '7', '8', '9', '/',
            '4', '5', '6', '*',
            '1', '2', '3', '-',
            '0', '.', '=', '+',
        ]

        # Posicionar botões da calculadora no grid
        linha = 1
        coluna = 0
        for botao in botoes:
            tk.Button(self.frame_calculadora, text=botao, width=5, height=2, font=("Helvetica", 18), command=lambda b=botao: self.click_botao(b)).grid(row=linha, column=coluna)
            coluna += 1
            if coluna == 4:
                coluna = 0
                linha += 1
                
        # Adiciona o botão de apagar no grid
        tk.Button(self.frame_calculadora, text="⌫", width=5, height=2, font=("Helvetica", 18), command=self.backspace).grid(row=5, column=0)      

    # Botões para alternar entre as funcionalidades
        self.btn_mostrar_relogio = tk.Button(root, text="Relogio", command=self.mostrar_relogio)
        self.btn_mostrar_relogio.pack(side=tk.LEFT, padx=10)
        self.btn_mostrar_cronometro = tk.Button(root, text="Cronometro", command=self.mostrar_cronometro)
        self.btn_mostrar_cronometro.pack(side=tk.LEFT, padx=10)
        self.btn_mostrar_tarefas = tk.Button(root, text="Tarefas", command=self.mostrar_tarefas)
        self.btn_mostrar_tarefas.pack(side=tk.LEFT, padx=10)
        self.btn_mostrar_calculadora = tk.Button(root, text="Calculadora", command=self.mostrar_calculadora)
        self.btn_mostrar_calculadora.pack(side=tk.LEFT, padx=10)

    # Inicialmente, mostrar o relógio
        self.mostrar_relogio()

    # Atualizar o relógio e a data
        self.atualizar_tempo()
        self.exibir_data()

    def atualizar_horario_mundial(self, *args):
        cidade = self.cidade_selecionada.get()
        timezone = pytz.timezone(cidade)
        horario_atual = datetime.now(timezone).strftime("%H:%M:%S")
        self.label_horario_mundial.config(text=f"Horário em {cidade}: {horario_atual}")
        self.atualizar_clima(cidade) # Chama a função de clima
        self.root.after(1000, self.atualizar_horario_mundial)

    def atualizar_clima(self, cidade):
        try:
            # Mapeamento de cidades do dropdown para o formato aceito da API
            cidade_mapeada = {"UTC": "São Paulo", #UTC NÃO É CIDADE, ENTÃO MAPEAMOS PARA SÃO PAULO COMO EXEMPLO
                "America/New_York": "New York",
                "Europe/London": "London",
                "Asia/Tokyo": "Tokyo",
                "Australia/Sydney": "Sydney"
            }
            # Obtém o nome da cidade mapeada
            nome_cidade = cidade_mapeada.get(cidade, "London") # Padrão para Londres se não houver mapeamento
            # URL da API com os parâmetros necessários
            url = f"https://api.openweathermap.org/data/2.5/weather?q={nome_cidade}&appid={self.api_key}&units=metric&lang=pt"
            
            # Faz a requisição para a API com verificação SSL desativada
            response = requests.get(url, verify=False)  # SSL desativado
            dados = response.json()

            # Processa os dados recebidos
            if response.status_code == 200:
                temperatura = dados['main']['temp']
                descricao = dados['weather'][0]['description']
                self.label_clima.config(text=f"Clima em {nome_cidade}: {descricao}, {temperatura}°C")
            else:
                mensagem_erro = dados.get("message", "Erro desconhecido")
                self.label_clima.config(text=f"Erro: {mensagem_erro}")
        except Exception as e:
            print(f"Erro ao obter dados meteorológicos: {e}")
            self.label_clima.config(text="Erro ao obter dados meteorológicos")

    # --- Relógio ---
    def mostrar_relogio(self):
        self.frame_cronometro.pack_forget()
        self.frame_tarefas.pack_forget()
        self.frame_calculadora.pack_forget()
        self.frame_relogio.pack()

    def atualizar_tempo(self):
        hora_atual = time.strftime("%H:%M:%S")
        self.label_tempo.config(text=hora_atual)
        self.root.after(1000, self.atualizar_tempo)
        
    def exibir_data(self):
        data_atual = datetime.now().strftime("%d/%m/%Y")
        self.label_data.config(text=data_atual)

    def mudar_cor_texto(self):
        cor = colorchooser.askcolor(title="Escolha a cor do texto")[1]
        if cor:
            self.cor_texto = cor
            self.label_tempo.config(fg=self.cor_texto)
            self.label_data.config(fg=self.cor_texto)
        
    def mudar_cor_fundo(self):
        cor = colorchooser.askcolor(title="Escolha a cor do fundo")[1]
        if cor:
            self.cor_fundo = cor
            self.label_tempo.config(bg=self.cor_fundo)
            self.label_data.config(bg=self.cor_fundo)
            self.frame_relogio.config(bg=self.cor_fundo)
            # --- Cronometro ---
    def iniciar_cronometro(self):
        if not self.cronometro_rodando:
            self.cronometro_rodando = True
            self.atualizar_cronometro()
        
    def pausar_cronometro(self):
                self.cronometro_rodando = False
            
    def resetar_cronometro(self):
        self.tempo = 0
        self.cronometro_rodando = False
        self.label_cronometro.config(text="00:00:00")
            
    def atualizar_cronometro(self):
        if self.cronometro_rodando:
            self.tempo += 1
            horas, resto = divmod(self.tempo, 3600)
            minutos, segundos = divmod(resto, 60)

            self.label_cronometro.config(text=f" {horas:02}:{minutos:02}:{segundos:02}")
            self.root.after(1000, self.atualizar_cronometro)

    def mostrar_cronometro(self):
        self.frame_relogio.pack_forget()
        self.frame_tarefas.pack_forget()
        self.frame_calculadora.pack_forget()
        self.frame_cronometro.pack()

    # --- Tarefas ---
    def mostrar_tarefas(self):
        self.frame_relogio.pack_forget()
        self.frame_cronometro.pack_forget()
        self.frame_calculadora.pack_forget()
        self.frame_tarefas.pack()

    def adicionar_tarefa(self):
        tarefa = self.entry_tarefa.get()
        if tarefa != "":
            self.lista_tarefas.insert(tk.END, tarefa)
            self.entry_tarefa.delete(0, tk.END)
            self.iniciar_cronometro()
        else:
            messagebox.showwarning("Entrada vazia", "A tarefa não pode estar vazia.")

    def remover_tarefa(self):
        try:
            index = self.lista_tarefas.curselection()[0]
            self.lista_tarefas.delete(index)
            self.resetar_cronometro()
            
        except IndexError:
            messagebox.showwarning("Seleção inválida", "Por favor, selecione uma tarefa para remover.")

    # --- Calculadora ---
    def mostrar_calculadora(self):
        self.frame_relogio.pack_forget()
        self.frame_cronometro.pack_forget()
        self.frame_tarefas.pack_forget()
        self.frame_calculadora.pack()

    def click_botao(self, botao):
        if botao == "=":
            try:
                resultado = str(eval(self.entry_calculadora.get()))
                self.entry_calculadora.delete(0, tk.END)
                self.entry_calculadora.insert(tk.END, resultado)
            
            except:
                self.entry_calculadora.delete(0, tk.END)
                self.entry_calculadora.insert(tk.END, "Erro")

        else:
                self.entry_calculadora.insert(tk.END, botao)
                
    def backspace(self):
        texto_atual = self.entry_calculadora.get()
        self.entry_calculadora.delete(0, tk.END)
        self.entry_calculadora.insert(tk.END, texto_atual[:-1])  

# Criar a interface
if __name__ == "__main__":
    root = tk.Tk()
    app = App(root)
    root.mainloop()
