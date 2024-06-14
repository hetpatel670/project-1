import tkinter as tk
import sqlite3
from tkinter import messagebox

# Function to save employee details
def save_employee_details():
    # Retrieve employee name and amount credited from entry fields
    employee_name = name_entry.get()
    amount_credited = credited_entry.get()

    # Connect to SQLite database
    conn = sqlite3.connect('employee_details.db')
    c = conn.cursor()

    # Create table if not exists
    c.execute('''CREATE TABLE IF NOT EXISTS employees
                 (name TEXT, amount_credited REAL)''')

    # Insert employee details into the table
    c.execute("INSERT INTO employees (name, amount_credited) VALUES (?, ?)", (employee_name, amount_credited))

    # Commit changes and close connection
    conn.commit()
    conn.close()

    # Display success message
    messagebox.showinfo("Save Successful", f"Employee {employee_name} details saved. Amount credited: {amount_credited}")

# Function to search for employee details
def search_employee_details():
    # Retrieve employee name to search for from the entry field
    search_name = search_entry.get()

    # Connect to SQLite database
    conn = sqlite3.connect('employee_details.db')
    c = conn.cursor()

    # Search for employee details in the table
    c.execute("SELECT * FROM employees WHERE name=?", (search_name,))
    result = c.fetchone()

    # Close connection
    conn.close()

    if result:
        messagebox.showinfo("Search Result", f"Employee {result[0]} details found. Amount credited: {result[1]}")
    else:
        messagebox.showinfo("Search Result", f"Employee {search_name} details not found.")

# Function to display history
def display_history():
    # Connect to SQLite database
    conn = sqlite3.connect('employee_details.db')
    c = conn.cursor()

    # Retrieve all employee details from the table
    c.execute("SELECT * FROM employees")
    results = c.fetchall()

    # Close connection
    conn.close()

    if results:
        history = "\n".join([f"Employee: {row[0]}, Amount Credited: {row[1]}" for row in results])
        messagebox.showinfo("History", history)
    else:
        messagebox.showinfo("History", "No history available.")

# Create GUI
root = tk.Tk()
root.title("Employee Details")

# Labels
tk.Label(root, text="Employee Name:", fg="blue").grid(row=0, column=0)
tk.Label(root, text="Amount Credited:", fg="blue").grid(row=1, column=0)
tk.Label(root, text="Search Employee:", fg="blue").grid(row=3, column=0)

# Entry fields
name_entry = tk.Entry(root, bg="lightblue")
name_entry.grid(row=0, column=1)
credited_entry = tk.Entry(root, bg="lightblue")
credited_entry.grid(row=1, column=1)
search_entry = tk.Entry(root, bg="lightblue")
search_entry.grid(row=3, column=1)

# Buttons
save_button = tk.Button(root, text="Save", command=save_employee_details, bg="green", fg="white")
save_button.grid(row=2, column=0, columnspan=2, pady=10)
search_button = tk.Button(root, text="Search", command=search_employee_details, bg="orange", fg="white")
search_button.grid(row=4, column=0, columnspan=2, pady=10)
history_button = tk.Button(root, text="Display History", command=display_history, bg="blue", fg="white")
history_button.grid(row=5, column=0, columnspan=2, pady=10)

root.mainloop()
