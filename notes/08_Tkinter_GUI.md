---
createdAt: 2026-11-27T06:31:27.709Z
updatedAt: 2026-11-27T06:31:43.657Z
lastViewedAt: 2026-11-27T06:31:27.709Z
field: Python/第三方库/matplotlib
---
# Tkinter GUI

本篇整理自原 `matplotlib turorial/GUI-tk.md`。

## 基础控件

### Button（按钮）

```python
Button = tk.Button(window, text=, bg=, fg=, width=, height=, command=)
```

### Label（标签）

```python
Label = tk.Label(window, text= or textvariable=, bg="green", fg, font=, width=, height=)
```

### Entry（输入框）

```python
E = tk.Entry(window, show="" or None)
A = E.get()
```

### Insert

```python
End, insert, x.y
```

### Listbox（列表框）

```python
Lb = tk.Listbox(window, listvariable=var1)
var1.set()
lb.insert("end", item) or (item, "end")
lb.delete(item)
```

### Radiobutton（单选按钮）

```python
Radiobutton = tk.Radiobutton(window, text=, variable=var1, value=, command=)
```

### Scale（滑块）

```python
Scale = tk.Scale(window, label=, from_=, to=, orient=tk.HORIZONTAL, length=, showvalue=0, tickinterval=, resolution=, command=)
```

### Checkbutton（复选框）

```python
tk.Checkbutton(window, text=, variable=var1, onvalue=, offvalue=, command=)
```

## canvas 画布

```python
Canvas = tk.Canvas(window, bg=, width=, height=)

image_file = tk.PhotoImage(file="")

Image = tk.canvas.create_image(10, 10, anchor=, image=)

Line = tk.canvac.create_line(x0, y0, x, y)  # 直接输入坐标值，不用写 x=

Oval = ~(x~, fill="red")

Arc = ~(~, start=, end=180)

Rectangle =

# 移动
canvas.move(arc, x, y)  # 点击一次移动 x, y
```

## 菜单栏

```python
Menubar = tk.Menu(window)

Filemenu = tk.Menu(menubar, tearoff=0)

menubar.add_cascade(label="", menu=filemenu)

filemenu.add_command(label="", command=)

filemenu.add_separator()

Subfilemenu = tk.Menu(filemenu)

filemenu.add_cascade(label=, menu=, underline=)

subfilemenu.add_command()
```

## frame 框架

```python
Frm = tk.Frame(window)

Frm.pack(window)

frm1 = tk.Frame(Frm)

frm1.pack(side="left")
```

## Messagebox（弹窗）

```python
tk.messagebox.showinfo(title=, message=)
tk.messagebox.showerror()
tk.messagebox.showwarning()
tk.messagebox.askquestion()
tk.messagebox.asktrycancel()
tk.messagebox.askyesno()
tk.messagebox.askokcancel
```

## 放置位置

1. `pack()`：参数 `pack(side=)`；或 `x=`、`y=`
2. `grid()`：参数 `grid(row=, column=, padx=, pady=)`（原笔记写作 `grind()`）
3. `place()`：参数 `place(x=, y=)`
