# RGB-Analog-clock-
import tkinter as tk
import time
import math
import itertools

# Cycle through RGB colors
rgb_colors = itertools.cycle([
    "#FF0000", "#FF7F00", "#FFFF00", "#00FF00",
    "#00FFFF", "#0000FF", "#8B00FF"
])

class FullScreenAnalogClock(tk.Tk):
    def __init__(self):
        super().__init__()
        self.attributes('-fullscreen', True)
        self.configure(bg='black')
        self.width = self.winfo_screenwidth()
        self.height = self.winfo_screenheight()
        self.center_x = self.width // 2
        self.center_y = self.height // 2
        self.radius = min(self.center_x, self.center_y) - 20

        self.canvas = tk.Canvas(self, width=self.width, height=self.height, bg='black', highlightthickness=0)
        self.canvas.pack()

        self.border_color = next(rgb_colors)
        self.update_clock()

    def update_clock(self):
        self.canvas.delete("all")
        self.border_color = next(rgb_colors)
        self.draw_circle_border()
        self.draw_numbers()
        self.draw_center_dot()
        self.draw_date()
        self.draw_hands()
        self.after(1000, self.update_clock)

    def draw_circle_border(self):
        self.canvas.create_oval(
            self.center_x - self.radius, self.center_y - self.radius,
            self.center_x + self.radius, self.center_y + self.radius,
            outline=self.border_color, width=10
        )

    def draw_numbers(self):
        for i in range(1, 13):
            angle = math.radians(i * 30 - 90)
            x = self.center_x + math.cos(angle) * (self.radius * 0.85)
            y = self.center_y + math.sin(angle) * (self.radius * 0.85)
            self.canvas.create_text(x, y, text=str(i), fill="white", font=("Arial", 24, "bold"))

    def draw_center_dot(self):
        self.canvas.create_oval(
            self.center_x - 15, self.center_y - 15,
            self.center_x + 15, self.center_y + 15,
            fill="yellow", outline="yellow"
        )

    def draw_date(self):
        date_str = time.strftime("%d %B %Y")
        self.canvas.create_text(
            self.center_x, self.center_y + self.radius * 0.65,
            text=date_str, fill="yellow", font=("Helvetica", 10, "bold")
        )

    def draw_hands(self):
        now = time.localtime()
        hour = now.tm_hour % 12 + now.tm_min / 60.0
        minute = now.tm_min + now.tm_sec / 60.0
        second = now.tm_sec + time.time() % 1  # for smoother second hand

        # Hour
        self.draw_hand(hour * 30 - 90, self.radius * 0.5, "red", 8)
        # Minute
        self.draw_hand(minute * 6 - 90, self.radius * 0.75, "green", 5)
        # Second
        self.draw_hand(second * 6 - 90, self.radius * 0.9, "white", 2)

    def draw_hand(self, angle_deg, length, color, width):
        angle = math.radians(angle_deg)
        x = self.center_x + math.cos(angle) * length
        y = self.center_y + math.sin(angle) * length
        self.canvas.create_line(self.center_x, self.center_y, x, y, fill=color, width=width)

        # Draw yellow selector dot at tip
        self.canvas.create_oval(
            x - 5, y - 5, x + 5, y + 5,
            fill="yellow", outline="yellow"
        )

if __name__ == "__main__":
    FullScreenAnalogClock().mainloop()
