class InventoryApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Inventory Management System")
        self.is_logged_in = False
        self.create_login_screen()
    def create_login_screen(self):
        """Create login screen for user authentication."""
        self.login_frame = tk.Frame(self.root)
        self.login_frame.pack()
        tk.Label(self.login_frame, text="Username").grid(row=0, column=0)
        tk.Label(self.login_frame, text="Password").grid(row=1, column=0)
        self.username_entry = tk.Entry(self.login_frame)
        self.password_entry = tk.Entry(self.login_frame, show="*")
        self.username_entry.grid(row=0, column=1)
        self.password_entry.grid(row=1, column=1)
        tk.Button(self.login_frame, text="Login", command=self.login).grid(row=2, column=1)
    def login(self):
        username = self.username_entry.get()
        password = self.password_entry.get()
        if authenticate(username, password):
            self.is_logged_in = True
            self.login_frame.pack_forget()
            self.create_inventory_screen()
        else:
            messagebox.showerror("Error", "Invalid credentials")
    def create_inventory_screen(self):
        """Create main inventory management screen."""
        self.inventory_frame = tk.Frame(self.root)
        self.inventory_frame.pack()
        tk.Button(self.inventory_frame, text="Add Product", command=self.add_product).grid(row=0, column=0)
        tk.Button(self.inventory_frame, text="Edit Product", command=self.edit_product).grid(row=0, column=1)
        tk.Button(self.inventory_frame, text="Delete Product", command=self.delete_product).grid(row=0, column=2)
        tk.Button(self.inventory_frame, text="View Inventory", command=self.view_inventory).grid(row=0, column=3)
        tk.Button(self.inventory_frame, text="Generate Report", command=self.generate_report).grid(row=0, column=4)
    def add_product(self):
        """Add a new product to the inventory."""
        product_name = tk.simpledialog.askstring("Product Name", "Enter product name:")
        if not product_name:
            return
        try:
            stock = int(tk.simpledialog.askstring("Stock", "Enter stock level:"))
            price = float(tk.simpledialog.askstring("Price", "Enter price:"))
            inventory[product_name] = {"stock": stock, "price": price}
            messagebox.showinfo("Success", f"Product '{product_name}' added!")
        except ValueError:
            messagebox.showerror("Error", "Invalid stock or price value.")
    def edit_product(self):
        """Edit an existing product's details."""
        product_name = tk.simpledialog.askstring("Edit Product", "Enter product name to edit:")
        if product_name not in inventory:
            messagebox.showerror("Error", "Product not found.")
            return
        try:
            stock = int(tk.simpledialog.askstring("Stock", "Enter new stock level:"))
            price = float(tk.simpledialog.askstring("Price", "Enter new price:"))
            inventory[product_name].update({"stock": stock, "price": price})
            messagebox.showinfo("Success", f"Product '{product_name}' updated!")
        except ValueError:
            messagebox.showerror("Error", "Invalid stock or price value.")
    def delete_product(self):
        """Delete a product from the inventory."""
        product_name = tk.simpledialog.askstring("Delete Product", "Enter product name to delete:")
        if product_name in inventory:
            del inventory[product_name]
            messagebox.showinfo("Success", f"Product '{product_name}' deleted!")
        else:
            messagebox.showerror("Error", "Product not found.")
    def view_inventory(self):
        """Display all products in the inventory."""
        inventory_window = tk.Toplevel(self.root)
        inventory_window.title("Inventory List")
        for index, (product, details) in enumerate(inventory.items(), start=1):
            tk.Label(inventory_window, text=f"{index}. {product} - Stock: {details['stock']}, Price: ${details['price']:.2f}").pack()
    def generate_report(self):
        """Generate low-stock report."""
        report = {product: details for product, details in inventory.items() if details['stock'] < 5}
        if not report:
            messagebox.showinfo("Report", "No low-stock items.")
            return
        report_window = tk.Toplevel(self.root)
        report_window.title("Low-Stock Report")
        for product, details in report.items():
            tk.Label(report_window, text=f"{product} - Stock: {details['stock']}").pack()


# Run the application
root = tk.Tk()
app = InventoryApp(root)
root.mainloop()
