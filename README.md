import customtkinter as ctk
from tkinter import messagebox
from tkinter import filedialog
from PIL import Image, ImageTk
import qrcode

# Konfigurasi awal CustomTkinter
ctk.set_appearance_mode("System")  # Light/Dark mode
ctk.set_default_color_theme("blue")


class AyamApp(ctk.CTk):
    def __init__(self):
        super().__init__()
        self.title("AYAM")

        # Mengatur ukuran jendela secara manual
        window_width = 1920 # Ganti dengan lebar yang diinginkan
        window_height = 1050 # Ganti dengan tinggi yang diinginkan
        self.geometry(f"{window_width}x{window_height}")

        # Tambahkan background
        self.bg_image = Image.open("background.jpg")
        self.bg_image = self.bg_image.resize((window_width, window_height))  # Resize sesuai ukuran jendela manual
        self.bg_image_tk = ImageTk.PhotoImage(self.bg_image)

        self.bg_label = ctk.CTkLabel(self, image=self.bg_image_tk, text="")
        self.bg_label.place(x=0, y=0, relwidth=1, relheight=1)  # Mengisi seluruh jendela

        # Variabel
        self.buyer_name = ctk.StringVar()
        self.cart = {}

        # Halaman utama
        self.main_page()

    def main_page(self):
        self.clear_frame()

        # Membuat frame untuk menampung elemen dan menempatkannya di tengah
        center_frame = ctk.CTkFrame(self, bg_color="transparent")
        center_frame.place(relx=0.5, rely=0.5, anchor='center')  # Menempatkan frame di tengah

        # Menambahkan label sambutan
        welcome_label = ctk.CTkLabel(center_frame, text="Selamat Datang di Ayam Unesa", font=("Arial", 20), bg_color="transparent")
        welcome_label.pack(pady=20)

        # Tombol Take Away
        takeaway_button = ctk.CTkButton(center_frame, text="Take Away", command=self.buyer_page)
        takeaway_button.pack(pady=10)

        # Tombol Dine In
        dinein_button = ctk.CTkButton(center_frame, text="Dine In", command=self.buyer_page)
        dinein_button.pack(pady=10)

    def buyer_page(self):
        self.clear_frame()

    # Membuat frame untuk menampung elemen dan menempatkannya di tengah
        center_frame = ctk.CTkFrame(self, bg_color="transparent")  # Frame dengan latar belakang transparan
        center_frame.place(relx=0.5, rely=0.5, anchor='center')  # Menempatkan frame di tengah

    # Menambahkan label dan entry ke dalam frame
        buyer_label = ctk.CTkLabel(center_frame, text="Masukkan Nama Pembeli", font=("Arial", 16), bg_color="transparent")
        buyer_label.pack(pady=20)

        buyer_entry = ctk.CTkEntry(center_frame, textvariable=self.buyer_name, placeholder_text="Nama")
        buyer_entry.pack(pady=10)

        submit_button = ctk.CTkButton(center_frame, text="OK", command=self.menu_page)
        submit_button.pack(pady=10)

    def menu_page(self):
        if not self.buyer_name.get():
            messagebox.showerror("Error", "Nama pembeli wajib diisi!")
            return
        
        self.clear_frame()

        tab_view = ctk.CTkTabview(self)
        tab_view.pack(expand=True, fill="both")

        # Menu Ayam
        ayam_tab = tab_view.add("Ayam")
        self.add_menu_item(ayam_tab, "Ayam Goreng (Rp 20.000)", self.cart.get("Ayam Goreng (Rp 20.000)", 0))
        self.add_menu_item(ayam_tab, "Ayam Bakar (Rp 22.000)", self.cart.get("Ayam Bakar (Rp 22.000)", 0))
        self.add_menu_item(ayam_tab, "Ayam Kremes (Rp 21.000)", self.cart.get("Ayam Kremes (Rp 21.000)", 0))

        # Menu Nasi
        nasi_tab = tab_view.add("Nasi")
        self.add_menu_item(nasi_tab, "Nasi Putih (Rp 5.000)", self.cart.get("Nasi Putih (Rp 5.000)", 0))
        self.add_menu_item(nasi_tab, "Nasi Gurih (Rp 7.000)", self.cart.get("Nasi Gurih (Rp 7.000)", 0))
        self.add_menu_item(nasi_tab, "Nasi Daun Jeruk (Rp 8.000)", self.cart.get("Nasi Daun Jeruk (Rp 8.000)", 0))

        # Menu Minuman
        minuman_tab = tab_view.add("Minuman")
        self.add_menu_item(minuman_tab, "Es Teh (Rp 5.000)", self.cart.get("Es Teh (Rp 5.000)", 0))
        self.add_menu_item(minuman_tab, "Teh Hangat (Rp 5.000)", self.cart.get("Teh Hangat (Rp 5.000)", 0))
        self.add_menu_item(minuman_tab, "Es Jeruk (Rp 6.000)", self.cart.get("Es Jeruk (Rp 6.000)", 0))
        self.add_menu_item(minuman_tab, "Jeruk Hangat (Rp 6.000)", self.cart.get("Jeruk Hangat (Rp 6.000)", 0))
        self.add_menu_item(minuman_tab, "Air Mineral (Rp 4.000)", self.cart.get("Air Mineral (Rp 4.000)", 0))

        # Menu Sambal
        sambal_tab = tab_view.add("Sambal")
        self.add_menu_item(sambal_tab, "Sambal Tomat (Rp 3.000)", self.cart.get("Sambal Tomat (Rp 3.000)", 0))
        self.add_menu_item(sambal_tab, "Sambal Ijo (Rp 4.000)", self.cart.get("Sambal Ijo (Rp 4.000)", 0))
        self.add_menu_item(sambal_tab, "Sambal Matah (Rp 5.000)", self.cart.get("Sambal Matah (Rp 5.000)", 0))
        self.add_menu_item(sambal_tab, "Sambal Terasi (Rp 3.000)", self.cart.get("Sambal Terasi (Rp 3.000)", 0))

        show_chart_button = ctk.CTkButton(self, text="Show Chart", command=self.chart_page)
        show_chart_button.pack(pady=10)

    def add_menu_item(self, parent, item_name, quantity):
        frame = ctk.CTkFrame(parent)
        frame.pack(fill="x", pady=5, padx=10)

        label = ctk.CTkLabel(frame, text=item_name, font=("Arial", 14))
        label.pack(side="left", padx=10)

        quantity_label = ctk.CTkLabel(frame, text=f"{quantity}", font=("Arial", 14))
        quantity_label.pack(side="right", padx=10)

        def increment():
            self.cart[item_name] = self.cart.get(item_name, 0) + 1
            quantity_label.configure(text=f"{self.cart[item_name]}")
        
        def decrement():
            if self.cart.get(item_name, 0) > 0:
                self.cart[item_name] -= 1
                quantity_label.configure(text=f"{self.cart[item_name]}")
        
        plus_button = ctk.CTkButton(frame, text="+", command=increment)
        plus_button.pack(side="right", padx=5)

        minus_button = ctk.CTkButton(frame, text="-", command=decrement)
        minus_button.pack(side="right", padx=5)

    def chart_page(self):
        self.clear_frame()

        if not self.cart:
            messagebox.showerror("Error", "Keranjang kosong!")
            self.menu_page()
            return
        
        main_frame = ctk.CTkFrame(self)
        main_frame.pack(expand=True)

        chart_label = ctk.CTkLabel(main_frame, text="Keranjang Pemesanan", font=("Arial", 16))
        chart_label.pack(pady=20)

        total_price = 0  # Inisialisasi total harga

        for item, quantity in self.cart.items():
            if quantity > 0:
                # Ambil harga dari item (misalnya, "Ayam Goreng (Rp 20.000)")
                price = int(item.split("Rp")[1].replace(".", "").replace(")", "").strip())
                item_total = price * quantity
                total_price += item_total

                # Tampilkan item dan jumlahnya
                item_label = ctk.CTkLabel(main_frame, text=f"{item}: {quantity} x Rp {price:,} = Rp {item_total:,}", font=("Arial", 14))
                item_label.pack(pady=5)

        # Tampilkan total harga
        total_label = ctk.CTkLabel(main_frame, text=f"Total Harga: Rp {total_price:,}", font=("Arial", 16, "bold"))
        total_label.pack(pady=20)

        checkout_button = ctk.CTkButton(main_frame, text="Checkout", command=self.payment_page)
        checkout_button.pack(pady=10)

        back_button = ctk.CTkButton(main_frame, text="Back to Menu", command=self.menu_page)
        back_button.pack(pady=10)

    def payment_page(self):
        self.clear_frame()

        # Membuat frame untuk menampung elemen dan menempatkannya di tengah
        center_frame = ctk.CTkFrame(self)  # Frame tanpa latar belakang
        center_frame.place(relx=0.5, rely=0.5, anchor='center')  # Menempatkan frame di tengah

        # Menambahkan label dan tombol ke dalam frame
        payment_label = ctk.CTkLabel(center_frame, text="Pilih Metode Pembayaran", font=("Arial", 16))
        payment_label.pack(pady=20)

        cash_button = ctk.CTkButton(center_frame, text="Cash", command=self.generate_receipt)
        cash_button.pack(pady=10)

        cashless_button = ctk.CTkButton(center_frame, text="Cashless", command=self.generate_qr_code)
        cashless_button.pack(pady=10)

    def generate_receipt(self):
        self.clear_frame()

        receipt_content = f"Struk Pembelian\nPembeli: {self.buyer_name.get()}\n\n"
        total_price = 0  # Inisialisasi total harga

        for item, quantity in self.cart.items():
            if quantity > 0:
                # Ambil harga dari item (misalnya, "Ayam Goreng (Rp 20.000)")
                price = int(item.split("Rp")[1].replace(".", "").replace(")", "").strip())
                item_total = price * quantity
                total_price += item_total
                receipt_content += f"{item}: {quantity} x Rp {price:,} = Rp {item_total:,}\n"

        receipt_content += f"\nTotal Harga: Rp {total_price:,}"

        # Membuat frame untuk menampung elemen dan menempatkannya di tengah
        center_frame = ctk.CTkFrame(self)  # Frame tanpa latar belakang
        center_frame.place(relx=0.5, rely=0.5, anchor='center')  # Menempatkan frame di tengah

        # Menambahkan label dan tombol ke dalam frame
        receipt_label = ctk.CTkLabel(center_frame, text=receipt_content, font=("Arial", 14))
        receipt_label.pack(pady=20)

        download_receipt_button = ctk.CTkButton(center_frame, text="Download Struk", command=self.download_receipt)
        download_receipt_button.pack(pady=10)

        finish_button = ctk.CTkButton(center_frame, text="Finish", command=self.main_page)
        finish_button.pack(pady=10)

    def generate_qr_code(self):
        self.clear_frame()

        qr_content = f"Pembeli: {self.buyer_name.get()}\nKeranjang: {self.cart}\n"
        qr_image = qrcode.make(qr_content)
        qr_image_path = "payment_qr.png"
        qr_image.save(qr_image_path)

        # Membuat frame untuk menampung elemen dan menempatkannya di tengah
        center_frame = ctk.CTkFrame(self)  # Frame tanpa latar belakang
        center_frame.place(relx=0.5, rely=0.5, anchor='center')  # Menempatkan frame di tengah

        # Menambahkan label QR Code ke dalam frame
        qr_label = ctk.CTkLabel(center_frame, text="QR Code Pembayaran", font=("Arial", 16))
        qr_label.pack(pady=20)

        # Menambahkan gambar QR Code ke dalam frame
        qr_image = Image.open(qr_image_path)
        qr_image = qr_image.resize((200, 200))
        qr_image_tk = ImageTk.PhotoImage(qr_image)

        qr_image_label = ctk.CTkLabel(center_frame, image=qr_image_tk)
        qr_image_label.image = qr_image_tk  # Menyimpan referensi gambar
        qr_image_label.pack(pady=10)

        # Menambahkan tombol download struk ke dalam frame
        download_receipt_button = ctk.CTkButton(center_frame, text="Download Struk", command=self.download_receipt)
        download_receipt_button.pack(pady=10)

        # Menambahkan tombol finish ke dalam frame
        finish_button = ctk.CTkButton(center_frame, text="Finish", command=self.main_page)
        finish_button.pack(pady=10)

    def download_receipt(self):
        file = filedialog.asksaveasfilename(defaultextension=".txt", filetypes=[("Text Files", "*.txt")])
        if file:
            with open(file, "w") as f:
                receipt_content = f"Struk Pembelian\nPembeli: {self.buyer_name.get()}\n\n"
                total_price = 0
                for item, quantity in self.cart.items():
                    if quantity > 0:
                        price = int(item.split("Rp")[1].replace(".", "").replace(")", "").strip())
                        item_total = price * quantity
                        total_price += item_total
                        receipt_content += f"{item}: {quantity} x Rp {price:,} = Rp {item_total:,}\n"

                receipt_content += f"\nTotal Harga: Rp {total_price:,}"
                f.write(receipt_content)

    def clear_frame(self):
        for widget in self.winfo_children():
            widget.destroy()

    def clear_frame(self):
        for widget in self.winfo_children():
            if widget != self.bg_label:  # Jangan hapus label background
                widget.destroy()

    def main_page(self):
    # Reset semua variabel

        self.buyer_name.set("")  # Reset nama pembeli
        self.cart = {}  # Reset keranjang
        self.name_entered = False  # Reset status nama telah dimasukkan

        self.clear_frame()

        # Membuat frame untuk menampung elemen dan menempatkannya di tengah
        center_frame = ctk.CTkFrame(self, bg_color="transparent")
        center_frame.place(relx=0.5, rely=0.5, anchor='center')  # Menempatkan frame di tengah

        # Menambahkan label sambutan
        welcome_label = ctk.CTkLabel(center_frame, text="Selamat Datang di Ayam Unesa", font=("Arial", 20), bg_color="transparent")
        welcome_label.pack(pady=20)

        # Tombol Take Away
        takeaway_button = ctk.CTkButton(center_frame, text="Take Away", command=self.buyer_page)
        takeaway_button.pack(pady=10)

        # Tombol Dine In
        dinein_button = ctk.CTkButton(center_frame, text="Dine In", command=self.buyer_page)
        dinein_button.pack(pady=10)
if __name__ == "__main__":
    app = AyamApp()
    app.mainloop()
