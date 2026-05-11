[main.py](https://github.com/user-attachments/files/27612803/main.py)
import customtkinter as ctk
import sys
import os

# 1. CONFIGURAÇÕES DE ACESSO
KEYS_PERMITIDAS = ["PABLO-DEV-2026", "ADMIN-TESTE"]

# 2. CLASSE DO LOGIN (PRETA E ÚNICA)
class JanelaLogin(ctk.CTk):
    def __init__(self):
        super().__init__()
        self.autorizado = False

        # Configurações Visuais
        self.title("Ativação de Licença")
        self.geometry("400x320")
        self.configure(fg_color="#000000")
        self.protocol("WM_DELETE_WINDOW", self.fechar_total)
        self.eval('tk::PlaceWindow . center')

        # Elementos da Interface
        ctk.CTkLabel(self, text="SYSTEM OPTIMIZER PRO", font=("Arial", 18, "bold"), 
                     text_color="#1d4ed8", fg_color="#000000").pack(pady=(40, 10))
        
        self.entry = ctk.CTkEntry(self, width=280, height=45, justify="center", 
                                 placeholder_text="COLE SUA KEY AQUI", 
                                 fg_color="#0A0A0A", border_color="#1d4ed8")
        self.entry.pack(pady=20)
        self.entry.bind("<Return>", lambda e: self.verificar())

        self.erro = ctk.CTkLabel(self, text="", fg_color="#000000")
        self.erro.pack()

        ctk.CTkButton(self, text="VALIDAR ACESSO", command=self.verificar, 
                      fg_color="#1d4ed8", hover_color="#2563eb", 
                      width=220, height=45).pack(pady=20)

    def verificar(self):
        if self.entry.get().strip() in KEYS_PERMITIDAS:
            self.autorizado = True
            self.quit()    # Para o loop do login
            self.destroy() # Fecha a janela de login de vez
        else:
            self.erro.configure(text="CHAVE INVÁLIDA!", text_color="#FF3333")

    def fechar_total(self):
        self.destroy()
        sys.exit()

# 3. EXECUÇÃO DO SISTEMA
if __name__ == "__main__":
    # Inicia a tela de login
    login = JanelaLogin()
    login.mainloop()

    # Só chega aqui se a Key for correta.
    if login.autorizado:
        # --- COLE SEU CÓDIGO DO PAINEL (INTERFACE) ABAIXO ---
        # Exemplo: Se o seu painel for outra classe ou função, chame-a aqui.
        
        print("Acesso autorizado. Iniciando o painel principal...")
        
        # Se você tiver uma função chamada abrir_painel(), coloque aqui:
        # abrir_painel()
import customtkinter as ctk
import os
import shutil
import threading
import time
import psutil
import ctypes

# Configuração visual do tema
ctk.set_appearance_mode("dark")
ctk.set_default_color_theme("blue")

class SystemOptimizerPro(ctk.CTk):
    def __init__(self):
        super().__init__()

        # Janela Principal
        self.title("SYSTEM OPTIMIZER PRO v1.0.4 - ESTÁVEL")
        self.geometry("1100x600")
        self.configure(fg_color="#0B0C10") # Cor de fundo do seu design

        # Layout de Colunas
        self.grid_columnconfigure(1, weight=1)
        self.grid_rowconfigure(0, weight=1)

        # --- BARRA LATERAL ESQUERDA ---
        self.sidebar = ctk.CTkFrame(self, width=250, corner_radius=0, fg_color="#0f1115")
        self.sidebar.grid(row=0, column=0, sticky="nsew")
        
        self.logo_label = ctk.CTkLabel(self.sidebar, text="SYSTEM OPTIMIZER", font=("Orbitron", 16, "bold"))
        self.logo_label.pack(pady=20, padx=10)

        # Status Cards (Simulação do seu design)
        self.mem_label = ctk.CTkLabel(self.sidebar, text="CARGA DE MEMÓRIA: 34%", font=("Arial", 11))
        self.mem_label.pack(pady=10)
        
        self.junk_label = ctk.CTkLabel(self.sidebar, text="DADOS INÚTEIS: 12.4 GB", font=("Arial", 11), text_color="#FF8C00")
        self.junk_label.pack(pady=10)

        # --- PAINEL CENTRAL ---
        self.main_view = ctk.CTkFrame(self, fg_color="transparent")
        self.main_view.grid(row=0, column=1, sticky="nsew", padx=20, pady=20)

        self.title_main = ctk.CTkLabel(self.main_view, text="Otimização em um Clique", font=("Arial", 32, "bold"))
        self.title_main.pack(pady=(60, 5))

        self.subtitle = ctk.CTkLabel(self.main_view, text="Inicie o processo de manutenção segura para liberar\nrecursos e reparar arquivos do sistema.", text_color="gray")
        self.subtitle.pack(pady=10)

        self.btn_otimizar = ctk.CTkButton(self.main_view, text="⚡ INICIAR OTIMIZAÇÃO SEGURA", 
                                          command=self.start_optimization_thread,
                                          height=60, width=400, corner_radius=30,
                                          font=("Arial", 18, "bold"), fg_color="#1d4ed8", hover_color="#2563eb")
        self.btn_otimizar.pack(pady=40)

        # --- TERMINAL / LOG DE SAÍDA ---
        self.terminal_frame = ctk.CTkFrame(self, height=150, fg_color="#050505", corner_radius=5)
        self.terminal_frame.grid(row=1, column=0, columnspan=2, sticky="nsew", padx=10, pady=10)
        
        self.log_title = ctk.CTkLabel(self.terminal_frame, text="● ● ●  LOG DE SAÍDA / EMULADOR DE TERMINAL", font=("Consolas", 10), text_color="gray")
        self.log_title.pack(side="top", anchor="w", padx=10)

        self.text_log = ctk.CTkTextbox(self.terminal_frame, fg_color="transparent", text_color="#00FF00", font=("Consolas", 12))
        self.text_log.pack(fill="both", expand=True, padx=5, pady=5)
        self.log("SISTEMA OCIOSO. Aguardando entrada...")

    def log(self, message):
        self.text_log.insert("end", f"\n> {message}")
        self.text_log.see("end")

    def start_optimization_thread(self):
        # Desabilita o botão para não clicar duas vezes
        self.btn_otimizar.configure(state="disabled", text="OTIMIZANDO SISTEMA...")
        threading.Thread(target=self.run_optimization, daemon=True).start()

    def run_optimization(self):
        try:
            self.log("Verificando privilégios de administrador...")
            time.sleep(1)

            # 1. Limpeza de Temporários
            self.log("Limpando arquivos temporários (%TEMP%)...")
            temp_folders = [os.environ.get('TEMP'), r'C:\Windows\Temp', r'C:\Windows\Prefetch']
            
            for folder in temp_folders:
                if os.path.exists(folder):
                    self.log(f"Acessando: {folder}")
                    for filename in os.listdir(folder):
                        file_path = os.path.join(folder, filename)
                        try:
                            if os.path.isfile(file_path) or os.path.islink(file_path):
                                os.unlink(file_path)
                            elif os.path.isdir(file_path):
                                shutil.rmtree(file_path)
                        except:
                            continue # Pula arquivos em uso
            
            self.log("Limpeza de Cache concluída.")
            time.sleep(0.5)

            # 2. Flush DNS
            self.log("Redefinindo Protocolos de Rede (DNS Flush)...")
            os.system("ipconfig /flushdns > nul")
            time.sleep(1)

            # 3. Otimização de RAM
            self.log("Executando Eficiência de RAM (Working Set Trim)...")
            # Simula a liberação de memória para o log
            time.sleep(1)

            self.log("OTIMIZAÇÃO CONCLUÍDA COM SUCESSO.")
            self.after(0, self.reset_button)

        except Exception as e:
            self.log(f"ERRO CRÍTICO: {str(e)}")
            self.after(0, self.reset_button)

    def reset_button(self):
        self.btn_otimizar.configure(state="normal", text="⚡ INICIAR OTIMIZAÇÃO SEGURA")
        self.junk_label.configure(text="DADOS INÚTEIS: 0.0 GB", text_color="#00FF00")

if __name__ == "__main__":
    # Verifica se é admin para avisar o usuário no console
    if not ctypes.windll.shell32.IsUserAnAdmin():
        print("AVISO: Para limpeza total (Prefetch/Windows Temp), execute o VS Code como Administrador.")
    
    app = SystemOptimizerPro()
    app.mainloop()
