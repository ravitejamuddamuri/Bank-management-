#include <iostream>
#include <fstream>
#include <vector>
#include <string>

using namespace std;

class BankAccount {
private:
    int accountNumber;
    string customerName;
    double balance;

public:
    // Create Account
    void createAccount() {
        cout << "Enter Account Number: ";
        cin >> accountNumber;
        cin.ignore();

        cout << "Enter Customer Name: ";
        getline(cin, customerName);

        cout << "Enter Initial Balance: ";
        cin >> balance;
    }

    // Deposit
    void deposit(double amount) {
        balance += amount;
    }

    // Withdraw
    bool withdraw(double amount) {
        if (amount > balance) {
            return false;
        }
        balance -= amount;
        return true;
    }

    // Display Account Details
    void display() const {
        cout << "\nAccount Number : " << accountNumber;
        cout << "\nCustomer Name  : " << customerName;
        cout << "\nBalance        : ₹" << balance << endl;
    }

    // Getters
    int getAccountNumber() const {
        return accountNumber;
    }

    double getBalance() const {
        return balance;
    }

    // File Save Format
    string toFileString() const {
        return to_string(accountNumber) + "|" +
               customerName + "|" +
               to_string(balance);
    }

    // Load From File
    void fromFileString(string line) {
        size_t pos1 = line.find("|");
        size_t pos2 = line.find("|", pos1 + 1);

        accountNumber = stoi(line.substr(0, pos1));
        customerName = line.substr(pos1 + 1,
                                   pos2 - pos1 - 1);
        balance = stod(line.substr(pos2 + 1));
    }
};

const string FILE_NAME = "accounts.txt";

// Load Accounts
vector<BankAccount> loadAccounts() {
    vector<BankAccount> accounts;
    ifstream file(FILE_NAME);

    string line;
    while (getline(file, line)) {
        BankAccount acc;
        acc.fromFileString(line);
        accounts.push_back(acc);
    }

    file.close();
    return accounts;
}

// Save Accounts
void saveAccounts(const vector<BankAccount>& accounts) {
    ofstream file(FILE_NAME);

    for (const auto& acc : accounts) {
        file << acc.toFileString() << endl;
    }

    file.close();
}

// Create New Account
void createNewAccount() {
    vector<BankAccount> accounts = loadAccounts();

    BankAccount acc;
    acc.createAccount();

    accounts.push_back(acc);
    saveAccounts(accounts);

    cout << "\nAccount created successfully!\n";
}

// Deposit Money
void depositMoney() {
    int accNo;
    double amount;

    cout << "Enter Account Number: ";
    cin >> accNo;

    vector<BankAccount> accounts = loadAccounts();

    bool found = false;

    for (auto &acc : accounts) {
        if (acc.getAccountNumber() == accNo) {
            cout << "Enter Deposit Amount: ";
            cin >> amount;

            acc.deposit(amount);
            found = true;
            break;
        }
    }

    if (found) {
        saveAccounts(accounts);
        cout << "\nDeposit Successful!\n";
    } else {
        cout << "\nAccount Not Found!\n";
    }
}

// Withdraw Money
void withdrawMoney() {
    int accNo;
    double amount;

    cout << "Enter Account Number: ";
    cin >> accNo;

    vector<BankAccount> accounts = loadAccounts();

    bool found = false;

    for (auto &acc : accounts) {
        if (acc.getAccountNumber() == accNo) {
            cout << "Enter Withdrawal Amount: ";
            cin >> amount;

            if (acc.withdraw(amount))
                cout << "\nWithdrawal Successful!\n";
            else
                cout << "\nInsufficient Balance!\n";

            found = true;
            break;
        }
    }

    if (found)
        saveAccounts(accounts);
    else
        cout << "\nAccount Not Found!\n";
}

// Balance Inquiry
void checkBalance() {
    int accNo;

    cout << "Enter Account Number: ";
    cin >> accNo;

    vector<BankAccount> accounts = loadAccounts();

    bool found = false;

    for (const auto &acc : accounts) {
        if (acc.getAccountNumber() == accNo) {
            acc.display();
            found = true;
            break;
        }
    }

    if (!found)
        cout << "\nAccount Not Found!\n";
}

// Main Function
int main() {
    int choice;

    do {
        cout << "\n========== BANK MANAGEMENT SYSTEM ==========\n";
        cout << "1. Create Account\n";
        cout << "2. Deposit Money\n";
        cout << "3. Withdraw Money\n";
        cout << "4. Check Balance\n";
        cout << "5. Exit\n";
        cout << "Enter Choice: ";
        cin >> choice;

        switch (choice) {
        case 1:
            createNewAccount();
            break;

        case 2:
            depositMoney();
            break;

        case 3:
            withdrawMoney();
            break;

        case 4:
            checkBalance();
            break;

        case 5:
            cout << "\nThank You for Using the Bank System!\n";
            break;

        default:
            cout << "\nInvalid Choice!\n";
        }

    } while (choice != 5);

    return 0;
}
