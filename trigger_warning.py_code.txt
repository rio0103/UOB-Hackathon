import os
import PyPDF2
import sqlite3
from datetime import datetime

# SQLite database setup
def setup_database(db_name="vault_management.db"):
    conn = sqlite3.connect(db_name)
    cursor = conn.cursor()
    
    # Create tables if they don't exist
    cursor.execute('''CREATE TABLE IF NOT EXISTS vaults (
                        id INTEGER PRIMARY KEY AUTOINCREMENT,
                        vault_name TEXT NOT NULL,
                        created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)''')

    cursor.execute('''CREATE TABLE IF NOT EXISTS file_batches (
                        id INTEGER PRIMARY KEY AUTOINCREMENT,
                        vault_id INTEGER,
                        file_name TEXT NOT NULL,
                        statement_type TEXT NOT NULL,
                        lines INTEGER,
                        file_size REAL, -- in KB
                        FOREIGN KEY (vault_id) REFERENCES vaults(id))''')

    cursor.execute('''CREATE TABLE IF NOT EXISTS warnings (
                        id INTEGER PRIMARY KEY AUTOINCREMENT,
                        vault_id INTEGER,
                        warning_message TEXT,
                        triggered_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
                        FOREIGN KEY (vault_id) REFERENCES vaults(id))''')

    conn.commit()
    return conn, cursor

# Count the number of lines in a PDF
def count_pdf_lines(pdf_path):
    with open(pdf_path, 'rb') as pdf_file:
        reader = PyPDF2.PdfReader(pdf_file)
        total_lines = 0
        for page in reader.pages:
            text = page.extract_text()
            total_lines += len(text.splitlines())
    return total_lines

# Calculate the size of the PDF in KB
def calculate_pdf_size(pdf_path):
    return os.path.getsize(pdf_path) / 1024.0

# Generate the vault folder name
def generate_vault_name(base_name, letter):
    return f"{base_name}{letter}.pdf"

# Insert new vault into the database and return its ID
def insert_vault(cursor, vault_name):
    cursor.execute("INSERT INTO vaults (vault_name) VALUES (?)", (vault_name,))
    return cursor.lastrowid

# Insert file batch information into the database
def insert_file_batch(cursor, vault_id, file_name, statement_type, lines, file_size):
    cursor.execute('''INSERT INTO file_batches (vault_id, file_name, statement_type, lines, file_size)
                      VALUES (?, ?, ?, ?, ?)''', (vault_id, file_name, statement_type, lines, file_size))

# Insert warning into the database
def insert_warning(cursor, vault_id, message):
    cursor.execute("INSERT INTO warnings (vault_id, warning_message) VALUES (?, ?)", (vault_id, message))

# Batch process to allocate e-statements into the vaults
def batch_process(db_conn, cursor, pdf_folder="EST_Folder", vault="Vault", max_lines_per_file=100, warning_threshold=0.2):
    statement_types = ['CASA', 'CPFIS', 'Credit_Card', 'SRS']
    
    # Ask user which folder (statement type) to perform the allocation on
    print(f"Available folders in '{pdf_folder}': {', '.join(statement_types)}")
    selected_type = input("Enter the folder you want to perform the allocation on: ").strip()
    if selected_type not in statement_types:
        print(f"Invalid folder type. Please choose from: {', '.join(statement_types)}")
        return
    
    folder_path = os.path.join(pdf_folder, selected_type)
    
    # Ask user how many files they want to allocate
    num_files = int(input(f"Enter the number of files you want to allocate for {selected_type}: "))

    # List all the PDFs in the selected folder
    pdf_files = [f for f in os.listdir(folder_path) if f.endswith('.pdf')]
    print(f"Processing {len(pdf_files)} PDFs from {folder_path}...")
    
    # Initialize variables for vaulting
    base_vault_name = f"Vault\\{selected_type}\\CSA1SPCS"
    current_file_letter = 'S'
    current_file_lines = 0
    current_file_size = 0.0

    # Insert initial vault into the database
    vault_name = generate_vault_name(base_vault_name, current_file_letter)
    vault_id = insert_vault(cursor, vault_name)

    # Process each PDF in the folder
    file_count = 1
    for i, pdf_file in enumerate(pdf_files):
        pdf_path = os.path.join(folder_path, pdf_file)
        lines_in_pdf = count_pdf_lines(pdf_path)
        size_in_pdf = calculate_pdf_size(pdf_path)

        # If not the last file and it exceeds the line limit, move to the next vault
        if file_count < num_files and current_file_lines + lines_in_pdf > max_lines_per_file:
            current_file_letter = chr(ord(current_file_letter) + 1)  # Move to next letter
            vault_name = generate_vault_name(base_vault_name, current_file_letter)
            vault_id = insert_vault(cursor, vault_name)
            current_file_lines = 0  # Reset lines for new vault
            current_file_size = 0.0  # Reset file size
            file_count += 1

            print(f"New file created: {vault_name}. Starting with 0 lines and size 0 KB.")
        
        # Update current file lines and size
        current_file_lines += lines_in_pdf
        current_file_size += size_in_pdf

        # Insert batch information into the database
        insert_file_batch(cursor, vault_id, pdf_file, selected_type, lines_in_pdf, size_in_pdf)

        print(f"PDF {pdf_file}: {lines_in_pdf} lines, Size: {size_in_pdf:.2f} KB added to file {current_file_letter}. Total lines in file: {current_file_lines}, Total size: {current_file_size:.2f} KB.")

    # For the last file, trigger warning if it exceeds the limit
    if current_file_lines > max_lines_per_file:
        exceeded_lines = current_file_lines - max_lines_per_file
        exceeded_percentage = round(exceeded_lines / max_lines_per_file * 100, 2)
        warning_message = f"Last file {current_file_letter} exceeded the limit by {exceeded_percentage}%. Allocate more files."
        insert_warning(cursor, vault_id, warning_message)
        print(f"Warning: {warning_message}")

    db_conn.commit()

if __name__ == "__main__":
    pdf_folder = "EST_Folder"
    
    # Initialize SQLite database and tables
    db_conn, cursor = setup_database()

    # Perform batch processing with SQLite integration
    batch_process(db_conn, cursor, pdf_folder)
    
    # Close the database connection after processing
    db_conn.close()
