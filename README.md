# MiniprojPython
import tkinter as tk
from tkinter import filedialog
from PIL import Image, ImageGrab

class DigitalWhiteboard:
    def __init__(self, root):
        self.root = root
        self.root.title("Digital Whiteboard")
        self.root.geometry("800x600")

        self.canvas = tk.Canvas(root, bg="white", width=800, height=600)
        self.canvas.pack(expand=tk.YES, fill=tk.BOTH)

        # Initialize drawing parameters
        self.line_color = "black"
        self.line_width = 2
        self.drawing = False
        self.old_x = None
        self.old_y = None

        # Keep track of drawing actions for undo and redo
        self.drawing_actions = []
        self.current_action_index = -1

        # Add menu
        menu_bar = tk.Menu(root)
        root.config(menu=menu_bar)

        file_menu = tk.Menu(menu_bar, tearoff=0)
        menu_bar.add_cascade(label="File", menu=file_menu)
        file_menu.add_command(label="Save", command=self.save_drawing)

        color_menu = tk.Menu(menu_bar, tearoff=0)
        menu_bar.add_cascade(label="Color", menu=color_menu)
        color_menu.add_command(label="Black", command=lambda: self.set_line_color("black"))
        color_menu.add_command(label="Red", command=lambda: self.set_line_color("red"))
        color_menu.add_command(label="Blue", command=lambda: self.set_line_color("blue"))
        color_menu.add_command(label="Green", command=lambda: self.set_line_color("green"))

        width_menu = tk.Menu(menu_bar, tearoff=0)
        menu_bar.add_cascade(label="Line Width", menu=width_menu)
        width_menu.add_command(label="2", command=lambda: self.set_line_width(2))
        width_menu.add_command(label="4", command=lambda: self.set_line_width(4))
        width_menu.add_command(label="6", command=lambda: self.set_line_width(6))

        bg_color_menu = tk.Menu(menu_bar, tearoff=0)
        menu_bar.add_cascade(label="Background Color", menu=bg_color_menu)
        bg_color_menu.add_command(label="White", command=lambda: self.set_canvas_bg_color("white"))
        bg_color_menu.add_command(label="Light Gray", command=lambda: self.set_canvas_bg_color("lightgray"))
        bg_color_menu.add_command(label="Light Blue", command=lambda: self.set_canvas_bg_color("lightblue"))
        bg_color_menu.add_command(label="Light Green", command=lambda: self.set_canvas_bg_color("lightgreen"))

        # Bind mouse events
        self.canvas.bind("<B1-Motion>", self.paint)
        self.canvas.bind("<ButtonRelease-1>", self.reset)

        save_image_button = tk.Button(root, text="Save Image", command=self.save_canvas_as_image)
        save_image_button.pack(side=tk.RIGHT, padx=10)

    def paint(self, event):
        x, y = event.x, event.y
        if self.old_x and self.old_y:
            line = self.canvas.create_line((self.old_x, self.old_y, x, y), width=self.line_width, fill=self.line_color,
                                           capstyle=tk.ROUND, smooth=tk.TRUE)
            self.drawing_actions.append(line)
        self.old_x = x
        self.old_y = y

    def reset(self, event):
        self.old_x = None
        self.old_y = None
        self.current_action_index += 1
        self.drawing_actions = self.drawing_actions[:self.current_action_index + 1]

    def set_line_color(self, color):
        self.line_color = color

    def set_line_width(self, width):
        self.line_width = width

    def set_canvas_bg_color(self, color):
        self.canvas.config(bg=color)

    def save_drawing(self):
        file_path = filedialog.asksaveasfilename(defaultextension=".png", filetypes=[("PNG files", "*.png")])
        if file_path:
            x = self.root.winfo_rootx() + self.canvas.winfo_x()
            y = self.root.winfo_rooty() + self.canvas.winfo_y()
            x1 = x + self.canvas.winfo_width()
            y1 = y + self.canvas.winfo_height()
            ImageGrab.grab(bbox=(x, y, x1, y1)).save(file_path)

    def save_canvas_as_image(self):
        file_path = filedialog.asksaveasfilename(defaultextension=".png", filetypes=[("PNG files", "*.png")])
        if file_path:
            x = self.root.winfo_rootx() + self.canvas.winfo_x()
            y = self.root.winfo_rooty() + self.canvas.winfo_y()
            x1 = x + self.canvas.winfo_width()
            y1 = y + self.canvas.winfo_height()
            ImageGrab.grab(bbox=(x, y, x1, y1)).save(file_path)

if __name__ == "__main__":
    root = tk.Tk()
    whiteboard = DigitalWhiteboard(root)
    root.mainloop()
