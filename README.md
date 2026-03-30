# =============================
# Student Management System (Modern GUI)
# =============================

import tkinter as tk
from tkinter import messagebox
import json
import os

FILE = "tasks.json"

# Load data
if os.path.exists(FILE):
    with open(FILE, "r") as f:
        tasks = json.load(f)
else:
    tasks = []

# Save data

def save_tasks():
    with open(FILE, "w") as f:
        json.dump(tasks, f, indent=4)

# Add task

def add_task():
    title = title_entry.get()
    deadline = deadline_entry.get()

    if title == "":
        messagebox.showwarning("Warning", "Task cannot be empty")
        return

    task = {
        "title": title,
        "deadline": deadline,
        "status": "Pending"
    }

    tasks.append(task)
    save_tasks()
    update_list()
    animate_add()

    title_entry.delete(0, tk.END)
    deadline_entry.delete(0, tk.END)

# Update list

def update_list():
    task_listbox.delete(0, tk.END)
    for i, task in enumerate(tasks):
        status_color = "🟢" if task['status'] == "Done" else "🔴"
        display = f"{status_color} {task['title']} | {task['deadline']}"
        task_listbox.insert(tk.END, display)

# Mark complete

def mark_complete():
    try:
        index = task_listbox.curselection()[0]
        tasks[index]['status'] = "Done"
        save_tasks()
        update_list()
    except:
        messagebox.showwarning("Warning", "Select a task")

# Delete task

def delete_task():
    try:
        index = task_listbox.curselection()[0]
        tasks.pop(index)
        save_tasks()
        update_list()
    except:
        messagebox.showwarning("Warning", "Select a task")

# Animation (simple flash effect)

def animate_add():
    original = root.cget("bg")
    root.config(bg="#d1ffd6")
    root.after(200, lambda: root.config(bg=original))

# GUI Setup
root = tk.Tk()
root.title("Student Management System")
root.geometry("600x550")
root.config(bg="#1e1e2f")

# Title
label = tk.Label(root, text="📘 Student Task Manager", font=("Arial", 18, "bold"), fg="white", bg="#1e1e2f")
label.pack(pady=15)

# Input Frame
frame = tk.Frame(root, bg="#2c2c3e", bd=2, relief="ridge")
frame.pack(pady=10, padx=20, fill="x")

# Task
tk.Label(frame, text="Task", fg="white", bg="#2c2c3e").grid(row=0, column=0, padx=10, pady=5)
title_entry = tk.Entry(frame, width=30, bg="#3c3c50", fg="white", insertbackground="white")
title_entry.grid(row=0, column=1, padx=10, pady=5)

# Deadline
tk.Label(frame, text="Deadline", fg="white", bg="#2c2c3e").grid(row=1, column=0, padx=10, pady=5)
deadline_entry = tk.Entry(frame, width=30, bg="#3c3c50", fg="white", insertbackground="white")
deadline_entry.grid(row=1, column=1, padx=10, pady=5)

# Buttons
btn_frame = tk.Frame(root, bg="#1e1e2f")
btn_frame.pack(pady=15)

btn_style = {"font": ("Arial", 10, "bold"), "width": 12, "bd": 0}

tk.Button(btn_frame, text="➕ Add", bg="#4CAF50", fg="white", command=add_task, **btn_style).grid(row=0, column=0, padx=10)
tk.Button(btn_frame, text="✔ Done", bg="#2196F3", fg="white", command=mark_complete, **btn_style).grid(row=0, column=1, padx=10)
tk.Button(btn_frame, text="❌ Delete", bg="#f44336", fg="white", command=delete_task, **btn_style).grid(row=0, column=2, padx=10)

# Listbox Frame
list_frame = tk.Frame(root, bg="#2c2c3e")
list_frame.pack(pady=10, padx=20, fill="both", expand=True)

# Scrollbar
scrollbar = tk.Scrollbar(list_frame)
scrollbar.pack(side="right", fill="y")

# Listbox
task_listbox = tk.Listbox(
    list_frame,
    width=60,
    height=15,
    bg="#3c3c50",
    fg="white",
    selectbackground="#6a5acd",
    activestyle="none",
    yscrollcommand=scrollbar.set
)
task_listbox.pack(fill="both", expand=True)

scrollbar.config(command=task_listbox.yview)

update_list()

root.mainloop()

# =============================
# UI Improvements Added:
# =============================
# - Dark theme
# - Colored buttons
# - Emoji indicators
# - Smooth flash animation
# - Styled input fields
# - Scrollable task list
