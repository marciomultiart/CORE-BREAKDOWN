import tkinter as tk
import random

class HackerMasterSamsung:
    def __init__(self, root):
        self.root = root
        self.root.attributes("-fullscreen", True)
        self.root.configure(bg="#050505")
        
        self.canvas = tk.Canvas(root, bg="#050505", highlightthickness=0)
        self.canvas.place(relx=0, rely=0, relwidth=1, relheight=1)
        
        self.largura = root.winfo_screenwidth()
        self.altura = root.winfo_screenheight()
        self.drops = [random.randint(-20, 0) for _ in range(int(self.largura/20))]
        
        self.tempo_restante = 180 
        self.timer_rodando = False
        self.boss_ativo = False
        self.pressionado = False 

        self.label_timer = tk.Label(self.root, text="SINAL: 180s", bg="#050505", fg="#00FF41", font=("Courier", 10, "bold"))
        
        self.tela_inicial()
        self.animar_matrix()

    def limpar_tela(self):
        self.boss_ativo = False
        self.pressionado = False
        for widget in self.root.winfo_children():
            if widget not in [self.canvas, self.label_timer]:
                widget.destroy()
        self.canvas.delete("boss_layer")
        self.canvas.unbind("<ButtonPress-1>")
        self.canvas.unbind("<ButtonRelease-1>")
        self.canvas.unbind("<B1-Motion>")

    def tela_inicial(self):
        self.limpar_tela()
        self.label_timer.place_forget()
        
        # Título reduzido para caber na largura do Samsung
        tk.Label(self.root, text="CORE\nBREAKDOWN", bg="#050505", fg="#00FF41", 
                 font=("Courier", 26, "bold"), justify="center").place(relx=0.5, rely=0.3, anchor="center")
        
        tk.Label(self.root, text="SYSTEM ONLINE", bg="#050505", fg="#004400", 
                 font=("Courier", 9)).place(relx=0.5, rely=0.42, anchor="center")
        
        btn_start = tk.Button(self.root, text="[ INICIAR HACK ]", command=self.reiniciar_tudo, 
                              bg="#00FF41", fg="black", font=("Courier", 12, "bold"), 
                              activebackground="#008F11", padx=15, pady=8)
        btn_start.place(relx=0.5, rely=0.55, anchor="center")

    def reiniciar_tudo(self):
        self.timer_rodando = False
        self.tempo_restante = 180
        self.label_timer.place(relx=0.5, rely=0.03, anchor="n")
        self.label_timer.config(text="SINAL: 180s", fg="#00FF41")
        self.numero_secreto = random.randint(1, 10)
        self.fase_numeros()

    def atualizar_timer(self):
        if self.tempo_restante > 0 and self.timer_rodando:
            self.tempo_restante -= 1
            cor = "#FF3E3E" if self.tempo_restante <= 20 else "#00FF41"
            self.label_timer.config(text=f"SINAL: {self.tempo_restante}s", fg=cor)
            self.root.after(1000, self.atualizar_timer)
        elif self.tempo_restante <= 0 and self.timer_rodando:
            self.falha("TEMPO ESGOTADO")

    def fase_numeros(self):
        self.limpar_tela()
        self.cnt = tk.Frame(self.root, bg="#050505", highlightthickness=1, highlightbackground="#00FF41", padx=10, pady=10)
        self.cnt.place(relx=0.5, rely=0.3, anchor="center")
        
        tk.Label(self.cnt, text="[ FASE 1: ACESSO ]", bg="#050505", fg="#00FF41", font=("Courier", 10, "bold")).pack()
        self.label_status = tk.Label(self.cnt, text="DIGITE (1-10)", bg="#050505", fg="#555", font=("Courier", 9))
        self.label_status.pack(pady=5)

        self.entrada = tk.Entry(self.cnt, font=("Courier", 30, "bold"), justify='center', bg="#1a1a1a", fg="#00FF41", width=5, borderwidth=0)
        self.entrada.pack(pady=10)
        self.entrada.focus_set()
        self.entrada.bind('<Return>', lambda e: self.processar_input())

    def processar_input(self):
        if not self.timer_rodando: 
            self.timer_rodando = True
            self.atualizar_timer()
        cmd = self.entrada.get().strip()
        self.entrada.delete(0, tk.END)
        try:
            val = int(cmd)
            if val == self.numero_secreto: 
                self.fase_fios()
            elif val > self.numero_secreto:
                self.label_status.config(text="↓ MUITO ALTO", fg="#FF3E3E")
            else:
                self.label_status.config(text="↑ MUITO BAIXO", fg="#00A8FF")
        except: pass

    def fase_fios(self):
        self.limpar_tela()
        # Cores em formato string para evitar erro de comparação
        cores = [("#FF3E3E", "VERMELHO"), ("#00A8FF", "AZUL"), ("#FFD700", "AMARELO"), ("#A020F0", "ROXO")]
        random.shuffle(cores)
        self.cor_obj = random.choice(cores)
        self.alvo_fio_nome = self.cor_obj[1]
        
        f = tk.Frame(self.root, bg="#050505", highlightthickness=1, highlightbackground="#00FF41", padx=10, pady=10)
        f.place(relx=0.5, rely=0.4, anchor="center")
        
        self.lbl_radar = tk.Label(f, text=f"RADAR: {self.alvo_fio_nome}", bg="#050505", fg="#00FF41", font=("Courier", 11, "bold"))
        self.lbl_radar.pack(pady=10)
        self.root.after(4000, lambda: self.lbl_radar.config(text="RADAR: [OFFLINE]", fg="#333") if self.lbl_radar.winfo_exists() else None)

        for c, n in cores:
            tk.Button(f, text=f"FIO {n}", command=lambda n_fix=n: self.aviso_boss() if n_fix == self.alvo_fio_nome else self.falha("FIO ERRADO"), 
                      bg=c, fg="white", font=("Arial", 9, "bold"), width=14, pady=6).pack(pady=3)

    def aviso_boss(self):
        self.limpar_tela()
        aviso = tk.Label(self.root, text="ALVO DETECTADO:\nFIREWALL CORE", bg="#050505", fg="#FF3E3E", font=("Courier", 14, "bold"))
        aviso.place(relx=0.5, rely=0.5, anchor="center")
        self.root.after(2000, self.fase_boss)

    def fase_boss(self):
        self.limpar_tela()
        self.boss_ativo = True
        self.timer_rodando = False
        self.label_timer.place_forget()
        
        self.boss_vida = 1000
        self.boss_dir = 8 
        self.nave_x = self.largura // 2

        self.canvas.create_rectangle(self.largura*0.1, 40, self.largura*0.9, 60, fill="#222", tags="boss_layer")
        self.barra_v = self.canvas.create_rectangle(self.largura*0.1, 40, self.largura*0.9, 60, fill="#00FF41", tags="boss_layer")
        self.lbl_hp = self.canvas.create_text(self.largura//2, 50, text=f"HP: {self.boss_vida}", fill="white", font=("Courier", 8), tags="boss_layer")
        
        # Boss um pouco menor para caber melhor
        self.boss_obj = self.canvas.create_rectangle(self.largura//2-70, 80, self.largura//2+70, 150, fill="#00FF41", outline="white", tags="boss_layer")
        self.nave_obj = self.canvas.create_polygon(self.nave_x, self.altura-200, self.nave_x-25, self.altura-160, self.nave_x+25, self.altura-160, fill="white", tags="boss_layer")

        self.canvas.bind("<ButtonPress-1>", self.on_press)
        self.canvas.bind("<ButtonRelease-1>", self.on_release)
        self.canvas.bind("<B1-Motion>", self.on_drag)
        self.atualizar_boss()

    def on_press(self, e):
        self.pressionado = True
        self.on_drag(e)
        self.fire_loop()

    def on_release(self, e):
        self.pressionado = False

    def on_drag(self, e):
        if self.boss_ativo:
            self.nave_x = max(30, min(self.largura-30, e.x))
            self.canvas.coords(self.nave_obj, self.nave_x, self.altura-200, self.nave_x-25, self.altura-160, self.nave_x+25, self.altura-160)

    def fire_loop(self):
        if self.boss_ativo and self.pressionado and self.boss_vida > 0:
            t_val = "1" if random.random() < 0.2 else "0"
            tiro = self.canvas.create_text(self.nave_x, self.altura-210, text=t_val, fill="#00FF41", font=("Courier", 16, "bold"), tags="boss_layer")
            self.animar_tiro(tiro, 30 if t_val=="1" else 10)
            self.root.after(140, self.fire_loop)

    def atualizar_boss(self):
        if self.boss_ativo and self.boss_vida > 0:
            try:
                coords = self.canvas.coords(self.boss_obj)
                if coords[2] >= self.largura or coords[0] <= 0: self.boss_dir *= -1
                self.canvas.move(self.boss_obj, self.boss_dir, 0)
                self.root.after(30, self.atualizar_boss)
            except: pass

    def animar_tiro(self, t, d):
        if self.boss_ativo:
            self.canvas.move(t, 0, -40)
            try:
                p = self.canvas.coords(t)
                b = self.canvas.coords(self.boss_obj)
                if p[1] < b[3] and b[0] < p[0] < b[2]:
                    self.canvas.delete(t)
                    self.boss_vida = max(0, self.boss_vida - d)
                    cor = "#00FF41"
                    if self.boss_vida < 300: cor = "#FF3E3E"
                    elif self.boss_vida < 700: cor = "#FFD700"
                    self.canvas.itemconfig(self.boss_obj, fill=cor)
                    self.canvas.itemconfig(self.barra_v, fill=cor)
                    self.canvas.itemconfig(self.lbl_hp, text=f"HP: {self.boss_vida}")
                    larg = self.largura * 0.1 + (self.largura * 0.8 * (self.boss_vida/1000))
                    self.canvas.coords(self.barra_v, self.largura*0.1, 40, larg, 60)
                    if self.boss_vida <= 0: self.vitoria_total()
                elif p[1] > 0: self.root.after(15, lambda: self.animar_tiro(t, d))
                else: self.canvas.delete(t)
            except: self.canvas.delete(t)

    def vitoria_total(self):
        self.limpar_tela()
        tk.Label(self.root, text="SISTEMA\nHACKEADO", bg="#050505", fg="#00FF41", font=("Courier", 18, "bold")).place(relx=0.5, rely=0.5, anchor="center")
        self.root.after(4000, self.tela_inicial)

    def falha(self, m):
        self.timer_rodando = False
        self.limpar_tela()
        tk.Label(self.root, text=f"FALHA: {m}", bg="#050505", fg="#FF3E3E", font=("Courier", 14, "bold")).place(relx=0.5, rely=0.5, anchor="center")
        self.root.after(3000, self.tela_inicial)

    def animar_matrix(self):
        self.canvas.delete("bits")
        for i in range(len(self.drops)):
            x, y = i * 20, self.drops[i] * 20
            self.canvas.create_text(x, y, text=random.choice(["0", "1"]), fill="#001500", font=("Courier", 8), tags="bits")
            if y > self.altura or random.random() > 0.98: self.drops[i] = 0
            self.drops[i] += 1
        self.root.after(85, self.animar_matrix)

if __name__ == "__main__":
    root = tk.Tk()
    app = HackerMasterSamsung(root)
    root.mainloop()
