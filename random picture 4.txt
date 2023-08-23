import tkinter as tk
from tkinter import filedialog
import random
import time
import os
import sys
import threading
import pygame
from PIL import Image, ImageTk

root = tk.Tk()

folder1 = '' 
folder2 = ''
selected_sound_file = None  # To store the selected sound file

def select_folder1():
    global folder1
    folder1 = filedialog.askdirectory()

def select_folder2():
    global folder2
    folder2 = filedialog.askdirectory()

def select_sound_file():
    global selected_sound_file
    selected_sound_file = filedialog.askopenfilename(filetypes=[("Audio Files", "*.wav *.mp3")])

def play_beep():
    pygame.mixer.init()
    beep_sound = pygame.mixer.Sound(selected_sound_file) if selected_sound_file else pygame.mixer.Sound('default_beep.wav')
    beep_sound.play()

def start():
    while True:
        folder = random.choice([folder1, folder2])
        img_list = [img for img in os.listdir(folder) if img.lower().endswith(('.png', '.jpg', '.jpeg', '.gif'))]
        if not img_list:
            continue
        img_path = random.choice(img_list)
        img = Image.open(os.path.join(folder, img_path.replace('\\', '/')))
        img = img.resize((500, 500), Image.ANTIALIAS)
        img = ImageTk.PhotoImage(img)
        label.config(image=img)
        label.image = img
        
        if folder == folder1:
            beep_thread = threading.Thread(target=play_beep)
            beep_thread.start()
        
        root.update()
        time.sleep(3)
        
        if stop_pressed:
            break
        
stop_pressed = False
def stop():
    global stop_pressed 
    stop_pressed = True
    
button1 = tk.Button(text="Select Folder 1", command=select_folder1)
button2 = tk.Button(text="Select Folder 2", command=select_folder2)  
select_sound_button = tk.Button(text="Select Sound", command=select_sound_file)  # New button for sound selection
start_button = tk.Button(text="Start", command=start)
stop_button = tk.Button(text="Stop", command=stop)

label = tk.Label(root)
label.pack()

button1.pack()
button2.pack()
select_sound_button.pack()  # Pack the sound selection button
start_button.pack()  
stop_button.pack()

root.mainloop()
