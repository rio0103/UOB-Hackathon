import random
import os
from fpdf import FPDF
from datetime import datetime, timedelta

# Function to generate random account number
def generate_random_account_number():
    return f"{random.randint(100, 999)}-{random.randint(100, 999)}-{random.randint(1000, 9999)}-{random.randint(1, 9)}"

# Function to generate random date between two dates
def generate_random_date(start_date, end_date):
    delta = end_date - start_date
    random_days = random.randint(0, delta.days)
    return start_date + timedelta(days=random_days)

# Function to generate a single statement
def generate_statement(pdf_number, folder, est_type):
    # Create PDF instance
    pdf = FPDF()

    # Add a page
    pdf.add_page()

    # Title
    pdf.set_font('Arial', 'B', 16)
    pdf.cell(0, 10, "XXX BANK", 0, 1, 'C')
    pdf.cell(0, 10, f"{est_type} Statement of Account", 0, 1, 'C')
    pdf.ln(10)

    # Account overview heading
    pdf.set_font('Arial', 'B', 12)
    pdf.cell(0, 10, f"Account Overview as at {datetime.now().strftime('%d %b %Y')}", 0, 1)
    pdf.ln(5)

    # Generate random balance, interest, and account number
    current_balance = round(random.uniform(5000, 100000), 2)
    interest_earned = round(random.uniform(1, 50), 2)
    account_number = generate_random_account_number()

    # Deposit details
    pdf.set_font('Arial', 'B', 10)
    pdf.cell(0, 10, f"{est_type} Deposits", 0, 1)
    pdf.set_font('Arial', '', 10)
    pdf.cell(0, 10, f"Account: {account_number}", 0, 1)
    pdf.cell(0, 10, "Currency: SGD", 0, 1)
    pdf.cell(0, 10, f"Interest Earned: {interest_earned}", 0, 1)
    pdf.cell(0, 10, f"Balance: {current_balance}", 0, 1)
    pdf.ln(10)

    # Transaction table heading
    pdf.set_font('Arial', 'B', 12)
    pdf.cell(0, 10, "Account Transaction Details", 0, 1)
    pdf.ln(5)

    # Table headers
    pdf.set_font('Arial', 'B', 10)
    pdf.cell(30, 10, "Date", 1)
    pdf.cell(70, 10, "Description", 1)
    pdf.cell(30, 10, "Withdrawals(SGD)", 1)
    pdf.cell(30, 10, "Deposits(SGD)", 1)
    pdf.cell(30, 10, "Balance(SGD)", 1)
    pdf.ln()

    # Generate random transaction details
    pdf.set_font('Arial', '', 10)
    start_date = datetime(2027, 2, 1)
    end_date = datetime(2027, 2, 28)

    # Number of transactions will vary to ensure some ESTs extend to the second page
    num_transactions = random.randint(3, 20)

    balance = current_balance
    for _ in range(num_transactions):
        date = generate_random_date(start_date, end_date).strftime("%d %b")
        description = random.choice(["BALANCE B/F", "Interest Credit", "Fee Charge", "Deposit", "Withdrawal"])
        withdrawals = round(random.uniform(1, 500), 2) if "Fee" in description or "Withdrawal" in description else ""
        deposits = round(random.uniform(1, 1000), 2) if "Deposit" in description else ""
        balance = balance - float(withdrawals or 0) + float(deposits or 0)
        pdf.cell(30, 10, date, 1)
        pdf.cell(70, 10, description, 1)
        pdf.cell(30, 10, str(withdrawals), 1)
        pdf.cell(30, 10, str(deposits), 1)
        pdf.cell(30, 10, str(round(balance, 2)), 1)
        pdf.ln()

        # Add a new page if current page is about to overflow
        if pdf.get_y() > 270:
            pdf.add_page()

    # Footer for inquiries
    pdf.ln(10)
    pdf.set_font('Arial', '', 10)
    pdf.cell(0, 10, "For inquiries, contact:", 0, 1)
    pdf.cell(0, 10, "Phone: 1800 000 0000 or +65 6666 2222", 0, 1)
    pdf.cell(0, 10, "Email: support@XXX.com", 0, 1)

    # Save the file to the folder in the respective type folder
    type_folder = os.path.join(folder, est_type)  # Subfolder for the type
    if not os.path.exists(type_folder):
        os.makedirs(type_folder)

    file_path = os.path.join(type_folder, f"{est_type}_statement_{pdf_number}.pdf")
    pdf.output(file_path)

# Create a folder to store the PDFs
folder = "EST_Folder"
if not os.path.exists(folder):
    os.makedirs(folder)

# Define the types of statements
statement_types = ["CASA", "Credit_Card", "SRS", "CPFIS"]

# Generate e-statements of different types
for i in range(100):  # Adjust number of e-statements as needed
    statement_type = random.choice(statement_types)
    generate_statement(i, folder, statement_type)

print(f"Generated e-statements in folder: {folder}")

