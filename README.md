# financial-data-sim

import random
import customtkinter as ctk
from collections import Counter  # For volume counting (not used yet)
import matplotlib.pyplot as plt
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg, NavigationToolbar2Tk
from matplotlib.figure import Figure

#                       Theme settings for customtkinter {dark mode}
ctk.set_appearance_mode("dark")
ctk.set_default_color_theme("blue")

def market(start_value, change=0.1, num_steps=1, lower_barrier=0, upper_barrier=40):
    """
    Random walking funck, market simulation. {+-change} with {50/50} probability. This funck has {lower_barrier} and
    {upper_barrier} to finish our random walk. sim.


    Parms{}:
    - start_value: (float or int)
    - change:  (floating step value from {0.1} to {10})
    - num_steps: max num of steps {100}
    - lower_barrier: {0}
    - upper_barrier: {40}

    Returns all values on ich step
    """
    values = [start_value]  # our START point
    current = start_value

    steps_taken = 0
    for _ in range(num_steps):
         #                       generate random value with range from {0.1} to {10}

        step_size = random.uniform(0.1, 10)

         #                       with 50/50 prblty +step_size or -step_size

        step = random.choice([step_size, -step_size])
        current += step
        values.append(current)
        steps_taken += 1

         #                       STOP iff current touch one of barriers

        if current <= lower_barrier or current >= upper_barrier:
            print(f"R.W. stopped at a step {steps_taken}: value {current} (barrier {'lower' if current <= lower_barrier else 'upper'})")
            break

    else:
        print(f"maximum reached {num_steps} steps qtty (rarely с p=0.5)")

    return values

class MarketSimulatorApp:
    def __init__(self):
        self.root = ctk.CTk()
        self.root.title("Market simulation: random walk")
        self.root.geometry("800x600")

         # buttom for start
        self.simulate_button = ctk.CTkButton(
            self.root,
            text="Start simulation",
            command=self.run_simulation,
            width=200,
            height=40
        )
        self.simulate_button.pack(pady=20)

         # matplotlib fraim (dinamic)
        self.plot_frame = ctk.CTkFrame(self.root)
        self.plot_frame.pack(fill="both", expand=True, padx=20, pady=20)

         # Default parms
        self.start_value = 500
        self.num_steps = 1000000
        self.lower_barrier = 0
        self.upper_barrier = 1000

    def run_simulation(self):
         # sim start
        result = market(
            self.start_value,
            0.1,
            self.num_steps,
            self.lower_barrier,
            self.upper_barrier
        )

         # cleaning priviose graph
        for widget in self.plot_frame.winfo_children():
            widget.destroy()

         # creatin a matplotlib figure
        fig = Figure(figsize=(8, 5), dpi=100)
        ax = fig.add_subplot(111)
        ax.plot(result, linewidth=1, color='cyan')
        ax.set_title('Price simulacion (random walk)')
        ax.set_xlabel('Steps')
        ax.set_ylabel('Price')
        ax.grid(True, alpha=0.3)
        ax.axhline(y=self.lower_barrier, color='red', linestyle='--', label=f'lower barrier ({self.lower_barrier})')
        ax.axhline(y=self.upper_barrier, color='green', linestyle='--', label=f'upper barrier ({self.upper_barrier})')
        ax.legend()

         # integration with tkinter/customtkinter
        canvas = FigureCanvasTkAgg(fig, master=self.plot_frame)
        canvas.draw()

         # adding the interactive toolbar for zoom, pan and ...
        toolbar = NavigationToolbar2Tk(canvas, self.plot_frame)
        toolbar.update()

         # canvas and toolbar
        canvas.get_tk_widget().pack(fill="both", expand=True)
        toolbar.pack(side="top", fill="x")

        print("Graph updated")

    def run(self):
        self.root.mainloop()


if __name__ == "__main__":
    app = MarketSimulatorApp()
    app.run()
