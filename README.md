import json
import datetime

# Files to simulate database
USER_FILE = 'users.json'
TRANSACTION_FILE = 'transactions.json'

# Load data
def load_data(file):
    try:
        with open(file, 'r') as f:
            return json.load(f)
    except:
        return {}

# Save data
def save_data(file, data):
    with open(file, 'w') as f:
        json.dump(data, f, indent=4)

# Register a new user
def register():
    users = load_data(USER_FILE)
    email = input("Enter Email: ")
    if email in users:
        print("User already exists.")
        return
    name = input("Enter Name: ")
    car_number = input("Enter Car Number: ")
    password = input("Set Password: ")
    users[email] = {
        'name': name,
        'car_number': car_number,
        'password': password,
        'balance': 0
    }
    save_data(USER_FILE, users)
    print("Registered Successfully!")

# Login user
def login():
    users = load_data(USER_FILE)
    email = input("Enter Email: ").strip()
    password = input("Enter Password: ").strip()
    if email in users and users[email]['password'] == password:
        print(f"Welcome, {users[email]['name']}!")
        user_menu(email)
    else:
        print("Invalid credentials.")

# Admin login
def admin_login():
    admin_email = "admin@toll.com"
    admin_pass = "admin123"
    email = input("Enter Admin Email: ").strip()
    password = input("Enter Admin Password: ").strip()
    if email == admin_email and password == admin_pass:
        admin_menu()
    else:
        print("Invalid admin credentials.")

# Credit eWallet
def credit_wallet(email):
    users = load_data(USER_FILE)
    amount = float(input("Enter amount to add: ₹"))
    users[email]['balance'] += amount
    save_data(USER_FILE, users)
    print(f"₹{amount} added to your wallet.")

# Simulate toll payment
def pay_toll(email):
    users = load_data(USER_FILE)
    transactions = load_data(TRANSACTION_FILE)
    toll_fee = 50
    if users[email]['balance'] >= toll_fee:
        users[email]['balance'] -= toll_fee
        trans_id = f"T{len(transactions)+1}"
        transactions[trans_id] = {
            'email': email,
            'car_number': users[email]['car_number'],
            'amount': toll_fee,
            'date': str(datetime.date.today())
        }
        save_data(USER_FILE, users)
        save_data(TRANSACTION_FILE, transactions)
        print("Toll Paid. You may proceed.")
    else:
        print("Insufficient balance. Manual payment required.")

# View own transactions
def view_user_transactions(email):
    transactions = load_data(TRANSACTION_FILE)
    for tid, details in transactions.items():
        if details['email'] == email:
            print(f"{tid} | ₹{details['amount']} | {details['date']}")

# View all transactions and income (Admin)
def admin_menu():
    transactions = load_data(TRANSACTION_FILE)
    income = 0
    print("\n--- All Transactions ---")
    for tid, details in transactions.items():
        print(f"{tid} | {details['car_number']} | ₹{details['amount']} | {details['date']}")
        income += details['amount']
    print(f"\nTotal Income: ₹{income}")

# User dashboard
def user_menu(email):
    while True:
        print("\n1. Credit Wallet\n2. Pay Toll\n3. View My Transactions\n4. Logout")
        choice = input("Choose an option: ")
        if choice == '1':
            credit_wallet(email)
        elif choice == '2':
            pay_toll(email)
        elif choice == '3':
            view_user_transactions(email)
        elif choice == '4':
            break
        else:
            print("Invalid choice!")

# Main menu
def main():
    while True:
        print("\n--- Online Toll Plaza ---")
        print("1. Register\n2. Login\n3. Admin Login\n4. Exit")
        choice = input("Choose an option: ")
        if choice == '1':
            register()
        elif choice == '2':
            login()
        elif choice == '3':
            admin_login()
        elif choice == '4':
            break
        else:
            print("Invalid choice!")

# Run the program
main()
