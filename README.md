# d-shape-drawer
import tkinter as tk
from tkinter import ttk
from tkinter import filedialog


root = tk.Tk()
root.title("Tkinter GUI Example")


label = tk.Label(root, text="SHAPE DRAWER")
label.pack(pady=50)


button = tk.Button(root, text="Press to draw", command=root.quit)
button.pack(pady=25)


root.mainloop()

root.mainloop()

class ToolTip:
    def __init__(self, widget, tooltip_text):
        self.widget = widget
        self.tooltip_text = tooltip_text
        self.tooltip = None

        self.widget.bind("<Enter>", self.display_tooltip)
        self.widget.bind("<Leave>", self.hide_tooltip)

    def display_tooltip(self, event):
        x, y, _, _ = self.widget.bbox("insert")
        x += self.widget.winfo_rootx() + 25
        y += self.widget.winfo_rooty() + 20

        self.tooltip = tk.Toplevel(self.widget)
        self.tooltip.wm_overrideredirect(True)
        self.tooltip.wm_geometry(f"+{x}+{y}")

        label = tk.Label(self.tooltip, text=self.tooltip_text, background="lightyellow", relief="solid", borderwidth=1)
        label.pack(ipadx=5, ipady=5)

    def hide_tooltip(self, event):
        if self.tooltip:
            self.tooltip.destroy()
            self.tooltip = None

root = tk.Tk()
root.title("Shape Drawer")

# Function to draw shapes
def draw_shape(event):
    global start_x, start_y, active_shape
    
    selected_shape = shape_var.get()
    color = color_entry.get()
    
    start_x, start_y = event.x, event.y

    if selected_shape == "Circle":
        active_shape = canvas.create_oval(start_x, start_y, start_x, start_y, fill=color)
    elif selected_shape == "Rectangle":
        active_shape = canvas.create_rectangle(start_x, start_y, start_x, start_y, fill=color)
    elif selected_shape == "Polygon":
        # For polygons, we need to provide a list of coordinates representing vertices
        active_shape = canvas.create_polygon(start_x, start_y, start_x + 50, start_y + 50, start_x - 50, start_y + 50, fill=color)


# Function to resize shapes
def resize_shape(event):
    global end_x, end_y
    
    end_x, end_y = event.x, event.y
    
    canvas.coords(active_shape, start_x, start_y, end_x, end_y)

# Function to delete shapes
def delete_shape():
    canvas.delete(active_shape)

def load_shapes():
    global shapes
    filename = filedialog.askopenfilename(filetypes=[("Text Files", "*.txt")])
    if filename:
        shapes = []
        with open(filename, "r") as file:
            for line in file:
                shape_data = eval(line.strip())
                shape_type, color, coords, _ = shape_data
                shape = None
                if shape_type == "Circle":
                    shape = canvas.create_oval(*coords, fill=color)
                elif shape_type == "Rectangle":
                    shape = canvas.create_rectangle(*coords, fill=color)
                elif shape_type == "Polygon":
                    shape = canvas.create_polygon(*coords, fill=color)
                shapes.append((shape_type, color, coords, shape))
def save_shapes():
    filename = filedialog.asksaveasfilename(defaultextension=".txt", filetypes=[("Text Files", "*.txt")])
    if filename:
        with open(filename, "w") as file:
            for shape in shapes:
                file.write(f"{shape}\n")

shape_frame = tk.Frame(root)
shape_frame.pack(pady=10)

shape_var = tk.StringVar()
shape_var.set("Circle")
shape_label = tk.Label(shape_frame, text="Select Shape:")
shape_label.pack(side="left", padx=10)

shape_menu = tk.OptionMenu(shape_frame, shape_var, "Circle", "Rectangle", "Polygon")
shape_menu.pack(side="left", padx=10)

param_frame = tk.Frame(root)
param_frame.pack(pady=10)

color_label = tk.Label(param_frame, text="Color:")
color_label.grid(row=0, column=0, padx=5, pady=5)
color_entry = tk.Entry(param_frame)
color_entry.grid(row=0, column=1, padx=5, pady=5)

canvas = tk.Canvas(root, width=1500, height=800, bg="black")
canvas.pack(pady=10)

canvas.bind("<Button-1>", draw_shape)  
canvas.bind("<B1-Motion>", resize_shape)  

delete_button = tk.Button(root, text="Delete Shape", command=delete_shape)
delete_button.pack(pady=10)

save_button = tk.Button(root, text="Save Shapes", command=save_shapes)
save_button.pack(pady=5)

load_button = tk.Button(root, text="Load Shapes", command=load_shapes)
load_button.pack(pady=5)

root.mainloop()
